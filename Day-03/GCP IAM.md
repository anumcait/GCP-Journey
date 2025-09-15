# 🧭 Day-3 : GCP IAM Fundamentals

### 🔐 What is IAM (Identity and Access Manager)

Generally say

GCP IAM is the **gate keeper of the Google Cloud**.

- IAM in GCP is a framework that controls:
    - **`who`** can do **`what`** on **`which`** resources
    - It's crucial for securely managing access in a cloud environment.


👉 Think of it like a company office:
- **Employees** = Users/Principals
- **ID Cards** = Authentication
- **Permission** = What doors you can open
- **Security guard** = IAM enforcing rules

## Core Building Blocks
|Component|Description|
|---------|-----------|
|**Identity** | Who? (User, Group, Service Account)|
|**Role**     | What actions are allowed|
|**Polocy**| Rule = Identity + Role + Resource|
|**Resource**|Any GCP Object (VM, Bucket, Project, API)|

---

## 🎭 Types of Roles

1. Basic Roles : Too broad, legacy roles (not recommended)  -> Owner , Editor, Viewer
2. Predefined Roles : Best Practice -> service-specific (e.g `roles/storage.admin`)
3. Custom Roles : Your own fine grained set of permissions

## 🧬 IAM Policy levels:

- **Organization** (Highest)
- **Folder**
- **Project**
- **Resource** (Lowest)

**🔑 Rule:** Permissions **inherit downward**.  
- Permissions **inherit** from parent to child unless explicitly overridden.
- **Example**: If you’re `Viewer` at **Project**, you’re `Viewer` on all **Buckets** inside it.

---

## 🤖 What are Service Accounts
- Service accounts are non-human identities used by apps, scripts, or CI/CD pipelines.
- Think of them as **robot users** for automation.  
- Example: **Cloud Build service account** deploys apps to **GKE**.

## 🛡️ Best Practices for DevOps
- ✅ Apply **Least Previlege** to users (Only needed permissions) like new user need just Viewing permission
- ✅ Use **Service Accounts** for Pipelines & Automation
- 🚫 Avoid **Basic Roles** (Too much Access)
- 🔁 Rotate **keys/secrets** regulary
- 📊 Audit **IAM Policies** regularly, don't just give and leave, check them regularly or make it like giving alerts for certain period
    
## 🧪 Hands-On Lab

### 🔧 Lab 1 – Create Project & Assign Viewer Role

```bash
export PROJECT_ID="my-demo-$(date +%s)"
gcloud projects create $PROJECT_ID
gcloud config set project $PROJECT_ID

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="user:me@example.com" \
  --role="roles/viewer"
```

### 🔧 Lab 2: Create a Service Account & Grant Role

```bash
gcloud iam service-accounts create devops-bot \
  --display-name="Pipeline Bot"

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:devops-bot@$PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/cloudbuild.builds.editor"
```

---

## 📦 Real-World DevOps Scenario
Use Case:
Your Cloud Build pipeline should deploy to GKE but NOT delete clusters.

**✔ Solution:**

- Create a Custom Role → only container.deployments.create

- Assign that role to a Service Account

- Use that service account in the CI/CD pipeline

---

### ✅ Quick Recap

- IAM is foundational for secure access in GCP. (IAM = Access control system)

- Building blocks: Identity, Role, Policy, Resource

- Use Predefined/Custom Roles → avoid Basic Roles

- Service Accounts = backbone of Devops automation

- Always enforce Least Privilege + Audits regularly

---

