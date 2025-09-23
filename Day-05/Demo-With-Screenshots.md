## 🚀 Hands-On Demo: Google Cloud Storage with Compute Engine VM
In this demo, we will connect Google Cloud Storage (GCS) with a Compute Engine VM using the Google Cloud Console (UI only).
You will learn how to create a bucket, set up IAM with a service account, launch a VM, and securely access storage from inside the VM.

### 🔑 Learning Objectives
By completing this demo, you will:

- Create a Cloud Storage bucket.
- Create and configure a Service Account for secure access.
- Launch a Compute Engine VM with the Service Account attached.
- Use the VM to read and write objects in the bucket.
- Clean up resources when finished.

### 📝 Step-by-Step Instructions
#### Step 1: Create a Cloud Storage Bucket
1. Go to the **Google Cloud Console → Navigation Menu (☰) → Cloud Storage → Buckets.**
2. Click Create bucket.
3. Enter a unique name (e.g., aliveni-first-storage-bucket).
    - Bucket names must be globally unique across GCP.
<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/ef9ea075-6ce8-4701-9e19-bb578bcb9c8f" />

4. Choose Location type → Region and pick a region close to you (e.g., asia-south1 (Mumbai)).
5. Leave the Storage class as Standard or as per requirement.
   <img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/585853b7-4c79-403e-b169-32005afef801" />
   <img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/027057cf-b019-4ab4-89c8-e151e81fd3f4" />
6. Under Access control, keep it at Uniform (recommended).
7. Click Create.

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/850408e1-d189-42d8-855a-719f93c332bc" />


✅ You now have a storage bucket ready.

---

### Step 2: Create a Service Account
1. Navigate to Navigation Menu → IAM & Admin → Service Accounts.
2. Click Create Service Account.
<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/25babf3a-5ffd-48e9-9cfd-c082db87df4a" />
<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/5222b5d9-59bc-4ef9-b878-0d101f0baa2a" />

3. Enter the name: <your-sa-name>.
4. Click **Create and continue**.
5. In **Grant this service account access to project**, select the role:
  - **Storage → Storage Object** Admin (allows the VM to read/write bucket objects).
6. Click **Continue → Done.**
✅ A service account is now available for attaching to your VM.

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/bdb1b821-fabf-4458-964a-47510f6072c8" />

---

### Step 3: Launch a Compute Engine VM
1. Go to Navigation Menu → Compute Engine → VM Instances.
2. Click Create Instance.
3. Configure:
     - Name: gcs-demo-vm.
     - Region/Zone: Same as your bucket (for lower latency).
4. Machine type: e2-micro (eligible for free tier).
5. Boot disk: Keep the default (Debian/Ubuntu).
6. Scroll down to Identity and API access:
7. Under Service account, choose <your-sa-name>.
For Access scopes, choose Allow full access to all Cloud APIs (simpler for this demo).
Click Create.
✅ You now have a VM that can access GCS without any keys.

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/f49eee32-bbe2-48dd-b6f2-efaf2e476788" />

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/a07e3551-8bdc-4822-bd4e-e17a68072fb3" />

<img width="1050" height="536" alt="image" src="https://github.com/user-attachments/assets/8954a1d5-ea45-417e-b11a-5ec93db40f7e" />

<img width="1050" height="535" alt="image" src="https://github.com/user-attachments/assets/a4e99ea2-c0b0-4d1e-bc7d-1df80ffec7a8" />


---

### Step 4: Upload a File to Your Bucket
Go to Cloud Storage → Buckets → your bucket name.
Click Upload Files.
Select a local file (e.g., Welcome.txt).
Verify that the file is visible in the bucket.
✅ This file will be accessed from the VM.

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/640707f7-4e1d-4e80-aabe-6a36b690d9fa" />

---

### Step 5: Access the Bucket from the VM
Go to Compute Engine → VM instances.

Click SSH next to your VM (<your-vm>) to open a browser terminal.

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/616c48fe-b5fd-4d2f-8c8f-fb64491c0d3d" />
<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/79d5e73b-4428-4d56-a9e9-ff0ecab0f404" />

Inside the VM terminal, run the following commands:

List files in your bucket:
```
gcloud storage ls gs://<your-bucket-name>
```
Download the uploaded file:
```
gcloud storage cp gs://<your-bucket-name>/hello.txt hello-vm.txt
cat hello-vm.txt
```
Upload a new file back to GCS:
```
echo "Hello from Compute Engine VM" > vm-upload.txt
gcloud storage cp vm-upload.txt gs://<your-bucket-name>/vm-upload.txt
```
Go back to the Cloud Storage Console and confirm that vm-upload.txt is present in your bucket.

✅ The VM can now read and write objects to GCS using its service account.

<img width="1050" height="534" alt="image" src="https://github.com/user-attachments/assets/7cc04faf-d848-4647-a705-03a14dda796b" />

### Step 6: Cleanup Resources
When done, clean up to avoid charges:

Delete the VM → Go to Compute Engine → VM Instances → Select VM → Delete.
Delete the Bucket → Go to Cloud Storage → Buckets → Select Bucket → Delete.
Delete the Service Account → Go to IAM & Admin → Service Accounts → Delete.
If this project was created just for testing, you can delete the entire project, which removes all resources.

<img width="1049" height="517" alt="image" src="https://github.com/user-attachments/assets/d90bb474-8a91-4019-a2f5-3b83bcf40c04" />

<img width="1050" height="526" alt="image" src="https://github.com/user-attachments/assets/1a45b786-90d0-4c6a-b846-ffa2160aa88e" />

<img width="1042" height="524" alt="image" src="https://github.com/user-attachments/assets/0e939f00-f39a-42a7-b7a4-3544330f0be7" />

## 🎯 Key Takeaways
- Cloud Storage is the default choice for storing objects like logs, backups, and artifacts.
- Service Accounts provide secure, keyless authentication for applications and VMs.
- Compute Engine VMs can interact with GCS easily when IAM roles are attached.
- Always clean up resources after hands-on work to avoid unexpected billing.
