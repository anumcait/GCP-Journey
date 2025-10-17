# 🌐 GCP Load Balancer with Managed Instance Group — High Availability & Auto Scaling Demo

“I implemented a high availability and auto-scaling demo on GCP using Managed Instance Groups and Application Load Balancer.

I started by creating an Instance Template that defined my web server configuration, including startup scripts and NGINX setup. Using this template, I deployed a Managed Instance Group (MIG) configured for autoscaling with a minimum of 3 and a maximum of 10 instances, and enabled multi-zone (regional) deployment to ensure high availability across zones.

Next, I configured a GCP HTTP Load Balancer with proper frontend and backend services and added a health check to continuously monitor instance health. Initially, the load balancer was not responding externally; after troubleshooting, I discovered that I hadn’t configured a firewall rule to allow HTTP traffic on port 80.

Once I created the firewall rule, the load balancer began serving traffic successfully. Every page refresh showed different instance IDs, confirming that load distribution was happening across multiple backend instances.

This exercise demonstrated my understanding of autoscaling, high availability, load balancing, and troubleshooting connectivity issues in GCP environments.”

---

## Here I have covered all the steps with screenshots

## 🏗️ Overview

This project demonstrates **highly available and auto-scaled web application architecture** using Google Cloud Platform (GCP).

It includes the configuration of:

- **Instance Template**
- - **Application Load Balancer**
- **HTTP(S) (Application) Load Balancer**
- **Autoscaling (Min: 3, Max: 10)**
- **Multi-Zone High Availability**
- **Firewall Rules & Health Checks**

When the setup was completed correctly, refreshing the load balancer’s public IP showed responses from **different instance IDs**, proving **load distribution and instance rotation**.

---

## ⚙️ Architecture
1. **Instance Template** – Defines the VM configuration (machine type, disk image, startup script, etc.)
2. **Managed Instance Group (MIG)** – Uses the template to create and manage multiple instances with:
   - Auto-scaling (min: 3, max: 10)
   - Multi-zone deployment for high availability
3. **Application Load Balancer** – Distributes traffic across instances and performs health checks.
4. **Firewall Rule** – Allows HTTP (port 80) access to instances.

### 🧩 Architecture Diagram
```
          ┌──────────────────────────────────────────┐
          │          GCP HTTP Load Balancer          │
          │  (Frontend IP: Global, HTTPS/HTTP Rule)  │
          └──────────────────────────────────────────┘
                              │
                              ▼
               ┌────────────────────────────────┐
               │  Backend Service (Health Check) │
               └────────────────────────────────┘
                              │
                              ▼
          ┌───────────────────────────────┐
          │ Managed Instance Group (MIG)  │
          │ - Multi-Zone (HA)             │
          │ - Autoscaling (3–10)          │
          │ - Instance Template           │
          └───────────────────────────────┘
                     │       │       │
                     ▼       ▼       ▼
           [VM1]     [VM2]     [VM3]   ... up to 10
            │
        Apache/NGINX on port 80
  ```      
---

## ⚙️ Step-by-Step Implementation

### 1️⃣ Create a Project

Go to GCP Console → Create a new project (e.g., gcp-loadbalancer-demo).

### 2️⃣ Create an Instance Template

- Navigate to Compute Engine → Instance templates → Create template
- Configure:
   - Machine Type: e2-micro (for demo)
   - Boot Disk: Debian/Ubuntu
   - Startup Script:
```
#!/bin/bash
apt update
apt install -y apache2
echo "Hello from $(hostname)" > /var/www/html/index.html
systemctl restart apache2

```
   - Expose port 80
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/9063f97d-03a1-401f-bbe1-66de86b62581" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/e74bbd6c-e523-415b-ab82-0d2409adc5f9" />

---

**Through CLI**

```bash
gcloud compute instance-templates create web-template \
  --machine-type=e2-medium \
  --image-family=debian-11 \
  --image-project=debian-cloud \
  --tags=http-server \
  --metadata=startup-script='#! /bin/bash
    apt update
    apt install -y apache2
    echo "<h1>Instance ID: $(hostname)</h1>" > /var/www/html/index.html
    systemctl enable apache2
    systemctl start apache2'
```


### 3️⃣ Create a Managed Instance Group (MIG)

- Choose Regional (Multi-Zone) for high availability
- Select the instance template created above
- Configure:
   - Autoscaling: Min 3, Max 10
   - Health Check: TCP/HTTP on port 80
- Deploy the group
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/9096fa30-8888-463b-a788-c20b3fa2bca4" />
<img width="1050" height="521" alt="image" src="https://github.com/user-attachments/assets/ed58db1d-2019-44cf-a141-b0843b278e0d" />

**Through CLI**
```
gcloud compute instance-groups managed create web-mig \
  --base-instance-name=web \
  --template=web-template \
  --size=3 \
  --zones=us-central1-a,us-central1-b
```
**Enable autoscaling:**
```
gcloud compute instance-groups managed set-autoscaling web-mig \
  --min-num-replicas=3 \
  --max-num-replicas=10 \
  --target-cpu-utilization=0.6
```

### 4️⃣ Create Firewall Rule

<img width="1050" height="528" alt="image" src="https://github.com/user-attachments/assets/efc5be77-26ac-489d-823a-f0b69c696ade" />

**Through CLI**

```
gcloud compute firewall-rules create allow-http \
  --allow tcp:80 \
  --target-tags=http-server \
  --description="Allow incoming HTTP traffic"
```
### 5. Create the Load Balancer

- Go to Network Services → Load balancing
- Choose HTTP(S) Load Balancer → From Internet to my VMs
- Backend Service:
   - Connect to your MIG
   - Add health check (HTTP, port 80)
- Frontend Configuration:
   - Assign a new public IP

Protocol: HTTP (or HTTPS if certs configured)

- Create Backend Service linked to the MIG
- Add Health Check (HTTP on port 80)
- Create Frontend Configuration with external IP (HTTP)
- Review and deploy

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/bba26027-5a84-47e2-9a62-d1e7742c022f" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/6545a41b-7d24-4447-b30e-167de47ca106" />

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/e590a57c-e5ee-4cb3-8488-2076d41c70ca" />
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/a7589f25-ed7c-467d-be7e-30e1bd664289" />

### 🧩 Verification

- Initially, browser access failed (firewall missing)
- After firewall rule → Load balancer IP showed response:
- Access the Load Balancer IP in a browser
- Each refresh should show a different instance ID, indicating load balancing across instances.
- If not accessible, check firewall rules or backend health.
```
Hello from gcp-instance-1
Hello from gcp-instance-3
Hello from gcp-instance-5
```
on refresh (round-robin load distribution).

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/87c489d9-a505-455a-9471-6ac96f099fad" />

### 🧠 Key Learnings

- Instance Template ensures consistent VM configuration.
- Managed Instance Group (MIG) handles auto-scaling and self-healing.
- Load Balancer distributes traffic for high availability.
- Firewall Rules are critical for external access.
- Health Checks maintain service reliability.

### 📷 Output

<img width="700" height="200" alt="image" src="https://github.com/user-attachments/assets/b42f40db-4007-4c6d-a16c-aeeb58e08bac" />
<img width="700" height="200" alt="image" src="https://github.com/user-attachments/assets/369f468f-ef8b-4cd2-b85c-d1f3365ddaee" />
<img width="700" height="200" alt="image" src="https://github.com/user-attachments/assets/e261c7f9-f31b-48f6-9275-756022a72954" />

### From Console
<img width="700" height="200" alt="image" src="https://github.com/user-attachments/assets/ddcf8abe-e772-4e4c-873f-12ced578e143" />



