## 🧠 Interview Questions & Answers
### 🧩 Conceptual Questions
**Q1. What is the difference between Instance Template and Managed Instance Group?**

**A:**
An Instance Template defines the configuration (machine type, disk, startup script).
A Managed Instance Group (MIG) uses that template to create, manage, and auto-scale multiple identical VMs.

**Q2. How does Autoscaling work in GCP?**

**A:**
Autoscaling monitors metrics like CPU utilization, load balancing capacity, or custom metrics. When usage exceeds the threshold, new instances are created (up to the max). When demand decreases, instances are deleted (down to the min).

**Q3. What ensures High Availability in your setup?**

**A:**
By creating a Regional MIG (multi-zone), instances are distributed across zones.
If one zone fails, others continue serving traffic — ensuring no single point of failure.

**Q4. What’s the purpose of Health Checks in Load Balancing?**

**A:**
Health checks periodically probe instance endpoints. If an instance fails to respond, the load balancer automatically stops routing traffic to it, maintaining uptime.

**Q5. Why was your load balancer not responding initially?**

**A:**
Because firewall rules blocking incoming traffic on port 80 were not configured.
After adding a firewall rule to allow tcp:80, external requests worked fine.

**Q6. How do you verify if load balancing is working?**

**A:**
By refreshing the public IP and observing responses from different instance hostnames (e.g., curl <LB_IP> multiple times).
You can also check Load Balancer Logs under GCP’s Logging → Load Balancing Requests.

**Q7. What happens if one instance fails the health check?**

**A:**
That instance is temporarily removed from backend service rotation. MIG automatically recreates a healthy instance to maintain desired capacity.

**Q8. How can you simulate Autoscaling behavior?**

**A:**
Use a load generator like:
```
ab -n 10000 -c 100 http://<LB_IP>/
```
This increases CPU utilization and triggers auto-scaling.

### 🧰 Scenario-Based Questions

- Load balancer not responding	
  - Tests networking & troubleshooting	
    - "I’d check firewall rules, health check configuration, and backend service status."
- One zone is down	
  - Tests HA understanding	
     - "Regional MIG ensures instances in other zones keep running, maintaining uptime."
- Scaling beyond limits	
  - Tests cost awareness	
    - "I’d configure autoscaling with min/max limits and possibly schedule scaling for cost optimization."
- Need HTTPS	
  - Tests practical knowledge	
    - "Use Google-managed SSL certs on load balancer frontend config."


