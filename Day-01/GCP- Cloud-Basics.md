## ☁️ Day-01: Introduction to Cloud and Google Cloud Platform (GCP)

---

### 📌 Key Concepts Covered
- What is Cloud Computing?  
- Difference between Cloud and Traditional IT  
- Why choose Google Cloud (GCP) over AWS or Azure?  
- Create your Free GCP Account  
- Quick Tour: GCP Console and Cloud Shell  
- Basics of Cloud Deployment Models (Public, Private, Hybrid)  
- Vocabulary of Cloud Computing  
- Day-1 Questions & Answers  

---

# ☁️ Basics of Cloud Computing
---
## 🔹 What is Cloud?

The **cloud** is a vast, virtual space where you can **store files, run software, and access services** over the internet.  

It’s like having a **powerful computer on the web** that you can use anytime without worrying about hardware.  


## 🌩️ What is Cloud Computing?

**Cloud computing** is a technology model that delivers **computing services over the internet**.  



Cloud computing means renting servers, databases, storage, and other services over the internet instead of owning them.  

Instead of maintaining physical servers, users can access on-demand resources over the internet-no need to own physical infrastructure such as:  
- Servers  
- Storage  
- Databases  
- Applications  

👉 In simple words:  
> "You pay only for what you use. No upfront investment, no maintenance headache."
> *"Use resources on demand. No upfront costs. No maintenance headaches."*

---

## 🔹 Cloud Models

### 🌍 Public Cloud
- **Definition:** Shared infrastructure for everyone.  
- **Example:** Google Drive, Amazon Web Services (AWS).  

### 🏢 Private Cloud
- **Definition:** Cloud dedicated to a single organization.  
- **Example:** A company running its own private servers.  

### 🔀 Hybrid Cloud
- **Definition:** A combination of both public and private cloud models.  
- **Example:** A business using private cloud for sensitive data and public cloud for scalability.  

### [🔗](https://github.com/anumcait/GCP-Journey/blob/main/Day-01/GCP-%20Cloud-Vocabulary.md) Cloud Vocabulary That need to be familier

---

Cloud providers:  
- Google Cloud Platform (GCP)  
- Amazon Web Services (AWS)  
- Microsoft Azure  

---

## 🏢 Traditional IT vs Cloud

| Traditional IT                  | Cloud (GCP)                   |
|---------------------------------|-------------------------------|
| Buy servers/Hardware upfront    | Rent what you need            |
| Pay for peak capacity/Usage     | Auto-scale as needed          |
| Manual setup                    | Automated provisioning        |
| High maintenance                | Managed by Google             |

---

## 🚀 Why Choose GCP?
- Google-level infrastructure, Backbone of Google Services (same as Search, YouTube, Gmail)  
- Powerful networking and AI/ML services  
- Easy to use UI + powerful CLI (`gcloud`)   
- Cost-effective and beginner-friendly  
- Trusted by global companies (e.g., Meta)  

---

## 📝 Task: Create Your Free GCP Account 
1. Go to: [https://cloud.google.com/free](https://cloud.google.com/free)  
2. Sign in with your Gmail account  
3. Provide PAN + card details (or UPI)  
4. ✅ No charges unless you upgrade manually  

---

> ✨ *"Cloud isn’t just the future—it’s the present. Every command I learn today builds the foundation for tomorrow’s tech career."*
## 🖥️ Task: Explore GCP Console and Cloud Shell

### 🔹 GCP Console
- URL: [https://console.cloud.google.com](https://console.cloud.google.com)  
- Everything you need: Compute, Storage, Billing, IAM, etc.  

### 🔹 Cloud Shell
- Built-in terminal in your browser  
- Free VM with 5GB storage  
- Comes pre-installed with **gcloud CLI**  

👉 Try these commands in Cloud Shell:
```bash
gcloud auth list
gcloud config list
