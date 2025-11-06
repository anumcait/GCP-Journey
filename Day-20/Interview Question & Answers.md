# 🧠 Day-20: GCP Cost Optimization for DevOps – Question & Answers

## 🧩 Section 1: Understanding Billing Breakdown

### **Q1. What are the three main cost categories in GCP billing?**

**A:**

1. **Compute** – Charges for VM instances, GKE nodes, Cloud Run, and App Engine usage.
2. **Network** – Charges for data egress, load balancers, and inter-zone/region transfers.
3. **Storage** – Charges for Persistent Disks, Cloud Storage, and Snapshots.

---

### **Q2. How does Compute Engine pricing work?**

**A:**
Compute Engine charges depend on:

* Machine type (CPU and memory)
* Region and zone
* Uptime (per-second billing)
* Discounts (sustained use, committed use, preemptible VMs)

---

### **Q3. What factors contribute to GCP network costs?**

**A:**

* Data transfer out of GCP (egress)
* Load balancer data processing
* Cross-zone or cross-region data movement

---

### **Q4. What are the GCP Cloud Storage pricing tiers?**

**A:**

| Storage Class | Use Case                 | Cost   |
| ------------- | ------------------------ | ------ |
| Standard      | Frequently accessed data | High   |
| Nearline      | Accessed < 30 days       | Medium |
| Coldline      | Accessed < 90 days       | Low    |
| Archive       | Rarely accessed          | Lowest |

---

## ⚙️ Section 2: Cost Optimization Techniques

### **Q5. What are Preemptible VMs?**

**A:**
Preemptible VMs are **short-lived, low-cost virtual machines** that can be terminated by Google anytime.
They are ideal for **batch processing, CI/CD pipelines, and test environments**, offering up to **80% cost savings** compared to regular VMs.

---

### **Q6. When should you avoid using Preemptible VMs?**

**A:**
Avoid for:

* Mission-critical workloads
* Long-running services
* Stateful applications that require consistent uptime

---

### **Q7. How does autoscaling help in cost optimization?**

**A:**
Autoscaling dynamically adjusts resources based on demand, ensuring:

* No over-provisioning
* Lower idle resource cost
* Automatic scale-up/down behavior in GKE and Cloud Run

---

### **Q8. What command enables autoscaling for GKE?**

**A:**

```bash
gcloud container clusters update my-cluster \
    --enable-autoscaling --min-nodes=1 --max-nodes=5 --zone=us-central1-a
```

---

### **Q9. How do you identify and clean up unused resources?**

**A:**
Run these commands:

```bash
gcloud compute disks list --filter="status:READY"
gcloud compute snapshots list
gcloud compute addresses list --filter="status:RESERVED"
```

Then delete unused disks, snapshots, or IPs that are not attached to any resource.

---

### **Q10. What is the benefit of deleting unused resources?**

**A:**
Unused Persistent Disks, Snapshots, and Reserved IPs **continue to generate charges**, even when idle. Deleting them directly reduces waste and saves cost.

---

## 💰 Section 3: Budgeting and Monitoring

### **Q11. What are Budget Alerts in GCP?**

**A:**
Budget Alerts notify you when your spending crosses specific thresholds (like 50%, 80%, or 100% of the defined budget).

**Setup Steps:**

1. Navigate to **Billing → Budgets & Alerts**.
2. Click **Create Budget**.
3. Define amount, thresholds, and email recipients.

---

### **Q12. What are the benefits of Budget Alerts?**

**A:**

* Prevent unexpected cost spikes
* Improve financial visibility
* Enable proactive cost control

---

### **Q13. What tools can you use for detailed cost reports?**

**A:**

* **GCP Billing Reports**
* **Cost Explorer**
* **BigQuery Billing Export** for advanced analytics

---

## 🧮 Section 4: Recommender & Policy Analyzer

### **Q14. What is the GCP Recommender service?**

**A:**
A built-in AI-driven tool that provides optimization insights like:

* Rightsizing VMs
* Identifying idle resources
* Suggesting committed use discounts

---

### **Q15. What is Policy Analyzer used for?**

**A:**
Policy Analyzer audits IAM and organizational policies to ensure:

* No excessive permissions
* Compliance with cost-efficient configurations

---

### **Q16. Where can you access Recommender and Policy Analyzer?**

**A:**

* **GCP Console → Recommender**
* **GCP Console → Policy Analyzer**

---

## 🔄 Section 5: Step-by-Step Optimization Workflow

### **Q17. What is the step-wise workflow to optimize GCP costs?**

**A:**

1. **Audit** current resources.
2. **Implement autoscaling** in GKE & Cloud Run.
3. **Switch to preemptible VMs** for non-critical workloads.
4. **Clean up idle resources** (disks, IPs, snapshots).
5. **Rightsize instances** using Recommender.
6. **Optimize storage classes** for data lifecycle.
7. **Set budget alerts** for proactive tracking.
8. **Monitor continuously** using Cloud Monitoring dashboards.

---

## 🌍 Section 6: Real-World Use Cases

### **Q18. How does an e-commerce company use GCP cost optimization?**

**A:**

* Autoscaling GKE during traffic peaks (e.g., festive sales).
* Preemptible VMs handle background batch jobs.
* Data archives moved to Coldline to reduce costs.

---

### **Q19. How can CI/CD pipelines be optimized?**

**A:**

* Use preemptible VMs for build and test stages.
* Clean up old build artifacts and logs automatically.
* Use autoscaling runners for on-demand build capacity.

---

### **Q20. How can backups and snapshots be optimized?**

**A:**

* Retain only recent snapshots.
* Move older backups to **Coldline** or **Archive** storage.
* Schedule automatic cleanup of old data.

---

## 📘 Section 7: Key Takeaways

### **Q21. What are the most impactful actions for GCP cost optimization?**

**A:**
✅ Use Preemptible VMs for cost savings
✅ Enable autoscaling for GKE and Cloud Run
✅ Delete unused resources regularly
✅ Set up budget alerts for monitoring
✅ Leverage Recommender for rightsizing

---

### **Q22. What mindset should DevOps engineers have regarding cost?**

**A:**
Cost optimization is **not a one-time task** — it’s a **continuous improvement process**.
Efficient resource usage reflects good DevOps culture and financial responsibility.

---

## 🧾 Section 8: Quick Recap Quiz

| # | Question                                    | Answer                           |
| - | ------------------------------------------- | -------------------------------- |
| 1 | What are the three main billing categories? | Compute, Network, Storage        |
| 2 | How much cheaper are Preemptible VMs?       | Up to 80%                        |
| 3 | What tools help identify idle resources?    | Recommender & Policy Analyzer    |
| 4 | What does autoscaling help prevent?         | Over-provisioning and idle costs |
| 5 | What are Budget Alerts for?                 | Monitoring and controlling spend |

---

## 💬 Final Thoughts

Cost optimization is the **bridge between DevOps efficiency and business sustainability**.
By adopting the right strategies — automation, cleanup, rightsizing, and budgeting — you ensure your GCP environment stays performant **and cost-effective**.

> “Optimize continuously. Every resource saved is performance gained.”

---
