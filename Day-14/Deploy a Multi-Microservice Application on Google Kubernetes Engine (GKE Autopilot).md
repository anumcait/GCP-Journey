# Day-14 : Deploy a Multi-Microservice Application on Google Kubernetes Engine (GKE Autopilot)

## 🧠 Overview

We’ll deploy a multi-language microservice application (the Docker voting app) on GKE Autopilot.
Autopilot manages the control plane and nodes automatically — meaning you only focus on your workloads, not infrastructure.

---
<img width="696" height="542" alt="image" src="https://github.com/user-attachments/assets/78d90545-d15b-46e9-9a60-7fd66ec28591" />





## 🏗 Application Architecture

The demo application is a voting app with five services:

**Microservices Overview**

|Service|	Language|	Purpose|	Exposed?|
|-------|---------|--------|----------|
|vote|	Python (Flask)|	Frontend web app for voting between Cats and Dogs|	✅ Public
|result|	Node.js|	Displays live voting results|	✅ Public
|worker|	.NET|	Consumes votes from Redis and stores them into PostgreSQL|	❌ Internal
|redis|	Redis(cache)|Redis	Caching layer for temporary vote storage|	❌ Internal
|db|	PostgreSQL|	Stores persistent voting results|	❌ Internal

---

## Data Flow Diagram

```
+---------+      +---------+      +---------+      +-----------+      +-----------+
|  Vote   | ---> |  Redis  | ---> |  Worker | ---> | PostgreSQL| ---> |  Result   |
+---------+      +---------+      +---------+      +-----------+      +-----------+
      ↑                                                                       |
      └───────────────────────────────────────────────────────────────────────┘
```
---

## ⚙️ Pre-requisites

Make sure you have:

- A **Google Cloud** account
- **Billing** enabled
- **GCP Project** created
- **Cloud Shell** (recommended)
- Pre-installed: `gcloud`, `kubectl`, `docker`, `helm`

---

## 🧩 Step 1 — Enable GKE API
Enable the container API for GKE:
```bash
gcloud services enable container.googleapis.com
```

## ☁️ Step 2 — Create GKE Autopilot Cluster

Run the following command to create an Autopilot cluster:
```bash
gcloud container clusters create-auto demo-autopilot-cluster \
  --region us-central1
```
> ⏱️ This may take 10–15 minutes.  
> Autopilot manages scaling and node provisioning automatically.

---

## 🧩 Step 3 — Understand Microservices

Clone the official Docker Voting App
:
```bash
git clone https://github.com/dockersamples/example-voting-app.git
cd example-voting-app
```
The project contains:
```
vote/        → Python Flask app
result/      → Node.js app
worker/      → .NET worker service
redis/       → Redis cache
db/          → PostgreSQL database
```
---

## 🐳 Step 4 — Dockerizing the Application

Each service has its own Dockerfile.

### 🐍 vote (Python Flask)
```
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 80
CMD ["python", "app.py"]
```

### 🧩 result (Node.js)
```
FROM node:18-slim
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 80
CMD ["npm", "start"]
```

### ⚙️ worker (.NET)
```
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /app
COPY . .
RUN dotnet restore && dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/runtime:6.0
WORKDIR /app
COPY --from=build /app/out .
CMD ["dotnet", "Worker.dll"]
```
---

## 🧪 Step 5 — Test Locally using Docker Compose

Before deploying to Kubernetes, test locally:
```bash
docker compose up
```
Once up:

- Voting App → http://localhost:8080
- Results App → http://localhost:8081

You should see:

- Vote between 🐱 Cats and 🐶 Dogs
- Real-time updates in results page

To stop:
```bash
docker compose down
```
---

## ☸️ Step 6 — Deploy on Kubernetes (GKE Autopilot)

### Troubleshooting Part
#### Connect kubectl to Your GKE Cluster

List your clusters
Run this to see your cluster name and location:

```bash
gcloud container clusters list --project gke-project-476409
```

You’ll see output similar to:
```nodejs
NAME             LOCATION       MASTER_VERSION   STATUS
autopilot-cluster-1  us-central1    1.30.3-gke.100    RUNNING
```
#### Get credentials for your cluster
Now connect your kubectl to that cluster:

```bash
gcloud container clusters get-credentials autopilot-cluster-1 --region us-central1 --project gke-project-476409
```
(Replace cluster name and region with yours if different.)

This command will:

- Download the cluster credentials
- Update your local kubeconfig
- Make kubectl talk to the right GKE control plane

#### Test the connection

Now try again:
```bash
kubectl get nodes
```
> Initially you will get no resources found , because no deployent is there yet

<img width="1050" height="519" alt="image" src="https://github.com/user-attachments/assets/ebc0f174-a798-4aa2-bef3-70bf922c9367" />

If you want test with sample nginx application

```bash
kubectl create deployment nginx --image=nginx
```
<img width="1050" height="81" alt="image" src="https://github.com/user-attachments/assets/377bc959-1c44-44fe-b20f-8e5d4524a13e" />



Apply all manifests:
```bash
kubectl apply -f k8s-specifications/
```
This folder includes:

- vote-deployment.yaml
- vote-service.yaml
- result-deployment.yaml
- result-service.yaml
- worker-deployment.yaml
- redis-deployment.yaml
- redis-service.yaml
- db-deployment.yaml
- db-service.yaml

> 💡 You may see warnings about CPU/memory limits — that’s because Autopilot prefers defined resource limits.

Verify Deployments:
```bash
kubectl get all
```
Expected Output:
```bash
NAME                                READY   STATUS
pod/vote-xxxxx                      1/1     Running
pod/result-xxxxx                    1/1     Running
pod/worker-xxxxx                    1/1     Running
pod/redis-xxxxx                     1/1     Running
pod/db-xxxxx                        1/1     Running
```
## 🌐 Step 7 — Expose Services using Ingress

1️⃣ Create Namespace for Ingress Controller
```bash
kubectl create namespace ingress-nginx
```
2️⃣ Install NGINX Ingress Controller using Helm
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx
```
Check status:
```bash
kubectl get pods -n ingress-nginx
```
Wait until all pods are Running.

3️⃣ Create Ingress Resource for `vote` App

`vote-ingress.yaml`
```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: vote-ingress
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: vote
            port:
              number: 8080
```
Apply it:
```bash
kubectl apply -f vote-ingress.yaml
```
Check ingress:
```bash
kubectl get ingress
```

You’ll see an external IP once the load balancer is provisioned:
```nginx
NAME            CLASS   ADDRESS          PORTS
vote-ingress    nginx   34.118.229.36    80
```

Now, visit http://<EXTERNAL-IP> to access your app.

---

## 🧹 Step 8 — Clean Up Resources

Once done, delete your cluster to avoid charges:
```bash
gcloud container clusters delete demo-autopilot-cluster \
  --region us-central1 --quiet
```

> 💰 Estimated cost: ~$1–2 for the full demo.

---

## 🏁 Conclusion

✅ You have successfully:

- Understood a multi-microservice architecture
- Containerized services using Docker
- Tested locally with Docker Compose
- Deployed on GKE Autopilot
- Exposed services using Ingress

This project is a perfect foundation for learning real-world Kubernetes deployments on GCP.

---
