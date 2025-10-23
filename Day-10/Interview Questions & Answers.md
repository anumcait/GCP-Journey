# Interview & Mock Test Questions for Databases on GCP (Day 10)  
*Focus: Databases types, deployment models, managed services (Cloud SQL), and DevOps perspective*

---

## ✅ Section A – Basic Concepts

**Q1. What is a relational database?**  
**A1.** A relational database uses structured tables with rows and columns, and relationships (via primary/foreign keys) to model data. It supports SQL, ACID transactions, and is good for structured, consistent data.

**Q2. What are non-relational (NoSQL) databases?**  
**A2.** NoSQL databases are schema-flexible, support key-value, document, wide-column or graph models, designed for scalability, high throughput, large volumes, or flexible schema (e.g., MongoDB, Cassandra, Redis).

**Q3. What is an in-memory database and when would you use it?**  
**A3.** An in-memory database stores data primarily in RAM for ultra-low latency access (e.g., Redis, Memcached). Use it for caching, session stores, real-time analytics, or high-speed lookups.

**Q4. What is a time-series database?**  
**A4.** A time-series DB is designed for timestamped data (metrics, telemetry, logs). It efficiently handles sequential data and queries over time windows (e.g., InfluxDB, TimescaleDB).

**Q5. Describe three deployment models for databases: on-premise, self-hosted, managed.**  
**A5.**  
- *On-premise:* Hardware and infrastructure owned and managed internally. Full control, high ops overhead.  
- *Self-hosted (cloud VM):* You run database on IaaS (VMs) in cloud. More flexibility, you manage OS, backups, patching.  
- *Managed service (DBaaS):* Cloud provider runs infrastructure, handles replication, backups, patching (e.g., Cloud SQL). Less ops overhead, less host-level control.

---

## 🔧 Section B – Cloud SQL & Database Management in GCP

**Q6. What is Google Cloud SQL and which engines does it support?**  
**A6.** Cloud SQL is GCP’s fully managed relational database service; it supports MySQL, PostgreSQL, and SQL Server. It handles provisioning, maintenance, backups, replication, high availability.

**Q7. How would you create a new PostgreSQL instance in Cloud SQL via CLI?**  
**A7.** Example:  
```bash
gcloud sql instances create my-pg-instance \
  --database-version=POSTGRES_15 \
  --tier=db-n1-standard-1 \
  --region=us-central1
```
Then set password, create databases/users.

**Q8. What connectivity methods are available for Cloud SQL and what is recommended for production?**
**A8.** Connectivity options: Public IP (with authorized networks + SSL), Private IP (in VPC), Cloud SQL Auth Proxy (client side). Recommended for production: Private IP + Auth Proxy (or VPC) to avoid exposing public IP.

**Q9. How do you perform import/export of data in Cloud SQL?**
**A9**.

Export: gcloud sql export sql [INSTANCE] gs://[BUCKET]/file.sql.gz --database=[DB]

Import: gcloud sql import sql [INSTANCE] gs://[BUCKET]/file.sql.gz --database=[DB]
Or use pg_dump/psql (for PostgreSQL) or mysqldump and Cloud Storage.

**Q10. What are best practices for backups and high availability in Cloud SQL?**
**A10.**

- Enable automated backups and choose a suitable retention window.
- Use instance with Regional (HA) configuration so standby is in another zone.
- Use read replicas for read-scaling and redundancy.
- Regularly test restore procedures.
- Monitor storage usage and growth so backup doesn’t fail.

## 🧠 Section C – DevOps & Operational Questions

**Q11. How does a DevOps engineer view database operations differently from just a developer?**

**A11.** DevOps focuses on automation (provisioning, configuration), monitoring/alerting, backups/DR, scaling/HA, security (IAM, network), cost management, and infrastructure as code rather than only schema and queries.

**Q12. How would you include Cloud SQL into a CI/CD pipeline?**

**A12.** Use infrastructure-as-code (e.g., Terraform or gcloud commands) to create/configure the instance. Use migration tools (Flyway, Liquibase) for schema changes. In CI/CD: run database migrations, run tests connecting to Cloud SQL stub/instance, then flag production change. Monitor rollbacks, backups executed before schema changes.

**Q13. What monitoring metrics are important for a managed database such as Cloud SQL?**

**A13.** CPU usage, memory usage, disk I/O, storage available/free, number of connections, replication lag (for HA/read replicas), slow query logs, backup success/failure, error logs, network latency.

**Q14. How do you secure a Cloud SQL instance?**

**A14.**

- Use Private IP/VPC rather than public IP.
- Use IAM roles and limited access users, avoid using root/admin for apps.
- Enable SSL/TLS connections if public IP used.
- Use network restrictions (authorized networks).
- Encrypt data at rest (managed by GCP) and in transit.
- Use automated backups and point-in-time recovery for DR.

**Q15. Describe cost-management considerations for managed databases in cloud.**

**A15.**
- Choose right tier (vCPU/RAM) based on workload.
- Enable storage auto-increase or monitor storage growth.
- Use read replicas only where justified.
- Use lower tier for dev/test and shut-down when not used.
- Tag instances for cost monitoring, use budget/alerting on cost increases.
- Archive old data or move to cheaper storage if appropriate.

---
## 🎯 Section D – Scenario / Advanced Questions

**Q16. Scenario: You have a Cloud SQL PostgreSQL instance that is hitting high CPU and query latency. What steps would you take?**

**A16.**

1. Check query performance and slow query log; identify long-running queries.
2. Evaluate indexes; add missing indexes or optimize queries.
3. Monitor CPU/memory/disk I/O; if instance tier is small, consider upgrading.
4. Check for locking/contention issues.
5. Use read replicas if read-heavy workload.
6. Check storage efficiency; vacuum/analyze (PostgreSQL) if needed.
7. Set up monitoring and alerting to catch spikes early.

**Q17. Scenario: You need to migrate an on-premises PostgreSQL database to Cloud SQL with zero downtime. What is your approach?**

**A17.**

- Use logical replication or use pg_dump/pg_restore with replication lag monitoring.
- Set up Cloud SQL PG instance; enable public/private connectivity.
- Use Data Streaming or external tool (e.g., DMS) to replicate changes.
- Switch application endpoints to the Cloud SQL instance when data sync is complete and validation done.
- Ensure backups, snapshots, failover, and testing done before cutover.

**Q18. Question: How do you decide when to use Cloud SQL vs when to self-host a database on Compute Engine?**

**A18.**
- Use Cloud SQL when you want managed operations (backups, patching, HA) and your workload fits supported engines/versions.
- Consider self-hosting when you need full host-level control, custom extensions or engine versions not supported by Cloud SQL, or special licensing requirements.
- Also, weigh cost, operational overhead, compliance/regulatory constraints.

**Q19. Question: Explain how read replicas work in Cloud SQL and when you would use them.**

**A19.**

- Read replicas are copies of the primary instance; they offload read traffic and can be promoted if needed.
- Useful for read-heavy workloads (reporting, analytics) or multi-regional distribution.
- They help with scalability and redundancy but writes must still go to primary.

**Q20. Question: What is point-in-time recovery (PITR) and how is it implemented in Cloud SQL?**

**A20.**

- PITR allows restoring the database to a specific time in the past (within retention window).
- In Cloud SQL you enable binary logging (for MySQL) or write-ahead-log archiving (for PostgreSQL) and pick a timestamp to restore to. This is critical for data recovery beyond last full backup.

---

## 🧮 Section E – Quick Mock Test (True/False & Short Answer)

**T/F: In Cloud SQL, you are responsible for patching the OS of the database instance.**

**A:** False – Cloud SQL handles OS patching in managed instances.

**T/F: Private IP connectivity in Cloud SQL means the instance has no public IP.**

**A:** True – Private IP attaches the instance into a VPC and avoids public exposure.

**Short-Answer: What command would you use to export a PostgreSQL database from Cloud SQL to a Google Cloud Storage bucket?**

**A:** gcloud sql export sql [INSTANCE] gs://[BUCKET]/file.sql.gz --database=[DB]

**Short-Answer: Name two metrics you would monitor for a Cloud SQL instance to detect performance bottlenecks.**

**A:** CPU usage, number of connections, storage free space, replication lag, disk I/O.

**T/F: Read replicas in Cloud SQL can serve write operations as well as read operations.**

**A:** False – Read replicas serve reads; writes go to the primary unless promoted.

## 📌 Key Takeaways for Interview Prep

- Be clear on database types, deployment models, and trade-offs (control vs ops overhead).
- Understand what Cloud SQL offers (managed relational DB) and its capabilities (HA, backups, import/export, connectivity).
- Think like a DevOps engineer: automation, monitoring, provisioning, cost control, security.
- Be ready for scenario questions: migrations, scaling, performance issues.
- Practice commands (gcloud sql instances create, export sql, import sql) and connectivity options (Auth Proxy, Private IP).
- Use real examples for explanation: e.g., “We had a dev instance on tier db-f1-micro then upgraded to standard because CPU was >80%”.
