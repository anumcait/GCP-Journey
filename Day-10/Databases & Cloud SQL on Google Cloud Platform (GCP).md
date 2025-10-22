# 🌐 Day 10 — Databases & Cloud SQL on Google Cloud Platform (GCP)

## 🎯 Overview
Today I explored how databases fit into the **DevOps world** and how Google Cloud Platform (GCP) simplifies database operations using **Cloud SQL**.

As a DevOps Engineer, understanding databases is critical — from **deployment automation**, **backup management**, **scaling**, to **monitoring**.
---

<img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/dc036174-170d-414b-93e3-bb164ab6d412" /> 

---

## 🧠 1. Understanding Databases

### 🔹 Types of Databases
| Type | Example | Description |
|------|----------|--------------|
| Relational | PostgreSQL, MySQL, Oracle | Structured, table-based with SQL |
| Non-relational | MongoDB, Cassandra, Firestore | Flexible schema, document or key-value store |
| In-memory | Redis, Memcached, ValKey | Data stored in RAM for ultra-fast access |
| Time-series | InfluxDB, Timescale DB, Prometheus | Optimized for time-stamped data |

---

## 🏗️ 2. Deployment Options
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

## 🛠️ 4. What is Cloud SQL in Google Cloud Platform (GCP)

**Cloud SQL** is a **fully managed relational database service** that supports **PostgreSQL**, **MySQL**, and **SQL Server** on Google Cloud.

Features:
- Automated backups  
- High Availability & Read Replicas  
- Auto patching & scaling  
- IAM integration  
- Secure connectivity (Private IP, Auth Proxy)

---

## 🔹 5. Step-by-Step: Create PostgreSQL Instance in Cloud SQL

### A. Preparation
1. Enable the **Cloud SQL Admin API** in your project.  
2. Choose a **region** near your app (e.g., `asia-south1`, `us-central1`).  
3. Choose **PostgreSQL version** (recommended: latest supported).  
4. Decide **vCPU**, **memory**, and **storage** requirements.

<img width="300" height="465" alt="image" src="https://github.com/user-attachments/assets/c853dd0a-2004-40f3-979c-8df0054044ad" />

<img width="300" height="526" alt="image" src="https://github.com/user-attachments/assets/1ad2051c-0728-464d-9b74-8ad1f2de0260" />

<img width="300" height="520" alt="image" src="https://github.com/user-attachments/assets/33b36cdc-27d8-48b6-b6bd-cfa6da345222" />

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

<img width="300" height="524" alt="image" src="https://github.com/user-attachments/assets/cc3026b7-3640-41e6-88fa-08a443994923" />

<img width="300" height="516" alt="image" src="https://github.com/user-attachments/assets/02aee2eb-205e-473d-aeca-de270ed39a7d" />

<img width="300" height="535" alt="image" src="https://github.com/user-attachments/assets/91af266f-26dd-4482-be43-5467195a831c" />

<img width="300" height="533" alt="image" src="https://github.com/user-attachments/assets/62c91bbf-a1c1-42eb-8e53-cb0a4d1d1d9f" />

<img width="300" height="524" alt="image" src="https://github.com/user-attachments/assets/e8a2ff72-0748-4dcd-bfa4-29ef4a1fbd56" />

<img width="300" height="533" alt="image" src="https://github.com/user-attachments/assets/8868ead7-d150-4683-9ccf-c65e0cad3f17" />

<img width="300" height="528" alt="image" src="https://github.com/user-attachments/assets/2c1eab27-e815-4c7e-b684-25c13dfc2b62" />

<img width="300" height="526" alt="image" src="https://github.com/user-attachments/assets/bba516a7-bd2c-4c8b-9a45-b17c5caae9ca" />

<img width="300" height="542" alt="image" src="https://github.com/user-attachments/assets/e489bfa0-9028-4523-b805-2ca516024b2c" />











---

### C. Create Database & Users
After the instance is ready:

- Go to **Databases → Create database** → `employee_db`  
- Go to **Users → Create user account** → `app_user` with secure password  

> Avoid using the default `postgres` user for applications.

<img width="300" height="523" alt="image" src="https://github.com/user-attachments/assets/6f665a59-ef3f-4072-b53d-cdf2769eb488" />

<img width="300" height="527" alt="image" src="https://github.com/user-attachments/assets/25461ad6-cd51-4cc8-b90b-a8c9bae61e29" />

<img width="300" height="534" alt="image" src="https://github.com/user-attachments/assets/0f533756-84ed-49c8-86bd-1c1879628256" />

<img width="300" height="524" alt="image" src="https://github.com/user-attachments/assets/d19dba38-4c8a-4385-9341-2e264531527e" />

<img width="300" height="538" alt="image" src="https://github.com/user-attachments/assets/7f3520e4-c292-44a6-9d7e-1f96272db503" />

<img width="300" height="527" alt="image" src="https://github.com/user-attachments/assets/b41b6726-1ae9-452a-900c-fe384f8a598d" />

<img width="300" height="530" alt="image" src="https://github.com/user-attachments/assets/6de5c6a5-10ca-490a-84b0-6f7d45c73b3f" />

<img width="300" height="530" alt="image" src="https://github.com/user-attachments/assets/99bcaa9a-9bd5-46e1-a00a-0977d90996eb" />

<img width="300" height="466" alt="image" src="https://github.com/user-attachments/assets/6f41016b-533e-479d-8629-a625969f55a7" />

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
---

### ♻️ 6. Backup & Restore

- Enable Automated Backups
- Run On-Demand Backup before schema changes
- Use Point-in-Time Recovery for rollback

**Example: Export backup to Cloud Storage**
```bash
gcloud sql export sql pg-instance-dev gs://my-bucket/backups/employee_db.sql.gz \
  --database=employee_db
```

**Import backup:**
```bash
gcloud sql import sql pg-instance-dev gs://my-bucket/backups/employee_db.sql.gz \
  --database=employee_db
```
---

### ⚙️ 7. Create Read Replica

To scale reads:
```bash
gcloud sql instances create pg-replica-1 \
  --master-instance-name=pg-instance-dev \
  --region=asia-south1
```
---

### 💻 8. Common gcloud Commands
```bash
# Create PostgreSQL instance
gcloud sql instances create pg-instance-dev \
  --database-version=POSTGRES_17 \
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



