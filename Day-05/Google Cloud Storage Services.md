# 🚀 GCP Day-5: Google Cloud Storage Services
## 📌 What is Google Cloud Storage (GCS)?

**`Google cloud storage`** is an object storage service for unstructured data like logs, artifacts, files, images, backups and more.

Think of it an infinite hard drive in cloud with features like
- 🌍 Global Accessibility
- 💾 Multiple Storage Classes ( Standard, Nearline, Coldline, Archive)
- 🔑 Services with IAM Roles, Policies and RBAC (Role based Access Control)
- 🕒 Lifecycle management and versioning
- 🌐 Static website hosting

## Different Storage Terms & Its Usage

- Filestore – Managed NFS file system for workloads that need shared POSIX file access.
- Local SSDs – High-performance ephemeral block storage directly attached to VM hosts.
- Bigtable – Wide-column NoSQL database for large-scale, time-series, or operational data.
- Pub/Sub – Messaging and event streaming service to connect microservices and trigger workflows.

## 🔹 Key Differences

|Service	| Type	| ScopeUsage	|Durability & Availability|	Example Equivalent|
----------| ------|--------------|------------------------|-------------------|
|Cloud Storage|	Object Storage|	Regional / Multi-regional buckets|	99.999999999% durability|	AWS S3, Azure Blob
|Filestore	|File (NFS)|	Shared file systems for VMs / GKE|Regional (zonal redundancy)	|AWS EFS, Azure Files
|Local SSD	|Block (Ephemeral)|	Ultra-fast temp storage tied to VM|	Tied to VM lifecycle	|AWS Instance Store
|Bigtable	|NoSQL DB|	Wide-column DB for analytics/monitoring|	Regional replication	|AWS DynamoDB (similar)
|Pub/Sub	|Messaging Bus|	Event-driven, async service communication|	Global service, HA managed	|AWS SQS/SNS, Kafka


## Key Use Cases
### 1. Buckets & Objects

- Buckets = containers/folders
- Objects = files inside buckets

**➡️ Example:**
gs://my-company-logs/2025/09/system.log

### 2. Storage Classes (💰 Cost vs 📦 Access)

- Standard → Hot data (daily use, websites, apps)
- Nearline → Accessed ~ once a month (e.g., monthly reports)
- Coldline → Accessed ~ once a year (e.g., yearly audit logs)
- Archive → Rarely accessed, cheapest (e.g., compliance data)

### 3. Versioning

Keeps old versions of files when overwritten/deleted.
- ✅ Good for rollback, recovery
- ⚠️ Watch out: more cost

### 4. Lifecycle Rules

- Automate data movement:
- Move old logs → Coldline
- Delete files after 365 days
- Archive backups after 30 days

### 5. IAM & Access Control

- Uniform bucket-level IAM (simple, recommended)
- Fine-grained ACLs (object-level, more complex)
- Always follow least privilege principle

### 6. Static Website Hosting

- Host static HTML/CSS/JS directly from GCS
- Combine with Cloud CDN + DNS for performance & HTTPS

## 🔧 DevOps Use Cases

**Cloud Storage (GCS)**
- Store CI/CD artifacts, Helm charts, and Terraform state files.
- Centralized logging and backup storage for Kubernetes clusters.
- Host static websites and internal documentation.
**Filestore**
- Shared configuration files for stateful workloads in Kubernetes (e.g., Jenkins home).
- Persistent shared storage for legacy apps lifted into GKE.
**Local SSD**
- High-performance cache for CI/CD build pipelines.
- Temporary data processing (e.g., ETL staging area).
**Bigtable**
- Long-term observability data (metrics, logs, traces).
- Backend for time-series monitoring tools.
**Pub/Sub**
- Event-driven DevOps: trigger pipelines on code pushes or infra changes.
- Log & metrics streaming: pipe logs from GKE/VMs to BigQuery or GCS.
- Incident automation: integrate alerts into Pub/Sub → trigger Cloud Functions or send to PagerDuty.
- Decoupling microservices: async communication between independent services.

## What we should take care as a Devops

**- Cost Management**

- Use lifecycle rules to avoid surprise bills
- Watch egress costs (cross-region downloads)
- Avoid enabling versioning blindly

**- Security**

- Never make buckets public unless required
- Use service accounts, not user credentials
- Encrypt with KMS if handling sensitive data

**- Automation (IaC)**

- Manage buckets + policies via Terraform
- Deploy static site via GitHub Actions/Cloud Build

**- Monitoring**

- Enable Cloud Logging for access attempts
- Set alerts on storage growth or failed lifecycle jobs

**- Disaster Recovery**

- Versioning + backup policies
- Multi-region for global apps
- Retention locks for compliance

## 🌍 Real-Time Examples
|Use Case|	How GCS Fits	|Example|
|--------|----------------|-------|
|App Logs|	Store daily logs, archive after 30 days|	gs://app-logs/2025/09/|
|CI/CD Artifacts	|Build outputs pushed to GCS, kept 7 days| gs://ci-artifacts/build-1234.zip|
|Static Website|	Marketing site hosted directly| www.myproduct.com → GCS + CDN|
|Database Backups|	Daily dumps → Coldline after 7 days| gs://db-backups/postgres.sql|
|Analytics Data Lake|	Raw CSVs → BigQuery reads from GCS| gs://analytics-data/2025/09/|


# 🚀 Demo: Google Cloud Storage with Compute Engine VM
This demo shows how to securely connect Google Cloud Storage (GCS) with a Compute Engine VM using only the Google Cloud Console (UI).
By completing it, you’ll practice secure, keyless access to cloud storage.

## 🔑 What You Will Learn
- Create and configure a GCS bucket.
- Use service accounts for secure IAM-based access.
- Launch a Compute Engine VM with an attached service account.
- Access GCS from inside the VM (read/write files).
- Safely clean up resources after the demo.

## 📝 Step-by-Step Summary

### 1. Create a GCS Bucket

- Go to Cloud Storage → Buckets.
- Create a bucket with a unique name and regional location.
- Upload a sample file (e.g., hello.txt).

### 2. Create a Service Account

- Go to IAM & Admin → Service Accounts.
- Create a new service account (gcs-demo-sa).
- Assign the Storage Object Admin role.

### 3. Launch a Compute Engine VM

- Go to Compute Engine → VM Instances.
- Create a VM (gcs-demo-vm) in the same region.
- Attach the service account for GCS access.

### 4. Access GCS from the VM

- SSH into the VM from the console.
- Use gcloud storage ls to list bucket files.
- Download and upload files between VM and bucket.

### 5. Cleanup

- Delete the VM.
- Delete the Bucket.
- Delete the Service Account.
(Or delete the entire project if it was created just for this demo).

---

