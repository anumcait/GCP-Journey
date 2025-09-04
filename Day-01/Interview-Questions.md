# ❓ All Possible Questions

### 🔹 Cloud & Basics

What is Cloud?

What is Cloud Computing?

Difference between Cloud and Traditional IT?

What are the benefits of cloud computing?

What is the pay-as-you-go model?

### 🔹 Cloud Types

What is Public Cloud? Give examples.

What is Private Cloud? Give examples.

What is Hybrid Cloud? Give examples.

Difference between Public, Private, and Hybrid Cloud?

### 🔹 GCP Specific

Why should we choose GCP over AWS or Azure?

What services are available in GCP for computing?

What is IAM in GCP?

What is Cloud Storage in GCP?

What is Cloud Shell in GCP?

What is a GCP Project?

### 🔹 Vocabulary & Advanced Basics

What is Virtualization?

What is a Virtual Machine (VM)?

What is an API?

What are Regions in cloud?

What are Zones in cloud?

What is Scalability?

What is Elasticity?

What is Agility?

What is High Availability?

What is Fault Tolerance?

What is Disaster Recovery?

What is Load Balancing?

Difference between Scalability and Elasticity?

Difference between Fault Tolerance and Disaster Recovery?

Difference between Load Balancing and Auto-scaling?

What is meant by Shared Responsibility in cloud?

### ✅ Answers

**Cloud:** A virtual space to store and run apps/data online.

**Cloud Computing:** Delivery of IT services (servers, storage, apps) via the internet.

**Difference (Cloud vs Traditional):** Rent vs Buy, Auto-scale vs Manual setup.

**Benefits:** Cost-effective, scalable, global reach, high availability.

**Pay-as-you-go:** Pay only for the resources you consume.

**Public Cloud:** Shared by all users. Example: Gmail, AWS, GCP.

**Private Cloud:** Dedicated to one organization. Example: On-premises servers.

**Hybrid Cloud:** Combination of public and private.

**Difference:** Public = shared, Private = exclusive, Hybrid = mix.

**Why GCP:** Google infra, AI/ML strengths, cost-friendly, easy to use.

**GCP Compute Services:** Compute Engine, App Engine, Kubernetes Engine, Cloud Functions.

**IAM in GCP:** Identity and Access Management – controls permissions.

**Cloud Storage:** Object storage for files, backups, images.

**Cloud Shell:** Browser-based terminal with gcloud pre-installed.

**GCP Project:** A logical container for resources, billing, and IAM.

**Virtualization:** Creating multiple virtual resources from one physical resource.

**VM:** Emulated computer with OS and apps.

**API:** Rules for software-to-software communication.

**Regions:** Geo areas with GCP data centers.

**Zones:** Subdivisions of regions for redundancy.

**Scalability:** Planned resource expansion.

**Elasticity:** Automatic resource adjustment.

**Agility:** Speed to deploy and adapt.

**High Availability:** 99.9% uptime, no downtime.

**Fault Tolerance:** Keeps working despite failures.

**Disaster Recovery:** Restores systems after disaster.

**Load Balancing:** Distributes traffic to multiple servers.

**Scalability vs Elasticity:** Scalability = capacity planning, Elasticity = auto-adjustment.

**Fault Tolerance vs DR:** Fault Tolerance = keep running, DR = recover after fail.

**Load Balancing vs Auto-scaling:** LB = distribute load, Auto-scaling = adjust number of servers.

**Shared Responsibility:** Cloud provider secures infra, user secures apps/data.


What is GCP and why do companies use it?
GCP is referred as Google cloud Platform. Companies use this because of its already proven services like youtube, mail and search.

Explain the difference between a Region and a Zone in GCP.

What is Compute Engine and when would you use it?

Explain Cloud Storage and give a real-world example of its usage.

What is App Engine and how is it different from Compute Engine?

What is BigQuery and why is it powerful for data analytics?

What is IAM in GCP and why is it important?

What is Cloud Shell and when would you use it?


# ☁️ Day-01: GCP Short Answer Questions & Answers

---

## 🔹 Short Answer Questions

1. **Explain Virtualization with a real-time example.**  

2. **What is a Virtual Machine and why is it important in cloud computing?**  

3. **What is an API and how does GCP use APIs?**  

4. **Differentiate between Regions and Availability Zones.**  

5. **Explain Scalability vs Elasticity with examples.**  

6. **Define Agility in cloud computing.**  

7. **What does High Availability mean in practice?**  

8. **How does Fault Tolerance differ from Disaster Recovery?**  

9. **Why is Disaster Recovery important for enterprises?**  

10. **Explain a real-world use case of Load Balancing in GCP.**  

---

## ✅ Answers

1. **Virtualization** is creating virtual versions of physical hardware, such as servers, storage, or networks.  
   **Example:** Running multiple virtual servers on a single physical server in GCP Compute Engine to save cost and resources.  

2. **Virtual Machine (VM)** is a software-based emulation of a physical computer.  
   **Importance:** VMs allow running multiple OS instances on a single physical server, enabling resource efficiency and isolation in cloud computing.  

3. **API (Application Programming Interface)** is a set of rules that allows software to communicate.  
   **GCP Usage:** GCP APIs allow developers to programmatically interact with cloud services like Compute Engine, Cloud Storage, BigQuery, etc., without using the console.  

4. **Regions vs Availability Zones (AZs):**  
   - **Region:** A geographical area where GCP data centers are located (e.g., `us-central1`).  
   - **AZ:** Isolated locations within a region, each with independent power, networking, and cooling.  

5. **Scalability vs Elasticity:**  
   - **Scalability:** Ability to increase resources to handle growth over time.  
     **Example:** Adding more VM instances for a growing e-commerce website.  
   - **Elasticity:** Automatic adjustment of resources based on current demand.  
     **Example:** Auto-scaling VM instances during festival sales traffic spikes.  

6. **Agility** is the ability to quickly deploy and adapt resources or applications.  
   **Example:** Spinning up new VMs or databases in minutes during a sudden project requirement.  

7. **High Availability (HA)** means a system is operational and accessible most of the time, typically 99.9% uptime.  
   **Practice:** Deploying application servers across multiple AZs ensures users can access the app even if one AZ fails.  

8. **Fault Tolerance vs Disaster Recovery:**  
   - **Fault Tolerance:** System continues running despite hardware/software failure.  
   - **Disaster Recovery:** Planned process to restore operations after major failure or disaster.  

9. **Disaster Recovery Importance:** Protects enterprise data and ensures business continuity in case of data loss, natural disaster, or cyber-attack.  

10. **Real-world Load Balancing Use Case:**  
    - **Scenario:** Streaming IPL matches to millions of viewers.  
    - **Solution:** Use GCP **Cloud Load Balancing** to distribute user requests evenly across multiple servers, ensuring smooth streaming without server overload.  

---

## ❓ Real-Time Scenario Questions

1. **Scenario:** Your company needs to store large amounts of user-uploaded files that will be accessed globally.  
   **Question:** Which type of cloud model would you choose and why?  

2. **Scenario:** You are developing a web application and expect traffic spikes during product launches.  
   **Question:** How would you ensure your application handles peak traffic without downtime?  

3. **Scenario:** A department wants its own dedicated cloud environment for sensitive data, but occasionally needs extra compute from public cloud.  
   **Question:** Which cloud deployment model fits this requirement?  

4. **Scenario:** Your GCP VM in one zone fails due to a hardware issue, but the application must remain online.  
   **Question:** How can you design the system for fault tolerance?  

5. **Scenario:** You have multiple servers running an e-commerce app, and one server is overloaded while others are idle.  
   **Question:** What GCP service would you use to distribute the load?  

6. **Scenario:** You need to allow your application to interact with GCP services programmatically.  
   **Question:** How would you securely enable this communication?  

7. **Scenario:** You want to analyze a large dataset from your cloud storage to generate business insights.  
   **Question:** Which GCP service would you use for fast, serverless analytics?  

8. **Scenario:** You want to automate access control for your team members in GCP.  
   **Question:** Which GCP service helps manage permissions and roles efficiently?  

9. **Scenario:** Your company wants to save costs by paying only for what is actually used instead of buying physical servers.  
   **Question:** Which cloud computing pricing model is being referred to?  

10. **Scenario:** You are asked to quickly spin up a temporary VM for testing without worrying about the underlying infrastructure.  
    **Question:** Which GCP service or tool allows you to do this easily?

---

## ❓ Additional Real-Time Scenarios

11. **Scenario:** Your e-commerce app receives 10x traffic during a festival sale.  
    **Question:** Which cloud feature helps automatically handle this?  

12. **Scenario:** You are deploying an app in Mumbai Region. To ensure uptime during a power failure in one data center,  
    **Question:** What GCP feature should you use?  

13. **Scenario:** Your company wants to store backup data in a separate geographic location for safety.  
    **Question:** Which cloud concept applies here?  

14. **Scenario:** During IPL streaming, millions of users connect simultaneously.  
    **Question:** Which service ensures traffic is evenly distributed?  

15. **Scenario:** A banking app must remain available 24/7 with minimum downtime.  
    **Question:** Which two cloud features will you use?  

---

## ✅ Answers

1. **Answer:** Use **Public Cloud** (e.g., GCP Cloud Storage) for global access, scalability, and cost-effectiveness.  

2. **Answer:** Implement **auto-scaling** and **load balancing** using Compute Engine + Cloud Load Balancing to handle traffic spikes dynamically.  

3. **Answer:** Use a **Hybrid Cloud** model – private cloud for sensitive data, public cloud for extra compute on demand.  

4. **Answer:** Deploy resources in **multiple zones (Availability Zones)** within a region to achieve **fault tolerance**.  

5. **Answer:** Use **Cloud Load Balancing** to distribute traffic evenly across servers.  

6. **Answer:** Use **APIs** and **Service Accounts** to securely allow programmatic communication between applications and GCP services.  

7. **Answer:** Use **BigQuery** for fast, serverless, and scalable analytics on large datasets.  

8. **Answer:** Use **IAM (Identity and Access Management)** to define roles and manage permissions efficiently.  

9. **Answer:** The **pay-as-you-go** model – you pay only for the resources used.  

10. **Answer:** Use **Cloud Shell** or **Compute Engine** to quickly spin up temporary VMs for testing without managing physical servers.  

---

### ✅ Answers for Additional Real-Time Scenarios

11. **Answer:** **Auto-scaling** – GCP automatically adjusts the number of instances based on traffic.  

12. **Answer:** Deploy your app across **multiple Availability Zones (AZs)** within the Mumbai Region for high availability.  

13. **Answer:** **Disaster Recovery / Geo-redundant storage** – backup data in a different geographic region.  

14. **Answer:** **Cloud Load Balancing** – ensures traffic is distributed evenly across servers to prevent overload.  

15. **Answer:** **High Availability (HA)** and **Fault Tolerance** – ensures the banking app remains operational with minimal downtime.  

---

✅ End of **Day-01: Real-Time Scenario Q&A**

