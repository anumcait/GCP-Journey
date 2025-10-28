# Day-14 : Deploy a Multi-Microservice Application on Google Kubernetes Engine (GKE Autopilot)

## 🧠 Overview

We’ll deploy a multi-language microservice application (the Docker voting app) on GKE Autopilot.
Autopilot manages the control plane and nodes automatically — meaning you only focus on your workloads, not infrastructure.

---
<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/78d90545-d15b-46e9-9a60-7fd66ec28591" />

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

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/a2484bea-dfaf-4648-a9f7-0f369bd7422c" />

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

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/aac3d876-8b2d-4628-9eb3-09ac0739d236" />
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/e7164164-717d-4a29-a549-d79aa2b38632" />
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/929f618b-1b77-4257-95ed-8d598f7a5cf7" />

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

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/ebc0f174-a798-4aa2-bef3-70bf922c9367" />

If you want test with sample nginx application

```bash
kubectl create deployment nginx --image=nginx
```
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/1fb48fea-83a5-4dbe-b255-e2a93e6d30a5" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/7d3b6551-5114-45b3-b2b6-05f0fd0fe82f" />

Now Copy source code to the GCP Terminal

```bash
git clone https://github.com/dockersamples/example-voting-app.git
cd example-voting-app
```

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/3f740e70-d0d4-42a1-b8bb-48c862a331d6" />




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

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/7b11dbea-99d6-4165-83f8-58e2a5f28e67" />

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



<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/fff479ab-d655-42af-88d8-a4defe892421" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/f9ea87e0-4beb-43d9-98c6-f81b9d1ff8cd" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/91298406-8ce7-412a-8e70-876ed3a51be0" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/0dd61185-4eaa-4461-a05e-3e33bc743ab9" />


<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/05fea5d9-b0d5-4573-bd6b-c093a466176a" />

---

## 🌐 Step 7 — Expose Services using Ingress

1️⃣ Create Namespace for Ingress Controller
```bash
kubectl create namespace ingress-nginx
```
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/2d6aaaae-05e3-4a5e-a31a-ba15fabca4a6" />
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/9a842d83-91ed-4d42-a184-c7eee6b37ec7" />

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


<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/3fc46b09-0792-4d15-8f1f-b8a406cc0798" />

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
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/58801ccb-c2bf-4061-ad37-fe588a3a23ce" />

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

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/eedfe42f-0d06-4b64-b322-d17313568cce" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/ae73cb86-272f-4ed1-b0a5-5397f5d2cf7f" />

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/e509b4be-592b-4d9a-8a26-275f42a1b14b" />

---

## 🧹 Step 8 — Clean Up Resources

Once done, delete your cluster to avoid charges:
```bash
gcloud container clusters delete demo-autopilot-cluster \
  --region us-central1 --quiet
```

> 💰 Estimated cost: ~$1–2 for the full demo.

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/b88a77c5-b619-475c-8589-6895eed5dad2" />


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
