# Day-20: GCP Cost Optimization for DevOps

Cost optimization is a critical responsibility for DevOps engineers working in Google Cloud Platform (GCP). Efficient resource usage ensures you reduce cloud bills while maintaining performance, reliability, and scalability.

Today we will cover **billing breakdown, cost-saving strategies, practical steps, and tools** to optimize GCP costs.

---

## 1. Understanding GCP Billing Breakdown

To optimize costs, you need to understand where your money goes. GCP billing is primarily divided into **Compute, Network, and Storage**.

### a) Compute

* **Compute Engine VMs**

  * Charged based on machine type, CPU, memory, uptime, and region.
  * Cost depends on sustained use and preemptible options.
* **Google Kubernetes Engine (GKE)**

  * Node VMs are charged like standard Compute Engine instances.
  * Additional costs for network egress, load balancers, and persistent storage.
* **Cloud Run & App Engine**

  * Charged per request, CPU, memory usage, and instance uptime.

**Tip:** Monitor VM uptime and scale down idle workloads.

### b) Network

* **Egress Traffic**: Data leaving GCP (to internet or other regions) is billed.
* **Load Balancers**: Pay for processed data volume.
* **Inter-zone or Inter-region Traffic**: Data transferred between zones/regions has a cost.

**Tip:** Minimize cross-region traffic and use same-region resources when possible.

### c) Storage

* **Persistent Disks**: Charged per GB per month.
* **Snapshots**: Backup images incur storage cost.
* **Cloud Storage**: Costs vary by storage class:

  * **Standard**: Frequently accessed data
  * **Nearline**: Accessed < 30 days
  * **Coldline**: Accessed < 90 days
  * **Archive**: Rarely accessed

**Tip:** Move infrequently used data to Coldline or Archive to save costs.

---

## 2. Tips to Reduce Cost

Here are actionable cost-saving strategies:

### a) Use Preemptible VMs

* **Definition:** Short-lived, low-cost VMs that may be terminated by GCP at any time.
* **Benefits:** Save up to 80% compared to standard VMs.
* **Use Cases:** Batch jobs, CI/CD pipelines, testing environments.
* **Create Preemptible VM Example:**

```bash
gcloud compute instances create preemptible-vm \
    --zone=us-central1-a \
    --preemptible \
    --machine-type=e2-medium
```

* **Note:** Avoid using for critical workloads due to sudden termination risk.

### b) Use Autoscaling for GKE and Cloud Run

* **GKE Cluster Autoscaler:** Automatically adjusts node count based on workloads.

  ```bash
  gcloud container clusters update my-cluster \
      --enable-autoscaling --min-nodes=1 --max-nodes=5 --zone=us-central1-a
  ```
* **Cloud Run Autoscaling:** Automatically adjusts container instances based on traffic.

  * Configure **concurrency** and **max instances** to avoid overprovisioning.

**Benefit:** Pay only for resources used.

### c) Delete Unused Disks, Snapshots, and IPs

* **Idle Resources** continue to generate costs. Identify and delete them:

```bash
gcloud compute disks list --filter="status:READY"
gcloud compute snapshots list
gcloud compute addresses list --filter="status:RESERVED"
```

* Remove unused disks, snapshots, or reserved IP addresses to reduce waste.

### d) Setup Budget Alerts and Detailed Reporting

* **Budget Alerts:** Notify you when spending crosses thresholds (50%, 80%, 100%).
* **Detailed Reporting:** GCP Billing Reports and Cost Explorer provide insights into spending patterns.
* **Steps to Create a Budget:**

  1. Go to **Billing → Budgets & alerts** in GCP Console.
  2. Click **Create Budget**.
  3. Set thresholds (50%, 80%, 100%) and notification emails.

**Benefit:** Prevent unexpected cost spikes and track usage regularly.

### e) Use Recommender and Policy Analyzer

* **Recommender:** Provides actionable recommendations:

  * Idle VM detection
  * Rightsizing VM instances
  * Committed use discounts
* **Policy Analyzer:** Ensures organizational policies are cost-efficient.
* **How to use:** Go to **Recommender in GCP Console** → Select resource → Apply recommendation.

---

## 3. Step-wise Cost Optimization Workflow

1. **Audit Current Resources**

   * Identify all compute, storage, and network resources.
   * Use **Cost Explorer** to identify top cost contributors.

2. **Implement Autoscaling**

   * Enable **GKE Cluster Autoscaler** and **Cloud Run Autoscaling**.
   * Adjust instance types and max/min limits based on workload.

3. **Switch to Preemptible/Spot Instances**

   * Use for batch workloads and non-critical tasks to reduce compute cost.

4. **Clean Up Unused Resources**

   * Identify idle VMs, unattached disks, old snapshots, and reserved IPs.
   * Delete or release them.

5. **Rightsize Instances**

   * Use **GCP Recommender** to match VM size to actual workload requirements.

6. **Optimize Storage**

   * Move infrequently accessed data to **Nearline, Coldline, or Archive** storage classes.
   * Regularly review snapshots and delete outdated ones.

7. **Setup Budgets and Alerts**

   * Create budget thresholds and alert emails.
   * Monitor daily, weekly, and monthly usage.

8. **Continuous Monitoring**

   * Use **Cloud Monitoring dashboards** to track resource usage.
   * Review monthly cost reports and adjust resources accordingly.

---

## 4. Real-World Use Cases

1. **E-commerce Platform**

   * Autoscale GKE cluster during sales events to avoid over-provisioning.
   * Preemptible VMs for batch inventory processing.

2. **CI/CD Pipelines**

   * Run builds on preemptible VMs to save up to 70% of compute costs.

3. **Data Backup & Archiving**

   * Move infrequently accessed snapshots and storage to Coldline or Archive to reduce costs.

---

## 5. Summary / Key Takeaways

* Understand your costs: **Compute, Network, Storage**.
* Preemptible VMs and autoscaling significantly reduce compute costs.
* Idle resources like disks, snapshots, and IPs are money leaks—clean them regularly.
* Use **Recommender** and **Policy Analyzer** to rightsize resources.
* **Budget alerts and detailed reports** prevent overspending.
* Cost optimization is an ongoing process—monitor, analyze, and optimize continuously.

---

## 6. Quiz / Self-Check

1. What are the three main categories of GCP billing?
2. How much can preemptible VMs save compared to standard VMs?
3. Name two tools in GCP that help identify idle or underutilized resources.
4. Why should you enable autoscaling in GKE or Cloud Run?
5. How can moving storage to Coldline or Archive save costs?

---

**Pro Tip:** Cost optimization is a balance between **efficiency and performance**. Slightly higher costs can sometimes ensure better reliability, reducing downtime or failures which are more expensive than optimized billing.

---

This concludes **Day-20: GCP Cost Optimization for DevOps**. Follow the step-wise workflow today to save costs effectively and ensure your GCP workloads remain scalable and reliable.
