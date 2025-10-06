# Google Cloud Platform (GCP) VPC

## Overview
A **Virtual Private Cloud (VPC)** in GCP is a **private, isolated network environment** that allows you to host your cloud resources (VMs, databases, storage) securely. It provides **subnetworks, IP address management, firewall rules, routes, and connectivity options** to design a scalable and secure cloud network.

Think of a VPC as a **gated community**:
- **VPC:** The gated community itself  
- **Subnets:** Individual blocks/streets within the community  
- **Firewall Rules:** Security guards controlling entry and exit  
- **Routes:** Roads connecting the community internally and externally  
- **Cloud NAT / Internet Gateway:** Main gates to allow controlled access to the outside world  

---

## Key Components

| Component | Description |
|-----------|-------------|
| **VPC** | Global, logically isolated network in GCP. |
| **Subnet** | Region-specific IP range within a VPC. |
| **Firewall Rules** | Define what traffic is allowed or denied. |
| **Routes** | Control how packets flow between networks. |
| **Cloud Router** | Enables dynamic routing between on-premises and GCP VPC. |
| **Cloud VPN** | Secure connection from on-premises to GCP VPC. |
| **Cloud NAT** | Enables private VMs to access the internet without external IPs. |
| **VPC Peering** | Connect two VPCs privately within or across projects. |
| **Shared VPC** | Allows multiple projects to use a centralized VPC. |
| **Private Service Connect** | Access GCP services privately without using the public internet. |
| **Flow Logs** | Monitor ingress and egress traffic within a VPC. |

---

## Types of VPCs in GCP

1. **Auto Mode VPC**
   - Subnets are automatically created in each region.
   - Easy to deploy but less flexible IP control.

2. **Custom Mode VPC**
   - Subnets are created manually in specific regions.
   - Offers full control over IP ranges and network topology.

---

## Connectivity Options

- **Internet Access:** Through default internet gateway.  
- **Private Access to Google Services:** Using Private Google Access.  
- **On-Premises Connection:** Cloud VPN or Dedicated Interconnect.  
- **VPC Peering:** Connect multiple VPCs privately.  

---

## GCP VPC Interview Questions

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

## Real-world Analogy

| VPC Concept | Gated Community Analogy |
|------------|------------------------|
| VPC | The entire gated community |
| Subnet | Streets / Blocks inside the community |
| Firewall Rules | Security guards at gates |
| Routes | Roads connecting streets and other communities |
| Cloud NAT | Guarded exit for residents to reach outside safely |
| VPC Peering | Private tunnel to neighboring gated community |
| Cloud VPN | Encrypted private tunnel from outside world to community |

---

## Useful Links

- [GCP VPC Documentation](https://cloud.google.com/vpc/docs)  
- [VPC Peering Overview](https://cloud.google.com/vpc/docs/vpc-peering)  
- [Cloud NAT Guide](https://cloud.google.com/nat/docs/overview)  
- [VPC Firewall Rules](https://cloud.google.com/vpc/docs/firewalls)  

---

> ⚡ **Tip for interviews:** Understand **VPC as a gated community**, know the difference between **Auto vs Custom mode**, **Private vs Public connectivity**, and key features like **Cloud NAT, Shared VPC, and VPC Peering**.

