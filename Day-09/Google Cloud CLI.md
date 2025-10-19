# Day-9: Google Cloud CLI (gcloud)

In earlier days, we built everything from the Google Cloud Console (UI). From Day-9 onward, we'll use the Google Cloud CLI (`gcloud`) to automate, script, and work like real DevOps/Cloud engineers.

## Learning Outcomes

- Understand how `gcloud` maps to the same APIs used by the Console.
- Set up configurations (project, region, zone) and authenticate correctly.
- Create, list, update, and delete core resources (Compute VM, Firewall, GCS bucket) via CLI.

## Prerequisites

- A Google Cloud project with billing enabled.
- Console access to verify results created via CLI (optional).
- **Practice Environment**:
  - **Recommended**: Cloud Shell (already has `gcloud` pre-installed).
  - **Local (optional)**: Install Cloud SDK [here](https://cloud.google.com/sdk/docs/install).
  
  **Note**: `gcloud components update` is not available in Cloud Shell; it’s for local SDK installs.

---

## Quick Start: Initialize and Configure

### Step 1: Initialize `gcloud` CLI
Run the following command to initialize your `gcloud` configuration:

```bash
gcloud init
