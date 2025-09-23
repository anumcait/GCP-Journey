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

🔹 Key Differences

Service	Type	Scope / Usage	Durability & Availability	Example Equivalent
-------|----|--------------|

Cloud Storage	Object Storage	Regional / Multi-regional buckets	99.999999999% durability	AWS S3, Azure Blob
Filestore	File (NFS)	Shared file systems for VMs / GKE	Regional (zonal redundancy)	AWS EFS, Azure Files
Local SSD	Block (Ephemeral)	Ultra-fast temp storage tied to VM	Tied to VM lifecycle	AWS Instance Store
Bigtable	NoSQL DB	Wide-column DB for analytics/monitoring	Regional replication	AWS DynamoDB (similar)
Pub/Sub	Messaging Bus	Event-driven, async service communication	Global service, HA managed	AWS SQS/SNS, Kafka



## Key Concepts
1. Buckets & Objects
2. Storage Classes (Cost vs Access)
3. Versioning
4. Lifecycle Rules
5. IAM & Access control
6. Static Web Hosting

