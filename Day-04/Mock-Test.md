## 📝 Mock Test: GCP Compute Engine
### Part A – Multiple Choice Questions (MCQs)

**Q1. Which of the following best describes Compute Engine in GCP?**
A) PaaS offering for running managed containers
B) IaaS offering to provision and run virtual machines
C) SaaS offering for hosting applications
D) Serverless compute platform

✅ Answer:** B) IaaS offering to provision and run virtual machines**

**Q2. What is a "zone" in GCP?**
A) A collection of projects
B) A physical data center within a region
C) A firewall setting
D) A storage class

✅ Answer:** B) A physical data center within a region**

**Q3. Which command allows you to SSH into a VM instance?**
A) gcloud compute connect
B) gcloud compute ssh
C) gcloud ssh instance
D) gcloud vm login

✅ Answer: **B) gcloud compute ssh**

**Q4. Which firewall rule is required to allow HTTP access to NGINX running on your VM?**
A) --allow tcp:22
B) --allow tcp:443
C) --allow tcp:80
D) --allow all

✅ Answer: **C) --allow tcp:80**

**Q5. What is the purpose of a startup script in Compute Engine?**
A) To connect VM with IAM roles
B) To run initialization tasks automatically when VM boots
C) To define firewall rules
D) To monitor VM performance

✅ Answer: **B) To run initialization tasks automatically when VM boots**

### Part B – Short Answer Questions

**Q6. What is the difference between a boot disk and a persistent disk?**
✅ Answer:

Boot disk: Contains the operating system needed to start the VM.

Persistent disk: Storage that remains even after the VM is stopped or deleted (if detached).

**Q7. How do you create a custom VM image in GCP?**
✅ Answer:

Stop the VM instance.

Run gcloud compute images create <image-name> --source-disk=<vm-disk> --source-disk-zone=<zone>.

Use that image when launching new VMs.

**Q8. Explain the role of IAM in Compute Engine.**
✅ Answer: IAM controls who has access to resources. For Compute Engine, roles like Compute Admin, Compute Viewer, Compute Instance Admin define what actions users can perform on VM instances.

### Part C – Scenario-based Questions

**Q9. Your team wants to deploy 10 identical web servers on GCP. How will you ensure consistency in deployments?**
✅ Answer:

Use startup scripts to automate NGINX installation, or

Create a custom image with NGINX pre-installed, then launch multiple VMs from that image.

**Q10. You created a VM and installed NGINX, but you cannot access it from your browser. What could be the issue?**
✅ Answer:

Firewall rule for HTTP (port 80) is missing.

NGINX service is not running.

Wrong external IP being used.

**Q11. Compare GCP Compute Engine with AWS EC2 and Azure VM in terms of use case.**
✅ Answer:

GCP Compute Engine = scalable VM service with preemptible VMs (cost optimization).

AWS EC2 = highly mature, largest instance variety, spot instances for cost savings.

Azure VMs = deeply integrated with Microsoft ecosystem.

OCI Compute = cost-effective with strong network performance.
