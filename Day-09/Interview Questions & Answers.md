# Google Cloud CLI (`gcloud`) - Interview Questions & Answers

A complete guide to help you prepare for cloud engineer, DevOps, or GCP-related interviews.

---

## 🧰 Basics of `gcloud`

### 1. **What is `gcloud` CLI?**
The `gcloud` CLI is a command-line tool provided by Google Cloud that enables users to manage GCP resources and services. It provides access to compute, storage, networking, and many other services using command-line commands.

---

### 2. **How do you install the `gcloud` CLI?**
You can install the CLI via the [Google Cloud SDK](https://cloud.google.com/sdk/docs/install):
```bash
curl https://sdk.cloud.google.com | bash
```
### 3. How do you authenticate and set up the gcloud CLI?
```bash
gcloud init
```
This command lets you:
- Authenticate with Google account
- Set the active project
- Choose default region and zone
4. How do you check the installed gcloud version?
```bash
gcloud version
```
## ⚙️ Configuration & Project Management
### 5. How do you list and set configurations in gcloud?
```bash
gcloud config list
gcloud config set project [PROJECT_ID]
gcloud config set compute/region [REGION]
gcloud config set compute/zone [ZONE]
```
### 6. How do you manage multiple configurations (profiles)?
```bash
gcloud config configurations create dev-config
gcloud config configurations activate dev-config
```
## 🖥️ Compute Engine (VMs)
### 7. How do you create a Compute Engine VM using gcloud?
```bash
gcloud compute instances create my-vm \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=debian-11 \
  --image-project=debian-cloud
```
### 8. How do you SSH into a VM using gcloud?
```bash
gcloud compute ssh my-vm --zone=us-central1-a
```
### 9. How do you stop, start, or delete a VM?
```bash
gcloud compute instances stop my-vm
gcloud compute instances start my-vm
gcloud compute instances delete my-vm
```
### 10. How do you list all VMs in a project?
```bash
gcloud compute instances list
```
## 📦 Cloud Storage (GCS)
### 11. How do you create a GCS bucket?
```bash
gcloud storage buckets create gs://my-bucket-name --location=US
```
### 12. How do you upload and download files from a bucket?
```bash
gcloud storage cp file.txt gs://my-bucket-name/
gcloud storage cp gs://my-bucket-name/file.txt .
```
### 13. How do you list files in a bucket?
```bash
gcloud storage ls gs://my-bucket-name
```
### 14. How do you delete a file or bucket?
```bash
gcloud storage rm gs://my-bucket-name/file.txt
gcloud storage buckets delete gs://my-bucket-name
```
## 🔐 IAM & Access Management
### 15. How do you add an IAM policy binding to a project?
```bash
gcloud projects add-iam-policy-binding [PROJECT_ID] \
  --member="user:email@example.com" \
  --role="roles/viewer"
```
### 16. How do you view IAM policies?
```bash
gcloud projects get-iam-policy [PROJECT_ID]
```
## 🔄 Automation & Scripting

### 17. Can gcloud be used in automation scripts?
Yes, gcloud is commonly used in shell scripts, CI/CD pipelines, and automation tools (e.g., GitHub Actions, Jenkins).

### 18. How do you pass variables in a shell script using gcloud?
```bash
PROJECT_ID=$(gcloud config get-value project)
gcloud compute instances list --project=$PROJECT_ID
```
## 🧪 Troubleshooting

### 19. What to do if gcloud throws authentication errors?

- Run gcloud auth login to re-authenticate
- Ensure correct account is active with gcloud config list account

### 20. How do you debug gcloud commands?

Use the --verbosity flag:
```bash
gcloud compute instances list --verbosity=debug
```

## 📚 Miscellaneous

### 21. How do you list all available services and APIs?
```bash
gcloud services list
gcloud services list --available
```

### 22. How do you enable an API in a project?
```bash
gcloud services enable compute.googleapis.com
```
### 23. How do you set the default account for gcloud?
```bash
gcloud config set account user@example.com
```
### 24. How do you list all active configurations and accounts?
gcloud auth list
gcloud config configurations list

### 25. What is the difference between gcloud, gsutil, and bq?

- gcloud: Main CLI tool for all GCP services
- gsutil: Focused on Google Cloud Storage operations
- bq: CLI for interacting with BigQuery

### 26. How do you uninstall gcloud CLI?

On Linux:
```bash
sudo rm -rf ~/google-cloud-sdk
```

On macOS (Homebrew):
```bash
brew uninstall --cask google-cloud-sdk
```

### 27. How do you impersonate a service account with gcloud?
```bash
gcloud auth activate-service-account --key-file=[KEY_PATH]
```

Or using:
```bash
gcloud auth print-access-token --impersonate-service-account=[EMAIL]
```
## 🧠 Scenario-Based Questions
### 28. How would you automate VM creation and setup for multiple environments?

- Use shell scripts with gcloud compute instances create
- Parameterize project, zone, and VM name
- Include startup-script metadata for bootstrapping

### 29. You need to grant a team viewer access to GCS only. How?

Assign roles/storage.objectViewer to their email:
```bash
gcloud projects add-iam-policy-binding [PROJECT_ID] \
  --member="user:team@example.com" \
  --role="roles/storage.objectViewer"
```
### 30. How would you handle gcloud auth in a CI/CD pipeline?

- Use a service account key
- Store it securely in secrets
- Authenticate via:
```bash
gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
```
