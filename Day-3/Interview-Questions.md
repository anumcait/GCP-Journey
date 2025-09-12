## 🎤 Interview Questions (With Answers)
**1. What is IAM in GCP?**

👉 IAM is Identity and Access Management — controls who can do what on which resources in GCP.

**2. Difference between Basic, Predefined, and Custom Roles?**

Basic → Broad legacy roles (Owner, Editor, Viewer)

Predefined → Fine-grained, service-specific (e.g., roles/compute.admin)

Custom → You define your own set of permissions

**3. What is the IAM hierarchy in GCP?**

Levels: Organization → Folder → Project → Resource

Permissions inherit downward.

**4. What is a Service Account and why is it important?**

👉 A non-human identity used by apps or pipelines.

Example: CI/CD pipeline deploys to GKE using a service account.

Importance: enables automation + security.

**5. Explain the principle of least privilege in IAM.**

👉 Always give minimum permissions needed for a task.

Example: Instead of Owner, give roles/storage.objectViewer if the user only needs to view objects.

**6. How do you audit IAM in GCP?**

Use Cloud Audit Logs

Review IAM policy bindings

Use IAM Recommender to detect excessive permissions.

**7. How do IAM policies propagate in GCP?**

👉 They are inherited down the hierarchy unless overridden.

**8. In DevOps, how would you use IAM for CI/CD pipelines?**

Create a Service Account

Assign minimum required roles

Use the service account credentials in pipeline jobs

**9. Difference between a User account and a Service account?**

User = Human identity (managed by Google Workspace/Cloud Identity)

Service Account = Non-human identity for automation

**10. What are common IAM mistakes?**

Assigning Basic Roles

Giving excessive permissions

Not rotating service account keys

Not auditing access regularly

##
