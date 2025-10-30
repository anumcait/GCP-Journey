# 🧠 Day 15: Secrets Manager – Storing API Keys Safely in GCP

Google Cloud **Secret Manager** allows you to store, manage, and access sensitive information — such as API keys, passwords, or certificates — securely.  
Here, we’ll learn **why**, **how**, and **what** to do when working with secrets in GCP.

---

## 📍 Why Not to Hardcode Passwords or Tokens

Hardcoding secrets (like API keys or credentials) directly into source code is a **major security risk**.

```diff
- api_key = "12345-SECRET-KEY" -- This api key is hardcoded
```

> “Hardcoding secrets is like writing your ATM PIN on your debit card 💳.  
If your code is leaked, your keys go with it.  
Instead, store them securely in Secret Manager — where they’re encrypted, access-controlled, and logged.”

### ✅ Best Practice
> Always store secrets **outside** of your source code, in a secure vault such as **Google Cloud Secret Manager**.

---

## ⚙️ How Secret Manager Works

> Here’s how Secret Manager works behind the scenes:  
1️⃣ Store your secret in GCP  
2️⃣ It’s automatically **encrypted** with Google-managed keys  
3️⃣ Control access with IAM  
4️⃣ Fetch it securely in your app


## 🔒 What is GCP Secret Manager?

**Secret Manager** is a fully managed service provided by Google Cloud to:
- Securely **store** sensitive data (API keys, tokens, passwords, etc.).
- **Access** secrets programmatically with fine-grained IAM control.
- **Rotate** secrets automatically on a schedule.
- **Audit** who accessed or modified secrets.

Secrets are **encrypted at rest and in transit**, and fully integrated with **IAM** and **Cloud Audit Logs**.

- Diagram: Storage → Encryption → IAM Control → Secure Access

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/bfdfa2b4-85b2-4dd5-af8b-c01b8901903c" />

---

## ⚙️ Step-by-Step Guide: Storing and Accessing Secrets

### 🧩 Step 1 – Enable the Secret Manager API
Before creating or managing secrets, enable the API:

```bash
gcloud services enable secretmanager.googleapis.com
```
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/760fcf98-71cb-4240-8c8c-f9d9c9df0377" />

### 🧩 Step 2 – Create a Secret

Create a new secret to store your API key or password.
```bash
gcloud secrets create my-api-key \
  --replication-policy="automatic"
```
**Explanation:**

- my-api-key → Name of the secret.
- --replication-policy="automatic" → Google replicates data securely across regions.

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/ec880128-c111-4f00-822a-ea0584294edf" />

### 🧩 Step 3 – Add a Secret Version

Add the actual secret value to the secret. Every time you update the value, it creates a new version.
```bash
echo -n "YOUR_SECRET_KEY" | gcloud secrets versions add my-api-key --data-file=-
```
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/8cb3304e-fc5e-41ce-8127-bfbe120c06e1" />


**Explanation:**

- The echo -n command pipes your secret string directly into Secret Manager.
- Secret versions help maintain history and support rotation without downtime.

### 🧩 Step 4 – Grant Access with IAM
gcloud secrets add-iam-policy-binding my-api-key \
  --member="serviceAccount:myapp@project-id.iam.gserviceaccount.com" \
  --role="roles/secretmanager.secretAccessor"

### 🧩 Step 5 – Access It from Your Code

```python
from google.cloud import secretmanager

client = secretmanager.SecretManagerServiceClient()
name = "projects/PROJECT_ID/secrets/my-api-key/versions/latest"
response = client.access_secret_version(request={"name": name})
api_key = response.payload.data.decode("UTF-8")
print(api_key)
```

**“This way, your app reads secrets securely — no hardcoding, no leaks!”**

---

## 🔑 IAM-Based Access Control for Secrets

GCP Secret Manager uses IAM (Identity and Access Management) to control who can access or manage secrets.

|Role|	Description|
|----|-------------|
|roles/secretmanager.admin|	Full control over secrets|
|roles/secretmanager.secretAccessor|	Read-only access to secret data|
|roles/secretmanager.viewer|	View metadata only|
|roles/secretmanager.secretVersionAdder|	Add new secret versions|

Example: Step-4 above

**Best Practice:**
Use principle of least privilege – only grant minimum required access.

---

## 🔁 Secret Rotation

Rotating secrets reduces risk from credential leaks. GCP allows both manual and automatic rotation.

Manual Rotation Example:
```bash
echo -n "NEW_SECRET_KEY" | gcloud secrets versions add my-api-key --data-file=-
```
Automatic Rotation Example:
```bash
gcloud secrets update my-api-key \
  --rotation-period="30d" \
  --next-rotation-time="2025-11-01T00:00:00Z"
```

**Tip:** Always ensure your application dynamically fetches secrets so it doesn’t require redeployment after rotation.

---

## 🧾 Auditing Secret Access

Every interaction with Secret Manager is logged using Cloud Audit Logs.
**You can track:**
- Who accessed the secret
- When it was accessed
- From which service or account

**View Audit Logs:**
1. Go to Google Cloud Console → Logging → Logs Explorer
2. Filter by resource.type="secretmanager_secret"
3. Check access and modification events

**Use Case:**
Helps detect unauthorized access and maintain compliance.

---

