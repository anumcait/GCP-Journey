# 🚀 Day 16: Cloud Build – CI/CD with Google Cloud

Here  we will learn how to **automate builds, tests, and deployments** using **Google Cloud Build** — Google’s managed CI/CD platform.

## 🎯 Objectives
- Enable required Google Cloud APIs and permissions.
- Create an Artifact Registry repository (or use Container Registry).
- Write cloudbuild.yaml to build & push Docker images.
- Configure a GitHub trigger that starts builds on push.
- (Bonus) Add automated deployment to GKE in the same pipeline.


## 🧩 1. Introduction to Cloud Build

Cloud Build is Google’s serverless (fully managed) CI/CD platform.
Think of it like GitHub Actions or Jenkins, but you don’t manage any servers.

It:

- Builds your code in isolated containers automatically on each commit.
- Run tests or security scans.
- Pushes your Docker image to Artifact Registry
- And can even deploy to GKE, Cloud Run, or Compute Engine.

💡 You just define what to do in a YAML file — Google Cloud takes care of how it’s done.

💡 It integrates seamlessly with GitHub, GitLab, and Bitbucket.

---

## 🔧 Prerequisites

- Google Cloud Project with billing enabled.
- gcloud CLI installed and authenticated:
```bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```
- GitHub repository with your app source.
- (Optional) kubectl installed if deploying to GKE locally.

Replace YOUR_PROJECT_ID, YOUR_REGION, and other placeholders as you go.

---

### 1) Enable Required APIs

Run:
```bash
gcloud services enable \
  cloudbuild.googleapis.com \
  artifactregistry.googleapis.com \
  container.googleapis.com \
  iam.googleapis.com
```

> If you prefer Container Registry (legacy), enable containerregistry.googleapis.com instead of Artifact Registry.

## 2) Create an Artifact Registry (to store Docker images)

Artifact Registry is the recommended modern registry.
```bash
gcloud artifacts repositories create my-app-repo \
  --repository-format=docker \
  --location=us-central1 \
  --description="Docker repository for my Cloud Build demo"
```
✅ This will create a private Docker image repo under Artifact Registry.
You’ll use it to store your image after Cloud Build builds it.

💡 Note the repo location (e.g., us-central1-docker.pkg.dev/PROJECT_ID/my-repo/...) — you will use this in your cloudbuild.yaml.

---

3) Grant Cloud Build Permissions (Service Account)

Cloud Build uses a service account: PROJECT_NUMBER@cloudbuild.gserviceaccount.com. Grant it permissions to push to Artifact Registry and to cluster-admin (if deploying to GKE).

Grant Artifact Registry writer:

```bash
gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
  --member="serviceAccount:$(gcloud projects describe YOUR_PROJECT_ID --format='value(projectNumber)')@cloudbuild.gserviceaccount.com" \
  --role="roles/artifactregistry.writer"
```
If deploying to GKE, also grant Kubernetes permissions (limit to what’s required in production):
```bash
# Give Cloud Build permission to access GKE & update deployments
gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
  --member="serviceAccount:$(gcloud projects describe YOUR_PROJECT_ID --format='value(projectNumber)')@cloudbuild.gserviceaccount.com" \
  --role="roles/container.developer"
```

4) Sample App (repo structure)

Create a simple sample app — this is what you’ll push to GitHub.

```pgsql
cloudbuild-demo/
├── app/
│   ├── server.js
│   ├── package.json
├── Dockerfile
└── cloudbuild.yaml
```
`app/server.js`
```js
const express = require('express');
const app = express();
const port = process.env.PORT || 8080;
app.get('/', (req, res) => res.send('Hello from Cloud Build CI/CD!'));
app.listen(port, () => console.log(`Listening on ${port}`));
```

`package.json`
```json
{
  "name": "cloudbuild-demo",
  "version": "1.0.0",
  "main": "server.js",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

`Dockerfile`
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY app/package*.json ./
RUN npm install --production
COPY app/ ./
EXPOSE 8080
CMD ["node", "server.js"]
```
## 5) cloudbuild.yaml — Basic: Build & Push image to Artifact Registry

Place this at repo root (cloudbuild.yaml):
```yaml
# cloudbuild.yaml — Build and push to Artifact Registry
substitutions:
  _REPO_LOCATION: "us-central1"
  _REPO_NAME: "my-repo"

steps:
  - id: "Build"
    name: 'gcr.io/cloud-builders/docker'
    args:
      [
        'build',
        '-t',
        '${_REPO_LOCATION}-docker.pkg.dev/$PROJECT_ID/${_REPO_NAME}/myapp:$SHORT_SHA',
        '.'
      ]

  - id: "Push"
    name: 'gcr.io/cloud-builders/docker'
    args:
      [
        'push',
        '${_REPO_LOCATION}-docker.pkg.dev/$PROJECT_ID/${_REPO_NAME}/myapp:$SHORT_SHA'
      ]

images:
  - '${_REPO_LOCATION}-docker.pkg.dev/$PROJECT_ID/${_REPO_NAME}/myapp:$SHORT_SHA'
```
Key points:

- $SHORT_SHA is provided by Cloud Build — great for image tagging.
- You can customize _REPO_LOCATION and _REPO_NAME via trigger substitutions.

6) cloudbuild.yaml — Bonus: Build → Push → Deploy to GKE

If you want CI→CD: build, push, then update a Deployment in GKE:
```yaml
# cloudbuild-gke.yaml — Build, push, and deploy to GKE
substitutions:
  _REPO_LOCATION: "us-central1"
  _REPO_NAME: "my-repo"
  _CLUSTER_NAME: "my-gke-cluster"
  _CLUSTER_ZONE: "us-central1-a"
  _K8S_DEPLOYMENT: "myapp-deployment"
  _K8S_CONTAINER: "myapp-container"

steps:
  - id: "Build"
    name: 'gcr.io/cloud-builders/docker'
    args:
      ['build', '-t', '${_REPO_LOCATION}-docker.pkg.dev/$PROJECT_ID/${_REPO_NAME}/myapp:$SHORT_SHA', '.']

  - id: "Push"
    name: 'gcr.io/cloud-builders/docker'
    args:
      ['push', '${_REPO_LOCATION}-docker.pkg.dev/$PROJECT_ID/${_REPO_NAME}/myapp:$SHORT_SHA']

  - id: "Get credentials"
    name: 'gcr.io/cloud-builders/gcloud'
    entrypoint: bash
    args:
      [
        '-c',
        'gcloud container clusters get-credentials ${_CLUSTER_NAME} --zone ${_CLUSTER_ZONE} --project $PROJECT_ID'
      ]

  - id: "kubectl-set-image"
    name: 'gcr.io/cloud-builders/kubectl'
    args:
      [
        'set',
        'image',
        'deployment/${_K8S_DEPLOYMENT}',
        '${_K8S_CONTAINER}=${_REPO_LOCATION}-docker.pkg.dev/$PROJECT_ID/${_REPO_NAME}/myapp:$SHORT_SHA',
        '--namespace=default'
      ]

images:
  - '${_REPO_LOCATION}-docker.pkg.dev/$PROJECT_ID/${_REPO_NAME}/myapp:$SHORT_SHA'

```

Notes:

- gcloud container clusters get-credentials obtains kubeconfig for kubectl.
- Cloud Build service account must have roles/container.developer (or more restrictive role with getCredentials rights and ability to patch deployments).
- Prefer to use a Kubernetes rollout strategy (e.g., kubectl rollout status) to validate successful updates — you can add a step to check rollout status.

---

7) Connect GitHub → Cloud Build (Trigger)
Via Cloud Console (recommended)

1. Go to Cloud Console → Cloud Build → Triggers.
2. Click Connect Repository → choose GitHub (use GitHub App).
3. Authorize the Google Cloud Build app in GitHub and select your repository.
4. Create a trigger:
  - Event: Push to a branch (e.g., main).
  - Build configuration: Use cloudbuild.yaml in repo.
  - Optionally set substitutions (_REPO_LOCATION, _REPO_NAME, etc.)

Via gcloud (example)
```bash
gcloud beta builds triggers create github \
  --name="trigger-main-cloudbuild" \
  --repo-owner="GITHUB_USER_OR_ORG" \
  --repo-name="cloudrun-demo" \
  --repo-type="github" \
  --branch-pattern="^main$" \
  --build-config="cloudbuild.yaml"
```

After setup, pushing to main will start Cloud Build automatically.

---

8) Run & Verify a Build

You can trigger manually:
```bash
# trigger a build using current repo
gcloud builds submit --config=cloudbuild.yaml --substitutions=_REPO_LOCATION=us-central1,_REPO_NAME=my-repo
```
List builds:
```bash
gcloud builds list --limit=10
```

Describe a build for logs & status:
```bash
gcloud builds describe BUILD_ID
```
View logs in Cloud Console: Cloud Build → History (click a build → view logs).


