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

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/63e650c8-4644-4fda-8512-e68aa5bfcc95" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/cc280018-6303-421f-a9ea-318fbea2f536" />

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
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/641d56c7-a229-44cb-85cc-19e4602647b7" />

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

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/6d551689-88ee-4936-971b-8d8f677275be" />

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
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/5247696b-34aa-4bdd-862a-c54fcd7ddc0b" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/2084cd86-1c72-4c69-8a63-0875c4c9a2a5" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/cee6df98-4bde-413a-93b0-7ece3cdf500d" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/f092685c-27f2-45a2-bf68-736a037a49ba" />

---
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


<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/fd9bcaf3-3b24-48dc-a5bf-3311128a202c" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/6761fc30-57d5-4ee2-b77c-06b05c4bf034" />

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

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/3ebbf10f-fe4d-484f-b069-4d5aadb2a32e" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/47d9bc10-071c-482d-bfa0-713a7ad7a765" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/041cdb5d-a8f5-4bae-9aaf-67d476c72058" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/fe7ba058-c5a3-4773-8707-804554e3d1f7" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/0948f22e-0f38-4ed5-9282-17c1b418d6c4" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/6db8c5d6-7391-4f1d-acbf-4c05531fd55e" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/c5dc469a-3b71-46f3-8747-b125325eb9dc" />


## Ah! Build failed

## ~Now come to Troubleshooting part~

### Error

<img width="1159" height="591" alt="image" src="https://github.com/user-attachments/assets/4f239977-6217-43de-b625-c3f9f2d41c9b" />

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

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/072ed3b2-05ff-4fce-8037-02e2cdd99fed" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/0f9de1a0-530d-4bf7-90d8-c46e50b69959" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/b66c48d6-8dcb-4f70-8460-37a2eef9fbe2" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/0159a13b-a8d5-495c-bb9a-971bf665641e" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/c606f8d7-c1b8-4254-bdc7-5bbbde797549" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/297b723b-dee5-496d-97a0-3df69b7950dd" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/16e4da01-d238-4cc5-8abb-ab19c23e22ec" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/a4cca6f1-55a0-4d2f-b9b5-7cfff8cbe897" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/d30473c4-e70e-41c6-af26-b4a7509d8a95" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/6e5a66d5-4f4c-48e8-a0be-5cdd4ede0df3" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/2c254e4c-24a6-4a3b-bcc0-fa91a21b7b84" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/9bcb1862-c700-4591-a177-5fe5360235a0" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/6ecaab2d-8bdd-41f6-8d8c-ecf9d7e84e40" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/e93003d5-9352-4e0e-bac8-ad907f1bd04b" />

---

## 7. Verification (how you confirmed success)
1. Cloud Build logs showed build & push completed with `digest: sha256:...`  
2. `kubectl get pods` showed `demo-app` pod in `Running` state.  
3. `kubectl get svc` showed `demo-app-service` with an **EXTERNAL-IP** (LoadBalancer).  
4. Opening `http://<EXTERNAL-IP>` in browser showed the running app response.  
5. Future commits automatically triggered Cloud Build (if trigger configured) and deployments updated the running app.

View logs in Cloud Console: Cloud Build → History (click a build → view logs).

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/69803239-1ab6-4e74-923b-9b883207d676" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/f239e810-3fcc-4081-88df-758b4a7027f2" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/78bbc6f9-467a-438c-b7f1-0a2ebc166d95" />



---



