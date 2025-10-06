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
## Cloud VPC / Virtual Network Comparison

| Concept / Feature             | AWS (Amazon)             | Azure (Microsoft)           | GCP (Google)                    | Notes / Description |
|-------------------------------|------------------------|----------------------------|--------------------------------|-------------------|
| Virtual Network               | **VPC (Virtual Private Cloud)** | **VNet (Virtual Network)** | **VPC (Virtual Private Cloud)** | Logical isolated network in the cloud. |
| Subnet                        | **Subnet**              | **Subnet**                  | **Subnet**                      | Smaller IP range within VPC/VNet. |
| IP Addressing                 | **CIDR blocks**          | **Address space / CIDR**    | **CIDR ranges**                 | Used to define IP ranges for VPC/Subnet. |
| Route Table                   | **Route Table**          | **Route Table**             | **Route Table**                  | Determines traffic flow. |
| Internet Gateway               | **Internet Gateway (IGW)** | **Internet Gateway / Virtual Network Gateway** | **Internet Gateway** | Connects VPC/VNet to the internet. |
| NAT (Network Address Translation) | **NAT Gateway / NAT Instance** | **NAT Gateway / NAT Instance** | **Cloud NAT**                   | Allows private subnets to access internet. |
| Firewall / Security           | **Security Group (stateful)** <br> **Network ACL (stateless)** | **NSG (Network Security Group)** | **Firewall Rules**               | Controls traffic to/from subnets or instances. |
| Peering                       | **VPC Peering**          | **VNet Peering**            | **VPC Peering**                 | Connects two networks privately. |
| VPN Connection                 | **VPN Gateway / Customer Gateway** | **VPN Gateway / Local Network Gateway** | **Cloud VPN**                   | Connects on-premises networks securely. |
| Private Connectivity           | **Direct Connect**       | **ExpressRoute**            | **Cloud Interconnect / Dedicated Interconnect** | Private dedicated connection to cloud. |
| Multi-region / Global Network | **Transit Gateway / Global Accelerator** | **Virtual WAN**             | **VPC Network + Global Peering** | Connect multiple networks across regions. |
| DHCP Options                   | **DHCP Options Set**     | N/A                        | **DHCP Policies**               | Assign DNS or domain options to instances. |
| Elastic IP / Static IP         | **Elastic IP**           | **Public IP / Static Public IP** | **External IP**                 | Static public IP assignment. |
| Endpoint Service               | **VPC Endpoint / PrivateLink** | **Private Endpoint**        | **Private Service Connect**     | Connect privately to cloud services. |
| Flow Logs                     | **VPC Flow Logs**        | **NSG Flow Logs / Diagnostic Logs** | **VPC Flow Logs**               | Monitor traffic in/out of network. |

> ⚡ **Tip:** AWS and GCP use the term **VPC**, Azure calls it **VNet**. Security rules differ slightly: AWS has **Security Groups + NACLs**, Azure has **NSG**, and GCP has **Firewall Rules**. Peering and private connectivity features are conceptually similar but implemented differently in each cloud.
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

