# Day-8: 🚀 GCP Flask App Deployment — VPC + NAT + Load Balancer + Cloud DNS

## 📘 Overview

This project demonstrates how to deploy a **Flask web application** on **Google Cloud Platform (GCP)** using:
- Custom **VPC network** with **App** and **NAT** subnets  
- **Cloud Router + NAT Gateway** for private instances’ outbound access  
- **Firewall rules** for 8080 and health checks  
- **Instance Template + Managed Instance Group (MIG)**  
- **HTTP Load Balancer (Global)** for high availability  
- **Cloud DNS** for mapping domain name to Load Balancer IP  

The setup ensures **secure**, **scalable**, **fault-tolerant**, and **user-friendly (domain-based)** web access.

---

## 🏗️ Architecture Diagram
```
                ┌─────────────────────────────┐
                │        Internet Users       │
                └──────────────┬──────────────┘
                               │
                               ▼
                ┌────────────────────────────┐
                │  HTTP(S) Load Balancer     │
                └──────────────┬─────────────┘
                               │
                 ┌──────────────┴──────────────┐
                 ▼                             ▼
       +---------------------+       +----------------------+
       | Managed Instance    |       | Health Checks        |
       | Group (Flask App)   |       | Port 8080            |
       +----------+----------+       +----------+-----------+
                  │                             │
                  ▼                             ▼
           +---------------+            +---------------+
           | App Subnet    |            | NAT Subnet     |
           +---------------+            +---------------+
                  │                             │
                  └──────────► NAT Gateway ◄─────┘
                               │
                               ▼
                           Internet

```

## Pre-requisites

- A Google Cloud project with billing enabled
- Basic familiarity with the GCP Console (we use **UI only**; CLI optional for verification)
- A GoDaddy domain you can modify (or be ready to purchase one)
- Region choice (example used below: `asia-south1`). Use one region consistently.

---

## Step 1 — Create the VPC and subnets (Console)

1) In the console, go to: VPC network → VPC networks → Create VPC network
   - Name: prod-vpc
   - Subnet creation mode: Custom
   - Add subnets (same Region you chose):
     - Name: subnet-public, IP range: 10.10.10.0/24
     - Name: subnet-app,    IP range: 10.10.20.0/24
   - Click Create.

2) Create the **proxy-only** subnet (required for Regional External HTTP(S) LB):
   - VPC networks → prod-vpc → Subnetworks → Create subnetwork
   - Name: subnet-proxy-only
   - Region: your region (e.g., asia-south1)
   - IP range: 10.10.30.0/24
   - Purpose: Regional Managed Proxy (proxy-only)
   - Click Create.

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/5a02b63f-562a-4a1c-89a3-5565ba46f5e4" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/5c9f4d09-e6d9-43d5-ab7e-19cd6dfb0953" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/9070152a-8e4e-4525-8a71-89bb837c798a" />

---

## Step 2 — Cloud Router and Cloud NAT (private VM egress)

We want private VMs (no external IPs) to reach the internet for apt/pip.

1) Network services → Cloud NAT → Create NAT gateway
   - Name: nat-asia-south1
   - Region: your region
   - Network: prod-vpc
   - Cloud Router: Create new → router-asia-south1
   - NAT IP addresses: Auto-allocate (okay for demo)
   - Subnetworks: Select subnet-app (and subnet-public if you like)
   - Create.
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/9607f946-85e3-406b-9b14-14e7effea7de" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/bb319fe6-7c1e-4d82-b377-4f13e9b119d5" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/ed873979-0adb-40df-9a6b-ae7a95717e84" />

---

## Step 3 — Create firewall rules for health checks and data plane

We need two allows on TCP 8080:

A) Allow **Google health-check** ranges to reach your backends:
   - VPC network → Firewall → Create firewall rule
   - Name: allow-health-checks-8080
   - Direction: Ingress
   -**Network:prod-vpc** (Make sure selected this otherwise will get error , i did not get page after troubleshooting found this and changed network)
   - Targets: All instances in the network (or use a network tag from your template)
   - Source IPv4 ranges: 35.191.0.0/16, 130.211.0.0/22
   - Protocols/ports: TCP:8080
   - Create.

B) Allow **LB data-plane** traffic from the proxy-only subnet to your backends:
   - VPC network → Firewall → Create firewall rule
   - Name: allow-proxy-only-8080
   - Direction: Ingress
   - **Network:prod-vpc**
   - Targets: All instances in the network (or tag)
   - Source IPv4 ranges: 10.10.30.0/24   (your proxy-only subnet CIDR)
   - Protocols/ports: TCP:8080
   - Create.
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/29dbd7ea-608a-420a-84d4-4c05e2601c3f" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/e91a2ac5-004c-448a-bc6a-349ec5f2e76f" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/65fea79a-53e4-4295-82e0-6d979594040c" />

(**This is where i selected default and after troubleshooting changed**)

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/6159d7c6-ec02-4314-b971-7e9b9c490076" />


---

## Step 4 — Create the Instance Template (Flask app via startup script)

We will install Python3, Flask, and Gunicorn, and run on port 8080 with `/health`.

In the Instance Template UI, under “Automation → Startup script”, paste the script below.

```
#!/bin/bash
set -e

# Update & install prerequisites
apt-get update -y
apt-get install -y python3-pip python3-venv

# Create an app user if not present
id -u appuser &>/dev/null || useradd -m -s /bin/bash appuser

# Switch to appuser and set up a virtual environment
sudo -u appuser bash <<'EOF'
cd ~
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install flask gunicorn

# Create Flask app
cat > app.py <<PY
from flask import Flask
app = Flask(__name__)

@app.route("/")
def index():
    return "Hello from Flask on GCP (private subnet)!"

@app.route("/health")
def health():
    return "ok", 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
PY
EOF

# Create systemd service using venv's Gunicorn
cat >/etc/systemd/system/flask.service <<'UNIT'
[Unit]
Description=Flask via Gunicorn in venv
After=network.target

[Service]
User=appuser
WorkingDirectory=/home/appuser
ExecStart=/home/appuser/venv/bin/gunicorn -w 2 -b 0.0.0.0:8080 app:app
Restart=always

[Install]
WantedBy=multi-user.target
UNIT

# Enable and start service
systemctl daemon-reload
systemctl enable --now flask.service
```

Create the template:

1) Compute Engine → Instance templates → Create instance template
   - Name: tmpl-flask-app
   - Machine type: e2-micro or e2-small (demo)
   - Boot disk: Debian/Ubuntu (any recent LTS)
   - Networking:
     - Network: prod-vpc
     - Subnet: subnet-app
     - External IP: None (keep private)
   - Firewall section: leave unchecked (we use explicit rules created earlier)
   - Automation: paste the startup script
   - Create.
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/8b896ba0-455d-4d5f-a969-6ad4e9bc06fb" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/9a0fc1b7-5796-4634-8c76-361561550082" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/b5f6c873-7b54-4258-89fc-4a47079abe93" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/8eba712a-6a07-41e8-aa2e-5d77e31cb70f" />

---

## Step 5 — Create the Managed Instance Group (MIG)

1) Compute Engine → Instance groups → Create instance group
   - Name: mig-flask-app
   - Location: Single zone (e.g., asia-south1-a)
   - Instance template: tmpl-flask-app
   - Autoscaling: Min 2, Max 4 (demo)
   - Create.


> Verification (optional, via a bastion in subnet-public): you can SSH to a bastion with an external IP, then SSH to private instances using internal IP, and curl http://<vm-internal-ip>:8080/health

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/a5124601-dcde-47d5-87f6-18b1527764a5" />
---

## Step 6 — Create the Regional External HTTP(S) Load Balancer

1) Network services → Load balancing → Create load balancer
   - Choose: Application Load Balancer
   - From Internet to my VMs or serverless services → Start configuration
   - Load balancer scope: Regional
   - Region: your chosen region
   - Continue.

2) Frontend (HTTP for now)
   - Protocol: HTTP
   - Port: 80
   - IP address: Create or select a new External IPv4 (e.g., flask-ip)
   - VPC network for forwarding rule: prod-vpc
   - Save.

3) Backend
   - Backend type: Instance group
   - Instance group: mig-flask-app
   - Port: 8080
   - Health check: Create new → HTTP on port 8080 → Request path /health
   - Save.

4) Routing
   - Default URL map → points to the backend above

5) Proxy-only subnet
   - Ensure your Regional LB references your proxy-only subnet in the same region/VPC (we created it in Step 1).

6) Review & Create
   - After provisioning, open the frontend IP in a browser:
     - You should see: Hello from Flask on GCP (private subnet)!

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/12d94a5c-bc4a-45c8-8501-80f92a95ea41" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/f2305134-acee-4d8f-9345-7559f8b8d1e2" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/57255774-038b-4a0a-ba73-f54eb06c3505" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/96aa9c1e-3d2a-4303-8007-425d7ceeb681" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/280fe438-fbd7-4d19-8b44-5aa74ffecff5" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/003ade4c-6e30-4b48-b53b-f58e4e8eb8e9" />







---

## Step 7 — Domain setup (GoDaddy high-level) + Cloud DNS records (optional)

High-level in GoDaddy:
- Buy or use an existing domain (e.g., example.in)
- In the domain’s DNS/Nameservers panel, choose “Custom nameservers”
- You will set these to the 4 nameservers that Google gives you in Cloud DNS (next steps)

Create a Cloud DNS public zone:
1) Network services → Cloud DNS → Create zone
   - Zone type: Public
   - Zone name: example-in-zone (any friendly name)
   - DNS name: example.in.
   - Create.
   - Copy the 4 nameservers (ns-cloud-*.googledomains.com).

Add records in Cloud DNS (after the LB is ready and has an external IP):
1) Add an A record at the apex:
   - Add standard → Type: A
   - Name: (leave blank or use @)
   - TTL: 300
   - IPv4 address: <your LB external IPv4>
   - Save.

2) Optional CNAME for www:
   - Type: CNAME
   - Name: www
   - Canonical name: example.in.
   - TTL: 300
   - Save.

Delegate the domain to Google in GoDaddy:
- In GoDaddy → Nameservers → set to the 4 Cloud DNS nameservers you copied
- Save. Propagation can take some time (usually minutes, sometimes longer)

---

## Step 8 — Validate

By IP (before DNS):
- Open http://<LB_IP>/
- Open http://<LB_IP>/health  → should return ok

By domain (after delegation + A record):
- Open http://example.in/
- Open http://example.in/health  → should return ok

Optional terminal checks (any machine with dig/curl):
- dig A example.in +short
- dig NS example.in +short
- curl -i http://example.in/
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/2a72ab2c-d661-45b8-9daf-fdd8d81da465" />
<img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/2ca440d6-bce2-48a5-b912-445cc902daa8" />
<img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/289be31c-48d2-4ed1-8c32-92b75b91471b" />




---
