# Day-12 : Deep Dive into **Google Cloud Artifact Registry**

We’ll explore **Google Cloud Artifact Registry (AR)** — how to store, manage, and distribute **container** and **non-container artifacts** across teams securely.

---

## 🧩 What is Artifact Registry?

**Artifact Registry (AR)** is a **Google Cloud managed service** used to store and manage:
- **Container images** (like Docker images)
- **Non-container artifacts** (like `.jar`, `.war`, `npm`, or `python` packages)

It **replaces Google Container Registry (GCR)**, which was **deprecated in March 2025**.

---

## 🚀 Why Store Container Images Centrally?

In an organization:
- You may have **multiple microservices** (e.g., payments, shipments, UI, etc.).
- Each has **multiple versions** (v1, v2, … v100).

Storing all versions locally leads to:
- Single point of failure.
- Difficulty in collaboration.
- No centralized control.

**Artifact Registry** acts as a **centralized, secure location** to store, version, and share images.

---

## ⚖️ Difference Between Artifact Registry and Docker Hub

| Feature | Docker Hub | Artifact Registry |
|----------|-------------|-------------------|
| Ownership | Public cloud service (Docker Inc.) | Managed by your GCP project |
| Privacy | Public by default, private limited | Fully private within GCP |
| IAM Integration | No | Integrated with **Google Cloud IAM** |
| Latency | Global, but not region-specific | Multi-region and regional setups |
| Scanning | Limited | Built-in vulnerability scanning |
| Cost | Free (public), paid (private) | Pay per GB stored |

For **enterprise workloads**, companies prefer **Artifact Registry** due to:
- IAM integration  
- High availability  
- Security scanning  
- GCP-managed infrastructure  

---

## 🏗️ Managed vs Self-Hosted Registries

| Approach | Description | Pros | Cons |
|-----------|--------------|------|------|
| **Self-Hosted Registry** | Deploy your own container registry on VMs or Kubernetes. | Full control | Must handle patching, scaling, HA |
| **Managed (Artifact Registry)** | Provided by Google Cloud. | No patching, high availability, IAM integration | Cost based on usage |

**Conclusion:** Use **Artifact Registry** for production-grade, secure environments.

---

## 💻 Hands-On Steps (GCloud CLI)

Let’s implement everything step-by-step.

---

### 🧩 Step 1: Set up Cloud Shell and Project (One time setup)
```bash
# Set current project
gcloud config set project PROJECT_ID


# Export environment variables
export PROJECT_ID=$(gcloud config get-value project)
export REGION=asia-south1
export ZONE=asia-south1-a
export REPO_NAME=ecommerce-payments-dev
```
Verify:

```bash
echo $PROJECT_ID $REGION $ZONE $REPO_NAME
```
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/29d819e7-af91-4789-98f1-01eac5fd5534" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/30f4d127-4998-4e89-9912-745406727925" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/336de340-55f1-496b-a544-d4fb89a0df40" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/07eb32f0-b1f1-471b-92fd-a94eb566b34b" />

---

## ⚙️ Step 2: Enable Required APIs

```bash
gcloud services enable artifactregistry.googleapis.com \
  compute.googleapis.com \
  iam.googleapis.com
```
✅ This enables the Artifact Registry, Compute Engine, and IAM APIs.

---

## 🏗️ Step 3: Create a Docker Repo in Artifact Repository
```bash
gcloud artifacts repositories create $REPO_NAME \
  --repository-format=docker \
  --location=$REGION \
  --description="Repository for Payments microservice"

### Verify:

gcloud artifacts repositories list
```
You should see your repository in the list.
---

## 👤 Step 4: Create a Service Account and Grant Permissions
```bash
# Create service account
gcloud iam service-accounts create ar-demo \
  --display-name="Artifact Registry Demo Account"

# Grant write permissions to Artifact Registry
gcloud artifacts repositories add-iam-policy-binding $REPO_NAME \
  --location=$REGION \
  --member="serviceAccount:ar-demo@$PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/artifactregistry.writer"
```

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/7e308743-9561-40e1-b86c-70d949671146" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/d39b4ccb-6435-4394-8335-60bddefa6138" />

---

## 🖥️ Step 5: Create Virtual Machine and Attach Service Account
```bash
gcloud compute instances create ar-pay-demo \
  --zone=$ZONE \
  --machine-type=e2-micro \
  --service-account=ar-demo@$PROJECT_ID.iam.gserviceaccount.com \
  --scopes=https://www.googleapis.com/auth/cloud-platform

### Verify instance:
gcloud compute instances list
```

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/de48f042-8482-45a2-8461-2659484547a6" />


---

## Step 6: Connect to the VM using Console → “SSH” button
In the GCP Console, go to Compute Engine → VM instances → ar-builder → SSH.
You are now inside the VM shell.

Set env vars on the VM (so the rest of the steps work from here):
```bash
# Auto-detect PROJECT_ID and ZONE from metadata (recommended)
export PROJECT_ID="$(curl -H 'Metadata-Flavor: Google' -s http://metadata.google.internal/computeMetadata/v1/project/project-id)"
export ZONE="$(curl -H 'Metadata-Flavor: Google' -s http://metadata.google.internal/computeMetadata/v1/instance/zone | awk -F/ '{print $NF}')"
export REGION="${ZONE%-*}"

# Set the same values used earlier
export REPO="apps"
export IMAGE="hello"
export TAG="v1.0.0"
export AR_HOST="${REGION}-docker.pkg.dev"
export IMG_PATH="${AR_HOST}/${PROJECT_ID}/${REPO}/${IMAGE}"

# Point gcloud to the right project (we'll install gcloud next)
echo "${PROJECT_ID}"
```
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/8dfa60d1-cb38-4f7b-948f-7c613cdb61e9" />


---

## 🐳 Step 6: Install Docker on VM

SSH into the instance:
```bash
gcloud compute ssh ar-pay-demo --zone=$ZONE
```

Then inside the VM:
```bash
sudo apt update
sudo apt install -y docker.io (or) sudo apt-get install -y docker.io ca-certificates gnupg
sudo usermod -aG docker $USER
newgrp docker
```
Verify installation:
```bash
docker --version
```
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/49e84462-c311-422d-9e7b-70df6792bb7b" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/06056602-8ec5-4db4-b120-34f014c5a3e5" />


---

## 🧱 Step 7: Build, Tag, and Push Docker Image to Artifact Registry

**Create a simple Dockerfile**
```bash
cat <<EOF > Dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
EOF

echo "Hello from Artifact Registry!" > index.html
```

**Build image locally**
```bash
docker build -t payments-demo:v1 .
```
**Export Artifact Registry variables**
```bash
export AR_HOST=$REGION-docker.pkg.dev
export IMG_PATH=$AR_HOST/$PROJECT_ID/$REPO_NAME/payments-demo
```
**Tag image**
```bash
docker tag payments-demo:v1 $IMG_PATH:v1
```
**Authenticate Docker with Artifact Registry**
```bash
gcloud auth configure-docker $AR_HOST
```
**Push image**
```bash
docker push $IMG_PATH:v1
```

✅ If successful, verify from GCP Console → Artifact Registry → your repository.

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/1c7f9819-ba86-4bc1-a62b-0e1cf0002cac" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/304a3658-245b-45f6-a1c9-b059ea78b26e" />


<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/4bf67f0b-ad3c-4871-8ba8-760e9843b384" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/c8d403a0-7d30-4abb-a45b-fc4afaf21355" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/116db953-d038-4b37-85e3-c0f9ce89a183" />

---

## 🌟 Bonus: Useful Features of Artifact Registry

|Feature|	Description|
|-------|------------|
|Multi-region setup|	Reduce latency for distributed teams.|
|Encryption|	Choose between Google-managed keys or Cloud KMS keys.|
|Cleanup Policies|	Automatically delete untagged or old images.|
|IAM Control|	Fine-grained access — grant teams read/write to specific repos.|
|Image Scanning|	Built-in vulnerability scanning for containers.|

Example cleanup policy (Untagged images):
```bash
gcloud artifacts repositories update $REPO_NAME \
  --location=$REGION \
  --cleanup-policy=DELETE_UNTAGGED
```
---
## Troubleshooting (on the VM)
```
# Permission denied (push/pull):
# - Ensure VM is using SA: ar-pusher@${PROJECT_ID}.iam.gserviceaccount.com
# - Ensure repo-level role: roles/artifactregistry.writer for that SA
# - Re-run: gcloud auth configure-docker ${AR_HOST}

# "no basic auth credentials":
# - Use Option B docker login with access token OR re-run Option A

# "manifest unknown":
# - Check region/host, repo, image name, and tag
# - List images:
#   gcloud artifacts docker images list ${AR_HOST}/${PROJECT_ID}/${REPO} --include-tags
```
## 🧹 Cleanup Resources

After the demo, always clean up to avoid costs:
```bash
# Delete VM
gcloud compute instances delete ar-pay-demo --zone=$ZONE

# Delete repository
gcloud artifacts repositories delete $REPO_NAME --location=$REGION

# Delete service account
gcloud iam service-accounts delete ar-demo@$PROJECT_ID.iam.gserviceaccount.com
```

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/49ec1bd6-8471-4799-96c5-c4f44c566123" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/0f2f8150-6e00-423a-832a-97239879d9b9" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/ae3c2e31-cd08-42dc-9840-eb987fe10990" />





---

