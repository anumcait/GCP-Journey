# 🚀 Compute Engine in GCP (VMs, SSH, Images, Firewall)

## 🎯 Goal
To learn how to provision and manage virtual machines (VMs) in GCP, secure them, automate provisioning with startup scripts, and create reusable custom images — from a **DevOps perspective**.

---

## 🧱 What is Compute Engine?
Compute Engine is Google Cloud’s **Infrastructure as a Service (IaaS)** offering that provides on-demand virtual machines.

- Fully customizable (CPU, RAM, OS, networking)
- Supports startup automation
- Enables persistent disks and custom images
- Forms the foundation for many DevOps workflows

---

## ⚙️ Key Concepts

| Concept        | Description |
|----------------|-------------|
| **Instance**   | A VM (virtual server) running in a GCP zone |
| **Zone**       | A single data center within a region |
| **Machine Type** | CPU + RAM configuration (e.g., `e2-micro`) |
| **Boot Disk**  | Persistent disk with the OS |
| **Startup Script** | Script that runs automatically when the VM boots |
| **Firewall Rule** | Network policy to allow/deny traffic |

---

## 🛠️ Hands-On Practice

### Through Console

- Create New Project if necessary

- Create IAM Roles by clicking grant access to give permission to others to create compute engine

- Search Access -> Compute Admin

- Now the granted user can create Compute engine by logging into his own account

- Here also need to enable Compute engine API

- After that, create instance with all requirements

---

### 1️⃣ Launch a VM and Connect via SSH
```bash
gcloud compute instances create devops-vm \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=ubuntu-2004-lts \
  --image-project=ubuntu-os-cloud \
  --tags=http-server

gcloud compute ssh devops-vm --zone=us-central1-a
```
### 2️⃣ Install NGINX Manually
```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```
### 3️⃣ Automate NGINX Installation via Startup Script
```bash
gcloud compute instances create nginx-vm \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=ubuntu-2004-lts \
  --image-project=ubuntu-os-cloud \
  --metadata startup-script='#! /bin/bash
apt update
apt install -y nginx
systemctl enable nginx
systemctl start nginx' \
  --tags=http-server
```

### 4️⃣ Enable Firewall Rule for HTTP Access
```bash
gcloud compute firewall-rules create allow-http \
  --allow tcp:80 \
  --target-tags=http-server \
  --description="Allow HTTP traffic" \
  --direction=INGRESS \
  --priority=1000 \
  --network=default
```

### 5️⃣ Create a Custom Image
```bash
gcloud compute instances stop nginx-vm --zone=us-central1-a

gcloud compute images create nginx-custom-image \
  --source-disk=nginx-vm \
  --source-disk-zone=us-central1-a

gcloud compute instances create nginx-from-image \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image=nginx-custom-image \
  --tags=http-server
```

## 🧠 DevOps Real-World Use Case

- Automate web server installation with startup scripts

- Open firewall rules for public access

- Standardize deployments using custom images

- Integrate these images in CI/CD pipelines for repeatable environments

## ✅ Key Takeaways

- Compute Engine = IaaS for running VMs in GCP

- Startup scripts turn manual setup into automation

- Firewall rules are critical for controlled access

- Custom images ensure consistency across environments

- As DevOps engineers, always automate VM creation (CLI, Terraform, Ansible, etc.)

##
