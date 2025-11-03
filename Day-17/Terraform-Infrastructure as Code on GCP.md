# Day-17 of GCP : Terraform-Infrastructure as Code on GCP

## 🎯 What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool developed by HashiCorp that allows you to define, provision, and manage your cloud infrastructure using declarative configuration files.

## 🧩 Why Terraform?

|Feature|	Description|
|-------|------------|
|Multi-Cloud Support|	Works with AWS, GCP, Azure, OCI, VMware, etc.|
|Declarative Syntax|	You tell Terraform what you want, not how to do it.|
|Reusable Code|	Use variables, modules, and templates for reusability.|
|Version Control| Friendly	Store your .tf files in GitHub for collaboration.|
|State Management|	Tracks existing infrastructure in a state file.|
|Automation|	terraform apply creates everything automatically.|

## 🏗️ Terraform Architecture
```
+-------------------+
|  Configuration    |   ->  .tf files
|  (.tf scripts)    |
+-------------------+
           |
           v
+-------------------+
|  Terraform CLI    |   ->  terraform plan/apply/destroy
+-------------------+
           |
           v
+-------------------+
|  Providers        |   ->  AWS / GCP / Azure / Kubernetes
+-------------------+
           |
           v
+-------------------+
|  Target Platform  |   ->  Cloud Resources
+-------------------+

```
---

## ⚙️ Terraform Key Concepts
|Concept|	Description|
|-------|------------|
|Providers|	Plugin to interact with APIs of cloud platforms (like google, aws).|
|Resources|	The actual components (VM, bucket, network, etc.).|
|Variables|	Input parameters for customization.|
|Outputs|	Display key information after deployment (like IP, bucket name).|
|State File|	Keeps track of created resources (default: terraform.tfstate).|
|Modules|	Reusable groups of Terraform configuration.|
|Backend|	Defines where the state file is stored (local or remote like GCS/S3).|

---

## 🧱 Terraform Workflow

|Step|	Command	Description|
|----|---------------------|
|1️⃣	terraform init|	Initializes directory, installs providers, configures backend.|
|2️⃣	terraform plan|	Shows what Terraform will create/change/destroy.|
|3️⃣	terraform apply|	Actually builds the infrastructure.|
|4️⃣	terraform show|	Displays current resource state.|
|5️⃣	terraform destroy|	Deletes all managed resources.|

---

## 🧰 Terraform Installation

**Option 1 – Ubuntu:**
```bash
sudo apt-get update && sudo apt-get install -y unzip
wget https://releases.hashicorp.com/terraform/1.9.6/terraform_1.9.6_linux_amd64.zip
unzip terraform_1.9.6_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform -version
```

**Option 2 – Google Cloud Shell:**
Terraform is pre-installed!
✅ Just run:
```bash
terraform -version
```
---

## 🔑 GCP Setup for Terraform

### 1. Select Project
```
gcloud config set project <PROJECT_ID>
```

### 2. Enable APIs
```
gcloud services enable compute.googleapis.com storage.googleapis.com
```

### 3. Create Service Account
```
gcloud iam service-accounts create terraform-sa --display-name="Terraform SA"
```

### 4. Grant Permissions
```
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member="serviceAccount:terraform-sa@<PROJECT_ID>.iam.gserviceaccount.com" \
  --role="roles/editor"
```

### 5. Generate Key
```
gcloud iam service-accounts keys create key.json \
  --iam-account=terraform-sa@<PROJECT_ID>.iam.gserviceaccount.com
export GOOGLE_APPLICATION_CREDENTIALS="key.json"

```

---

## 🧩 Terraform Configuration Files for GCP

**🔹 main.tf**

```
provider "google" {
  project     = var.project_id
  region      = var.region
  zone        = var.zone
  credentials = file("key.json")
}

# Create a storage bucket
resource "google_storage_bucket" "demo_bucket" {
  name     = "aliveni-tf-demo-bucket"
  location = var.region
}

# Create a Compute Engine VM
resource "google_compute_instance" "demo_vm" {
  name         = "aliveni-demo-vm"
  machine_type = "e2-micro"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-12"
    }
  }

  network_interface {
    network = "default"
    access_config {}
  }
}

# Create Firewall rule
resource "google_compute_firewall" "allow_ssh" {
  name    = "allow-ssh"
  network = "default"

  allow {
    protocol = "tcp"
    ports    = ["22"]
  }

  source_ranges = ["0.0.0.0/0"]
}
```
---

**🔹 variables.tf**
```
variable "project_id" {
  description = "GCP Project ID"
  default     = "<YOUR_PROJECT_ID>"
}

variable "region" {
  default = "asia-south1"
}

variable "zone" {
  default = "asia-south1-a"
}
```
---

**🔹 outputs.tf**
```
output "vm_public_ip" {
  value = google_compute_instance.demo_vm.network_interface[0].access_config[0].nat_ip
}

output "bucket_name" {
  value = google_storage_bucket.demo_bucket.name
}
```
---

**🔹 backend.tf – Store State File in GCS**
```
terraform {
  backend "gcs" {
    bucket = "my-terraform-state-bucket"
    prefix = "terraform/state"
  }
}
```

| 💡 Create bucket once manually:
```bash
gsutil mb -l asia-south1 gs://my-terraform-state-bucket/
```
---

## 🧭 Running Terraform

**Step 1: Initialize**
```
terraform init
```

Downloads providers and configures backend.

---

**Step 2: Plan**
```
terraform plan
```

Previews what Terraform will do.

---

**Step 3: Apply**

```
terraform apply -auto-approve
```
Creates all resources in GCP.

---

## 🧩 Output

Example output:

Plan: 3 to add, 0 to change, 0 to destroy.
```
Changes to Outputs:
  + bucket_name  = "aliveni-tf-demo-bucket"
  + vm_public_ip = (known after apply)
```

✅ VM and Firewall were created successfully.
⚠️ Bucket creation gave a 409 Conflict because it already exists — this is harmless. You can rename and reapply if needed.

---

## ✅ Verify in:

- GCP Console → Compute Engine → VM instances
- GCP Console → Storage → Buckets
- GCP Console → VPC → Firewall rules

---

**Step 4: Destroy**
```
terraform destroy -auto-approve
```
Removes everything cleanly.

---
