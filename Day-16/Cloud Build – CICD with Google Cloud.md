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

## 3) Grant Cloud Build Permissions (Service Account)

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
### Grant Cloud Build service account permissions
```bash
PROJECT_NUMBER=$(gcloud projects describe cloud-build-project-476905 --format='value(projectNumber)')
gcloud projects add-iam-policy-binding cloud-build-project-476905 --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" --role="roles/artifactregistry.writer"
gcloud projects add-iam-policy-binding cloud-build-project-476905 --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" --role="roles/container.developer"
gcloud projects add-iam-policy-binding cloud-build-project-476905 --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" --role="roles/secretmanager.secretAccessor"
```

## 4) Sample App (repo structure)

Create a simple sample app — this is what you’ll push to GitHub.

```pgsql
google-cloud-build/             # repo root (GitHub)
├── index.js                    # sample Node.js app entry
├── package.json                # npm metadata & dependencies
├── Dockerfile                  # docker build instructions
├── cloudbuild.yaml             # Cloud Build pipeline (build + push + deploy)
├── deployment.yaml             # K8s Deployment manifest (applied by Cloud Build)
├── service.yaml                # K8s Service (LoadBalancer) manifest
└── README.md                   # (this file)
```
### `package.json`
```json
{
  "name": "cloudbuild-demo",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

### `index.js` (simple server)
```js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 8080;
app.get('/', (req, res) => res.send('🚀 Hello from Cloud Build demo!'));
app.listen(PORT, () => console.log(`Listening on ${PORT}`));
```

### `Dockerfile`
```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 8080
CMD ["npm", "start"]
```

### `deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
    spec:
      containers:
      - name: demo-app
        image: us-central1-docker.pkg.dev/cloud-build-project-476905/my-app-repo/myapp:latest
        ports:
        - containerPort: 8080
```

### `service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: demo-app-service
spec:
  type: LoadBalancer
  selector:
    app: demo-app
  ports:
  - port: 80
    targetPort: 8080
```

### `cloudbuild.yaml` (final working version)
```yaml
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-app-repo/myapp:latest', '.']

  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-app-repo/myapp:latest']

  - name: 'gcr.io/cloud-builders/gcloud'
    args: ['container', 'clusters', 'get-credentials', 'my-cluster', '--zone', 'us-central1-a']

  - name: 'gcr.io/cloud-builders/kubectl'
    env:
      - 'CLOUDSDK_COMPUTE_ZONE=us-central1-a'
      - 'CLOUDSDK_CONTAINER_CLUSTER=my-cluster'
    args: ['apply', '-f', 'deployment.yaml']

  - name: 'gcr.io/cloud-builders/kubectl'
    env:
      - 'CLOUDSDK_COMPUTE_ZONE=us-central1-a'
      - 'CLOUDSDK_CONTAINER_CLUSTER=my-cluster'
    args: ['apply', '-f', 'service.yaml']

images:
  - 'us-central1-docker.pkg.dev/$PROJECT_ID/my-app-repo/myapp:latest'

options:
  logging: CLOUD_LOGGING_ONLY
```

#### Notes:

- gcloud container clusters get-credentials obtains kubeconfig for kubectl.
- Cloud Build service account must have roles/container.developer (or more restrictive role with getCredentials rights and ability to patch deployments).
- Prefer to use a Kubernetes rollout strategy (e.g., kubectl rollout status) to validate successful updates — you can add a step to check rollout status.

---

## 5) Connect GitHub → Cloud Build (Trigger)
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

## 6. Troubleshooting & fixes you applied (log highlights)

- **Error:** `npm JSON.parse Unexpected end of JSON input`  
  **Fix:** `package.json` was empty/corrupt — replaced with valid JSON and re-committed.

- **Error:** `if 'build.service_account' is specified... must specify logs bucket`  
  **Fix:** Set `options.logging: CLOUD_LOGGING_ONLY` in `cloudbuild.yaml` to avoid requiring a logs bucket for custom SA use.

- **Error:** `deployments.apps "demo-app" not found`  
  **Fix:** Add `deployment.yaml` and `service.yaml` to repo and switch Cloud Build step from `kubectl set image` to `kubectl apply -f deployment.yaml` / `apply -f service.yaml`. Alternatively, created the deployment manually once using `kubectl apply`.

- **Error:** `No cluster is set` in kubectl step  
  **Fix:** Added `gcloud container clusters get-credentials` step before `kubectl` and added `env` values (`CLOUDSDK_COMPUTE_ZONE` and `CLOUDSDK_CONTAINER_CLUSTER`) to the kubectl steps so each step knows the cluster context.

---

## 7. Verification (how you confirmed success)
1. Cloud Build logs showed build & push completed with `digest: sha256:...`  
2. `kubectl get pods` showed `demo-app` pod in `Running` state.  
3. `kubectl get svc` showed `demo-app-service` with an **EXTERNAL-IP** (LoadBalancer).  
4. Opening `http://<EXTERNAL-IP>` in browser showed the running app response.  
5. Future commits automatically triggered Cloud Build (if trigger configured) and deployments updated the running app.

View logs in Cloud Console: Cloud Build → History (click a build → view logs).

---



