# 🧠 Day-17: Terraform in GCP – Question & Answer Summary

---

## ❓Q1. What is Terraform, and why is it used in GCP?

**Answer:**  
Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp.  
It allows you to define, provision, and manage cloud infrastructure (like Compute, Storage, Networking) declaratively using `.tf` files.  
In GCP, it helps automate resource creation and maintain infrastructure consistency using code.

---

## ❓Q2. What are the prerequisites for using Terraform in GCP?

**Answer:**
1. A **Google Cloud Project**
2. A **Service Account** with appropriate roles  
3. **Service Account Key file (`key.json`)**
4. A **Google Cloud Storage (GCS) bucket** for remote state management  
5. Terraform CLI installed or **Cloud Shell** environment

---

## ❓Q3. What is the purpose of the backend block in Terraform?

**Answer:**  
The `backend "gcs"` block in Terraform stores the state file (`terraform.tfstate`) remotely in Google Cloud Storage.  
This ensures:
- Shared access for teams  
- Centralized, versioned state management  
- Better reliability in case of Cloud Shell or local environment reset

Example:
```hcl
terraform {
  backend "gcs" {
    bucket = "aliveni-tf-state-terraform-in-gcp-477106"
    prefix = "terraform/state"
  }
}
```

---

## ❓Q4. Why did we get the “Permission denied” and “Bucket not found” errors?

**Answer:**
This error occurred because the active gcloud account was the service account itself, not the owner user account.

Fix:
```
gcloud auth login
gcloud config set account alivenidevops@gmail.com
```

Then add the required IAM bindings again for the Terraform service account.

---

## ❓Q5. Which IAM roles are required for the Terraform Service Account?

**Answer:**
At minimum:

- roles/editor
- roles/storage.admin
- roles/storage.objectAdmin

Optional (for advanced use):

- roles/iam.serviceAccountUser
- roles/compute.admin

## ❓Q6. How to create and activate a Terraform Service Account in GCP?

**Answer:**
```
gcloud iam service-accounts create terraform-sa \
  --display-name "Terraform Service Account"

gcloud iam service-accounts keys create key.json \
  --iam-account terraform-sa@${PROJECT_ID}.iam.gserviceaccount.com

export GOOGLE_APPLICATION_CREDENTIALS="$PWD/key.json"
gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
```

---

## ❓Q7. What is the purpose of a GCS bucket in Terraform?

Answer:
- It is used as a remote backend to store the Terraform state file.
- Terraform reads and updates this file during each plan or apply operation to keep track of managed infrastructure.

---

## ❓Q8. How to give a Service Account access to the GCS bucket?

**Answer:**
```
gcloud storage buckets add-iam-policy-binding gs://$BUCKET_NAME \
  --member="serviceAccount:terraform-sa@${PROJECT_ID}.iam.gserviceaccount.com" \
  --role="roles/storage.admin"

gcloud storage buckets add-iam-policy-binding gs://$BUCKET_NAME \
  --member="serviceAccount:terraform-sa@${PROJECT_ID}.iam.gserviceaccount.com" \
  --role="roles/storage.objectAdmin"
```

---

## ❓Q9. What are the main resources created in this project?

**Answer:**

1. Compute Engine VM
2. Firewall Rule (allow SSH)
3. Storage Bucket

Defined in Terraform as:
```
resource "google_compute_instance" "demo_vm" { ... }
resource "google_compute_firewall" "allow_ssh" { ... }
resource "google_storage_bucket" "demo_bucket" { ... }
```

---

## ❓Q10. What was the meaning of the 409 Conflict error during Terraform Apply?

**Answer:**

It means the GCS bucket already exists.
Terraform tried to create the same-named bucket again, but since the name is globally unique in GCS, the request failed with a 409 Conflict.
✅ This does not affect other resources; you can ignore or change the name.

---

## ❓Q11. What is the Terraform workflow in GCP?

**Answer:**

|Step|	Command|	Description|
|----|---------|-------------|
|1|	terraform init|	Initialize the provider and backend|
|2|	terraform plan|	Preview infrastructure changes|
|3|	terraform apply|	Apply and create resources|
|4|	terraform show|	View deployed resource details|
|5|	terraform destroy| Delete all resources|

---

## ❓Q12. How to confirm resources are created?

**Answer:**
```bash
gcloud compute instances list
gcloud compute firewall-rules list
gsutil ls
```
---

## ❓Q13. What are Terraform outputs used for?

**Answer:**

Outputs are used to display key resource information after terraform apply.

**Example:**
```bash
output "vm_public_ip" {
  value = google_compute_instance.demo_vm.network_interface[0].access_config[0].nat_ip
}
```
---

## ❓Q14. What are the benefits of using Terraform with GCP?

**Answer:**

- Automates infrastructure provisioning
- Enables version-controlled infrastructure
- Reduces manual errors
- Promotes infrastructure reusability
- Supports multi-cloud automation

---

## ❓Q15. What did we achieve on Day-17?

**Answer:**

✅ Created a GCP service account and enabled authentication

✅ Created a GCS backend for remote state management

✅ Configured Terraform to use the backend

✅ Deployed Compute Engine, Firewall, and Bucket resources

✅ Resolved IAM permission errors and bucket conflicts

---

