# 🌐 Day 10 — Databases & Cloud SQL on Google Cloud Platform (GCP)

## 🎯 Overview
Today I explored how databases fit into the **DevOps world** and how Google Cloud Platform (GCP) simplifies database operations using **Cloud SQL**.

As a DevOps Engineer, understanding databases is critical — from **deployment automation**, **backup management**, **scaling**, to **monitoring**.

---

## 🧠 Understanding Databases

### 🔹 Types of Databases
| Type | Example | Description |
|------|----------|--------------|
| Relational | PostgreSQL, MySQL | Structured, table-based with SQL |
| Non-relational | MongoDB, Firestore | Flexible schema, document or key-value store |
| In-memory | Redis, Memcached | Data stored in RAM for ultra-fast access |
| Time-series | InfluxDB, Prometheus | Optimized for time-stamped data |

---

## 🏗️ Deployment Options
| Mode | Description | Example |
|------|--------------|----------|
| On-Premise | Installed locally in servers | PostgreSQL on Linux VM |
| Self-Hosted | On VMs or Kubernetes | PostgreSQL in Compute Engine or GKE |
| Managed | Fully handled by provider | Cloud SQL for PostgreSQL |

---

## 🧩 3. Why DevOps Engineers Work with Databases

- Automate **provisioning**, **backups**, and **monitoring**
- Manage **infrastructure as code (IaC)** using Terraform or gcloud CLI
- Handle **failover**, **HA**, **replication**
- Integrate databases into **CI/CD pipelines**
- Ensure **security**, **IAM**, and **compliance**

---

## 🛠️ 4. What is Cloud SQL (PostgreSQL)

**Cloud SQL** is a **fully managed relational database service** that supports **PostgreSQL**, **MySQL**, and **SQL Server** on Google Cloud.

Features:
- Automated backups  
- High Availability & Read Replicas  
- Auto patching & scaling  
- IAM integration  
- Secure connectivity (Private IP, Auth Proxy)

---

## 🪜 5. Step-by-Step: Create PostgreSQL Instance in Cloud SQL

### A. Preparation
1. Enable the **Cloud SQL Admin API** in your project.  
2. Choose a **region** near your app (e.g., `asia-south1`, `us-central1`).  
3. Choose **PostgreSQL version** (recommended: latest supported).  
4. Decide **vCPU**, **memory**, and **storage** requirements.

---

### B. Create PostgreSQL Instance (via Console)
1. Go to **Google Cloud Console → SQL → Create Instance**  
2. Select **PostgreSQL**  
3. Set:  
   - Instance ID (e.g., `pg-instance-dev`)  
   - Password for the default user (`postgres`)  
   - Machine type & storage  
4. Enable:  
   - **High Availability (Regional)**  
   - **Automated backups** and **point-in-time recovery**  
   - **Private IP** connectivity (recommended)  
5. Click **Create Instance**

---

### C. Create Database & Users
After the instance is ready:

- Go to **Databases → Create database** → `employee_db`  
- Go to **Users → Create user account** → `app_user` with secure password  

> Avoid using the default `postgres` user for applications.

---

### D. Connect to Cloud SQL (PostgreSQL)

#### Option 1 — Cloud Shell:
```bash
gcloud sql connect pg-instance-dev --user=postgres
```
#### Option 2 — Local with Cloud SQL Auth Proxy (recommended):
```bash
./cloud-sql-proxy myproject:asia-south1:pg-instance-dev &
psql "host=127.0.0.1 port=5432 user=postgres dbname=postgres password=YourPassword"
```
#### Option 3 — Private IP from Compute Engine / Cloud Run:

- Ensure both are in the same VPC & region
- Use Private IP in connection string:
```bash
host=10.12.0.3 dbname=employee_db user=app_user password=xxxx sslmode=require
```

### 💻 8. Common gcloud Commands
```bash
# Create PostgreSQL instance
gcloud sql instances create pg-instance-dev \
  --database-version=POSTGRES_15 \
  --tier=db-custom-1-3840 \
  --region=asia-south1

# Set postgres user password
gcloud sql users set-password postgres \
  --instance=pg-instance-dev \
  --password='Strong@123'

# Create database
gcloud sql databases create employee_db --instance=pg-instance-dev

# Create app user
gcloud sql users create app_user \
  --instance=pg-instance-dev \
  --password='App@123'
```



