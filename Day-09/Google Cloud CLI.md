# Day-9: Google Cloud CLI (`gcloud`)

## Overview
The CLI helps automate processes, script workflows, and work efficiently as DevOps/Cloud Engineers.

**Learning Objectives:**
- With this, we can able to understand how the `gcloud` CLI interacts with the Google Cloud APIs used in the Console.
- How can we set up environment for project, region, and zone configurations.
- Learn how to manage resources like Compute Engine VMs and Cloud Storage (GCS) buckets directly from the command line.
- Understand basic resource lifecycle management (create, list, delete) using `gcloud`.

**Pre-requisites:**
- A Google Cloud project with billing enabled.
- Optional: Console access to verify resources created via CLI.
- **Practice environment**:
  - **Recommended**: Use Cloud Shell (already has `gcloud` pre-installed).
  - **Local machine (optional)**: Install Cloud SDK. [Instructions here](https://cloud.google.com/sdk/docs/install).

---

## Setting Up the Environment

### Step 1: Initialize `gcloud` CLI

To begin using the CLI, initialize `gcloud` on your local setup or Cloud Shell:

```bash
gcloud init
```
- This command will guide you through authenticating and selecting your Google Cloud project.

## Step 2: Verify Authenticated Accounts

You can list the accounts associated with your gcloud CLI using:

```bash
gcloud auth list
```

This helps confirm which account is active.

## Step 3: Review Current Configurations
To check the active configurations for project, region, and zone:

```bash
gcloud config list
```
## Step 4: Set Your Configuration

Set your project, region, and zone to align with the resources you will be working with:
```bash
PROJECT_ID="<YOUR_PROJECT_ID>"
REGION="us-central1"
ZONE="us-central1-a"

gcloud config set project ${PROJECT_ID}
gcloud config set compute/region ${REGION}
gcloud config set compute/zone ${ZONE}
```
## Creating and Managing a Compute VM
### Step 1: Create a Debian VM

To create a lightweight Debian VM, you can run the following command:
```bash
gcloud compute instances create day9-vm \
  --machine-type=e2-micro \
  --image-family=debian-12 \
  --image-project=debian-cloud
```
- machine-type=e2-micro: Smallest instance type suitable for testing and development.
- image-family=debian-12: Specifies the Debian 12 image.
- image-project=debian-cloud: Indicates the source project for the Debian image.
- machine-type=e2-micro: Smallest instance type suitable for testing and development.
- image-family=debian-12: Specifies the Debian 12 image.
- image-project=debian-cloud: Indicates the source project for the Debian image.

### Step 2: Get External IP Address

After the VM is created, get the external IP address using:
```bash
gcloud compute instances list --filter="name=day9-vm" --format="value(EXTERNAL_IP)"
```

This command lists the external IP of the VM you just created, which is essential for SSH and web access.

### Step 3: SSH into the VM

Once the VM is up, SSH into it using:
```bash
gcloud compute ssh day9-vm --zone=${ZONE}
```

This will open an SSH connection to your VM. If you're using Cloud Shell, it will automatically attach to the VM.

Clean Up: Delete the VM

Once you're done with your VM, clean up to avoid incurring unnecessary costs. To delete the VM:
```bash
gcloud compute instances delete day9-vm --zone=${ZONE}
```
Creating and Managing a Google Cloud Storage (GCS) Bucket

### Step 1: Create a GCS Bucket

To create a unique storage bucket, use the following:
```bash
BUCKET="day9-bucket-$RANDOM"
gcloud storage buckets create gs://${BUCKET} --location=${REGION}
```

The $RANDOM ensures a unique name for the bucket.

--location=${REGION} specifies where the bucket will be physically located.

### Step 2: Upload a File to the Bucket

Now, upload a file to your newly created bucket:
```bash
echo "hello from day9" > hello.txt
gcloud storage cp hello.txt gs://${BUCKET}/hello.txt
```

This command uploads a file named hello.txt to the GCS bucket.

### Step 3: List the Bucket Contents

To verify the file was uploaded correctly, list the contents of the bucket:
```bash
gcloud storage ls gs://${BUCKET}
```
### Step 4: Read the File from the Bucket

To view the content of hello.txt uploaded to the bucket:
```bash
gcloud storage cat gs://${BUCKET}/hello.txt
```
### Step 5: Delete the File and Bucket

To clean up, first delete the object and then the bucket:
```bash
gcloud storage rm gs://${BUCKET}/hello.txt
gcloud storage buckets delete gs://${BUCKET}
```




