## 1️⃣ Basic Conceptual Questions

#### Q1: What is Google Cloud Storage (GCS)?
A: GCS is a fully-managed, highly durable, and scalable object storage service by Google Cloud. It stores unstructured data like images, videos, backups, and logs.

#### Q2: What storage classes does GCS offer?
A:
- Standard: Frequent access, low latency.
- Nearline: For data accessed <1/month.
- Coldline: For data accessed <1/year.
- Archive: Long-term archival, lowest cost.

#### Q3: What is the maximum object size in GCS?
A: 5 TB per object.

#### Q4: Difference between GCS and a file system like Google Drive?
A: GCS is designed for programmatic access at scale, high durability (99.999999999% or 11 9s), while Drive is for personal productivity.

#### Q5: How durable is GCS data?
A: 11 9’s of durability (0.000000001% chance of data loss per year) with automatic replication across multiple locations.

### 2️⃣ Security & Access Questions

#### Q6: How do you secure data in GCS?
A:

- IAM roles & permissions
- Bucket policies
- Signed URLs
- Object-level ACLs
- Encryption (at rest and in transit)

#### Q7: What are signed URLs in GCS?
A: URLs granting temporary access to objects without needing IAM permissions. Useful for sharing private objects.

#### Q8: How is data encrypted in GCS?
A:

- Server-side encryption (SSE): Google-managed keys (default) or customer-managed keys (CMEK)
- Client-side encryption before uploading

### 3️⃣ Performance & Management Questions

#### Q9: Can GCS be used for hosting static websites?
A: Yes, you can host static HTML, CSS, JS files using a bucket configured for website hosting.

#### Q10: What is object versioning in GCS?
A: It keeps a history of object changes. Previous versions can be retrieved if accidentally deleted or overwritten.

#### Q11: Difference between regional, multi-regional, and dual-region buckets?
A:

- Regional: Single region, low latency within that region.
- Multi-regional: Geo-redundant across multiple regions, better availability.
- Dual-region: Data stored in two specific regions, combining latency and redundancy benefits.

#### Q12: How do you monitor GCS?
A:

- Stackdriver/Cloud Monitoring
- Logs (Cloud Audit Logs)
- Metrics like requests, errors, storage usage

### 4️⃣ Real-Time Scenario Questions

#### Q13: How to migrate 5 TB of data to GCS from on-prem?
A:

- Option 1: gsutil rsync or gsutil cp
- Option 2: Storage Transfer Service for large or scheduled transfers
- Option 3: Transfer Appliance for massive datasets

#### Q14: How to handle objects that are rarely accessed to save cost?
A: Use lifecycle rules to move them to Nearline, Coldline, or Archive automatically.

#### Q15: How to avoid accidental data deletion?
A: Enable bucket versioning, retention policies, and Object Lock (WORM).

#### Q16: Difference between GCS and AWS S3 in real-time?
A:
- Similar object storage; minor differences in naming conventions and lifecycle management
- S3 has Intelligent-Tiering; GCS has auto-class conversion based on lifecycle rules
- Pricing models differ: GCS may be cheaper for egress to GCP services

---
