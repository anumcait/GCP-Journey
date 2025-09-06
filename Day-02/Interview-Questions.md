## 🎯 Interview Questions with Answers

### 1. Explain the GCP resource hierarchy. How does it compare with AWS/Azure/OCI?
**Answer:**  
- In **GCP**, hierarchy is → Organization → Folders → Projects → Resources.  
- In **AWS**, it is → Organizations → Accounts → OUs → Resources.  
- In **Azure**, it is → Management Groups → Subscriptions → Resource Groups → Resources.  
- In **OCI**, it is → Tenancy → Compartments → Resources.  
This hierarchy ensures **clear governance, isolation, and billing transparency** across all cloud providers.

---

### 2. Why is linking billing accounts at the project level important?
**Answer:**  
Because in GCP, **billing is tied to projects**. Every resource (VM, bucket, database, etc.) is created under a project, so costs must be tracked at the project level. Linking billing accounts ensures:  
- **Centralized payment management**  
- **Clear cost allocation per project/team**  
- **Better budget control**  

---

### 3. What are budgets in GCP billing and how do they help?
**Answer:**  
Budgets allow you to set thresholds (e.g., 50%, 80%, 100%) for expected costs. When usage crosses these thresholds, **alerts are triggered** via email, Pub/Sub, or Cloud Monitoring.  
This helps organizations:  
- Avoid surprise bills  
- Take corrective action early  
- Maintain financial discipline in cloud usage  

---

### 4. How do you implement cost optimization in GCP projects?
**Answer:**  
- Right-size resources (avoid oversized VMs/databases).  
- Use **preemptible VMs** for batch/fault-tolerant workloads.  
- Take advantage of **committed use** and **sustained use discounts**.  
- Automate shutdown/startup for dev/test environments.  
- Monitor idle resources and decommission unused ones.  

---

### 5. Can a project be linked to multiple billing accounts?
**Answer:**  
No. A project can only be linked to **one billing account at a time**, but a billing account can be linked to **multiple projects**.  

---

### 6. Difference between Committed Use Discounts (CUDs) and Sustained Use Discounts (SUDs) in GCP?
**Answer:**  
- **CUDs** → Up to 70% discount in exchange for a **1–3 year usage commitment** on resources.  
- **SUDs** → Automatic discounts (up to 30%) applied when you use eligible compute services for a **large portion of the billing month**.  

---

### 7. How do billing alerts integrate with Cloud Monitoring and Pub/Sub?
**Answer:**  
- Budgets are configured with thresholds.  
- When thresholds are crossed, alerts are triggered via **Cloud Monitoring**.  
- Pub/Sub can push these alerts to downstream systems like Slack, email, or custom automation scripts.  
This enables **real-time cost visibility and automated responses**.  

---

### 8. Real-world: If your team exceeds the budget, what immediate steps will you take?
**Answer:**  
- Review the billing reports to identify which project/resource is driving cost.  
- Stop or resize non-critical resources.  
- Inform stakeholders about the budget overrun.  
- Set stricter monitoring/alerts for future.  
- Long term: Apply CUDs/SUDs and automate idle resource management.  

---
