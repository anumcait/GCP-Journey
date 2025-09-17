## 🧑‍💻 GCP Compute Engine Interview Q&A
### 🔹 Fundamentals

**Q1. What is Google Compute Engine?**
A: Compute Engine is GCP’s IaaS (Infrastructure-as-a-Service) offering that lets you create and manage virtual machines (VMs). You can customize CPU, RAM, OS, and networking. It supports automation via startup scripts, custom images, and integrates with DevOps tools like Terraform and Ansible.

**Q2. How is Compute Engine different from App Engine?**
A:

Compute Engine → VM-level control, you manage OS, networking, and scaling.

App Engine → PaaS, Google manages infrastructure, you just deploy code.
👉 Use Compute Engine for flexibility, App Engine for simplicity.

**Q3. What are regions and zones in GCP?**
A:

Region → Geographic location (e.g., us-central1).

Zone → An isolated data center within a region (e.g., us-central1-a).
Choosing multiple zones ensures high availability.

**Q4. What types of machine families are available in Compute Engine?**
A:

General Purpose → e2, n2, n1 (balanced workloads).

Compute Optimized → c2 (high CPU).

Memory Optimized → m1, m2 (large memory).

Accelerator Optimized → a2 (GPU/TPU).

**Q5. What’s the difference between a persistent disk, local SSD, snapshot, and image?**
A:

Persistent Disk → Durable storage, survives VM restarts.

Local SSD → Very fast, but data lost when VM stops.

Snapshot → Backup of a disk (point-in-time).

Image → Template to create new VMs (includes OS + configs).

### 🔹 Networking

**Q6. How do you access a VM in Compute Engine?**

A: You can:

Use SSH via Cloud Console or gcloud compute ssh.

Enable OS Login for IAM-based SSH.

Use external IP for internet access, or internal IP for VPC-only communication.

**Q7. What is a firewall rule in GCP?**

A: A firewall rule controls traffic to/from instances in a VPC. Example: allow HTTP traffic with:

gcloud compute firewall-rules create allow-http \
  --allow tcp:80 --target-tags=http-server


**Q8. Difference between firewall rules in GCP and security groups in AWS?**
A:

GCP Firewall Rules → Apply at VPC level, target instances via tags or service accounts.

AWS Security Groups → Apply at instance level, stateful rules per group.

### 🔹 IAM & Security

**Q9. What’s the difference between IAM roles and service accounts?**
A:

IAM Roles → Permissions assigned to users or groups (e.g., Compute Admin).

Service Accounts → Special identities for applications/VMs to interact with GCP APIs securely.

**Q10. How would you give a user permission to start/stop VMs but not delete them?**
A:

Create a custom IAM role with compute.instances.start and compute.instances.stop.

Assign that role to the user.

**Q11. How do you secure SSH access to production VMs?**
A:

Use OS Login (IAM-based).

Restrict SSH access with firewall rules.

Disable project-wide SSH keys.

Enforce 2FA for accounts.

### 🔹 Operations

**Q12. How do you create a VM using CLI?**
A:

gcloud compute instances create my-vm \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=ubuntu-2004-lts \
  --image-project=ubuntu-os-cloud


**Q13. How would you install NGINX automatically on VM startup?**
A: Use a startup script:

--metadata startup-script='#! /bin/bash
apt update
apt install -y nginx
systemctl enable nginx
systemctl start nginx'


**Q14. How do you verify NGINX installation?**
A:

From VM → curl http://localhost

From browser → http://<external-ip>

**Q15. What’s the difference between stopping and deleting a VM?**
A:

Stop → VM shuts down, persistent disk remains, billing stops for VM but continues for disk.

Delete → VM + disk (if not set to keep) are permanently removed.

### 🔹 Automation & Scaling

**Q16. What are startup scripts in Compute Engine?**
A: Bash scripts executed at boot. They allow infra as code (e.g., install software, configure services automatically). Equivalent to AWS user data.

**Q17. What’s the difference between snapshots and custom images?**
A:

Snapshot → Point-in-time copy of a disk (for backup/restore).

Custom Image → A bootable template to create VMs with pre-installed software.

**Q18. How do you scale applications in Compute Engine?**
A:

Use Managed Instance Groups (MIGs).

Attach autoscaler to adjust VM count based on CPU/load.

Combine with Load Balancer for distribution.

**Q19. How do you set up auto-healing for VMs?**
A:

Use Managed Instance Groups with a health check.

If an instance fails health check, MIG replaces it automatically.

**Q20. How do you integrate Compute Engine with Terraform?**
A:

Use Terraform google_compute_instance resource.

Example:

resource "google_compute_instance" "default" {
  name         = "my-vm"
  machine_type = "e2-micro"
  zone         = "us-central1-a"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
  network_interface {
    network = "default"
    access_config {}
  }
}

### 🔹 Real-World Scenarios

**Q21. VM is not accessible from the internet. Troubleshoot steps?**
A:

Check if VM has external IP.

Verify firewall rule allows HTTP/SSH.

Ensure service (e.g., NGINX) is running.

Check OS firewall (iptables/ufw).

**Q22. How would you replicate a VM setup for multiple teams?**
A:

Create a custom image of the configured VM.

Launch new VMs from that image.

Or use Instance Templates + MIGs.

**Q23. How do you back up and restore a VM?**
A:

Take a snapshot of the disk.

Create a new disk from the snapshot.

Attach it to a new VM.

**Q24. Real-world DevOps use case for custom images?**
A:

A company needs all dev VMs to have NGINX, Docker, and security patches pre-installed.

Instead of manual setup, create a custom image once → use in CI/CD pipelines for consistent environments.

**Q25. How do you handle high availability for web apps on Compute Engine?**
A:

Deploy VMs in multiple zones.

Use Managed Instance Group.

Put a Load Balancer in front.

Enable health checks.

### 🔹 Comparisons with Other Cloud Platforms

**Q26. How does GCP Compute Engine compare with AWS EC2?**
A:

GCP → Simpler startup scripts, sustained-use discounts.

AWS EC2 → Richer ecosystem (AMIs, Auto Scaling, Spot Instances).

Both → VM provisioning at IaaS level.

**Q27. What is the equivalent of GCP firewall rules in AWS & Azure?**
A:

GCP → Firewall rules (VPC-level).

AWS → Security Groups (instance-level).

Azure → NSGs (Network Security Groups).

**Q28. How do startup scripts in GCP compare with AWS user data?**
A: Both allow boot-time automation:

GCP → --metadata startup-script.

AWS → --user-data in EC2.

**Q29. Which platform has the best free tier for learning?**
A:

GCP → e2-micro (always free, US only).

AWS → t2/t3.micro (12 months free).

Azure → B1S (12 months free + credits).

OCI → Always free 2 VMs (best specs among free tiers).

**Q30. What would you consider when migrating workloads from AWS EC2 to GCP Compute Engine?**
A:

Networking differences (VPC vs Security Groups).

AMIs vs custom images.

IAM role mappings.

Cost model differences.

Integration with CI/CD pipelines.
