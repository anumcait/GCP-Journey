# GCP VPC Interview Questions

### 1. What is a VPC in GCP?
**Answer:**  
A Virtual Private Cloud (VPC) is a **globally unique, isolated virtual network** in GCP where you can launch and manage cloud resources securely.

---

### 2. Difference between Auto Mode and Custom Mode VPC?
**Answer:**  
| Feature | Auto Mode | Custom Mode |
|---------|-----------|-------------|
| Subnet Creation | Automatic in all regions | Manual in specific regions |
| IP Range | Predefined | User-defined |
| Use Case | Quick deployments | Fine-grained control over IPs |

---

### 3. What is a subnet in GCP VPC?
**Answer:**  
A **subnet** is a regional IP range within a VPC. Subnets determine where your resources are deployed in a specific region and define their private IP addresses.

---

### 4. How is VPC peering different from VPN?
**Answer:**  
- **VPC Peering:** Private connection between two VPCs within or across projects, no encryption needed.  
- **VPN:** Encrypted connection between VPC and on-premises network over the internet.

---

### 5. What is Cloud NAT and why is it used?
**Answer:**  
Cloud NAT (Network Address Translation) allows **private instances** (without external IPs) to access the internet securely while keeping them unexposed to inbound traffic.

---

### 6. Explain firewall rules in GCP VPC.
**Answer:**  
Firewall rules control **incoming and outgoing traffic** for VMs in a VPC. Rules are **stateful** and can allow or deny traffic based on IP ranges, protocols, or ports.

---

### 7. What is Shared VPC?
**Answer:**  
Shared VPC allows multiple projects to **use a centralized VPC** managed by a host project, simplifying network management across large organizations.

---

### 8. What is Private Service Connect?
**Answer:**  
Private Service Connect allows you to **privately access Google-managed services** or third-party services **without exposing traffic to the public internet**.

---

### 9. Can a GCP VPC span multiple regions?
**Answer:**  
Yes, a VPC itself is **global**, but its subnets are **regional**. This allows resources in different regions to communicate within the same VPC.

---

### 10. What are VPC flow logs?
**Answer:**  
VPC flow logs capture **network traffic information** (ingress and egress) for monitoring, auditing, and security analysis.

---
> ⚡ **Tip for interviews:** Understand **VPC as a gated community**, know the difference between **Auto vs Custom mode**, **Private vs Public connectivity**, and key features like **Cloud NAT, Shared VPC, and VPC Peering**.

