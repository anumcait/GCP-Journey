# 🚀 GCP Day 11 — Docker on GCP: Containerizing Applications

Welcome to **Day 11** of my Google Cloud journey!  
Today, we’ll learn how to **containerize a real-time JavaScript application** using Docker and deploy it on **Google Cloud Platform (GCP)**.

We’ll go step-by-step — from understanding Docker fundamentals, installing Docker on a Compute Engine VM, writing and building a multi-stage Dockerfile, to running the container and exposing it to the internet.

---

## 🧠 Quick Recap: Why Docker?

Docker has revolutionized modern application deployment because it solves **two classic IT challenges**:

### 1️⃣ “It works on my machine” problem

Before Docker, developers built applications on one environment (e.g., **Windows**) and sysadmins deployed on another (e.g., **Linux**).  
Applications often failed due to **different configurations**, missing dependencies, or OS variations (Ubuntu vs CentOS vs Debian).

**Example:**
> A Node.js app runs perfectly on a developer’s laptop (Windows), but fails on a production server (Linux) because of version mismatches or missing libraries.

**How Docker Solves It:**
Docker packages the **application + all its dependencies + system libraries** into a **container image** that runs consistently everywhere — regardless of the OS or environment.

---

### 2️⃣ Resource Utilization Problem

Before Docker, applications were deployed on **dedicated servers** or **VMs** that were **underutilized**.

**Example:**
> A physical server with 100 CPUs & 100 GB RAM runs one app using just 10% of the resources — the rest sits idle.

**Virtual Machines** improved utilization slightly but still required one OS per VM.  
Docker containers changed that — allowing **multiple lightweight containers** to share the same host OS while running **independent services**.

**DevOps Perspective:**
With Docker (and later Kubernetes), organizations now deploy **dozens or hundreds of microservices** efficiently on a single VM cluster, achieving optimal resource utilization and scalability.

---

## 👨‍💻 Why DevOps Engineers Should Learn Docker

As a **DevOps Engineer**, you don’t need to be an expert programmer — but you must:
- Understand **application structure** (e.g., where dependencies are declared).
- Know how to **build** the application (`npm run build`, `pip install`, `go build`, etc.).
- Know how to **deploy** it (via Docker/Kubernetes).

| Language | Dependency File | Build Command | Notes |
|-----------|------------------|----------------|--------|
| Java | `pom.xml` / `build.gradle` | `mvn package` / `gradle build` | |
| Python | `requirements.txt` | `pip install -r requirements.txt` | |
| JavaScript / TypeScript | `package.json` | `npm run build` | |
| Golang | `go.mod` | `go build` | |

So, as a DevOps engineer, your focus is not to write code — but to understand **how to build, containerize, and deploy** the app reliably.

---

## ☁️ Setup: Docker on Google Cloud Platform

### Step 1 — Create a New Project
1. Go to [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project (e.g., `docker-demo`).
3. Enable **Compute Engine API**.

---

### Step 2 — Create a Virtual Machine
1. Navigate to **Compute Engine → VM Instances → Create Instance**.
2. Recommended Config:
   - **Machine Type:** E2-small (cost-efficient)
   - **OS:** Debian (default)
   - **Provisioning Model:** Spot (optional, for cost savings)
   - **Network Tag:** `mario` (used for firewall rules)

---

### Step 3 — Connect to the VM
Click **SSH** or use **Cloud Shell**.

Update packages and install Docker:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Verify installation:

```bash
docker run hello-world
```
If you see the Docker success message — ✅ Docker is ready.

## 🕹️ Step 4 — Clone and Prepare Application

For this demo, we’ll use a Super Mario JavaScript (TypeScript) application.
```bash
git clone https://github.com/anumcait/super-mario-mimic
cd super-mario-mimic
```

Inside the project, ensure you have a package.json file — this lists all dependencies and build commands:

```bash
"scripts": {
  "dev": "vite",
  "build": "vite build"
}

```

## 🐳 Step 5 — Write a Multi-Stage Dockerfile

Create a file named Dockerfile inside the project directory:
```bash
# ---------- Stage 1: Build ----------
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# ---------- Stage 2: Serve ----------
FROM node:18-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
RUN npm install -g http-server
EXPOSE 8080
CMD ["http-server", "dist", "-p", "8080", "-c-1"]
```

This multi-stage build ensures:

- Smaller image size
- Improved security
- Faster build time (via caching)

## 🏗️ Step 6 — Build and Run Docker Image
Build the Docker image:
```bash
docker build -t mario-game:v1 .
```
Check image:
```bash
docker images
```

Run container:
```bash
docker run -d -p 8080:8080 mario-game:v1
```
Check running containers:
```bash
docker ps
```
## 🌍 Step 7 — Configure Firewall (Expose the App)

By default, GCP blocks all ports.
Create a firewall rule to allow HTTP traffic on port 8080.

1. Go to VPC Network → Firewall → Create Firewall Rule
2. Name: allow-mario-8080
3. Target tags: mario
4. Source IP Ranges: 0.0.0.0/0
5. Protocols and ports: tcp:8080
6. Click Create.

Now, go to your VM’s **External IP** and open in browser:
```bash
http://<EXTERNAL_IP>:8080
```

🎮 You should now see the Super Mario app live on the internet!

## 📦 Step 8 — Push Image to DockerHub (Optional)

If you want to store your Docker image on DockerHub or GCP Artifact Registry:
```bash
docker login
docker tag mario-game:v1 yourusername/mario-game:v1
docker push yourusername/mario-game:v1
```
## 🧩 Summary

✅ Installed Docker on GCP Compute Engine

✅ Wrote a multi-stage Dockerfile for a JS/TS app

✅ Built and ran the container

✅ Configured firewall for port 8080

✅ Deployed Super Mario app accessible publicly

## 💬 Real-World DevOps Relevance

As a DevOps engineer, you may not build apps from scratch — but you:
- Containerize and deploy them efficiently.
- Ensure consistent environments across Dev, QA, and Production.
- Optimize cloud resources using containers.
- Automate these steps later with CI/CD pipelines and Kubernetes.
- Docker bridges the gap between developers and operations, ensuring faster, more reliable deployments.

## 🌟 Note

The same process can be repeated on AWS EC2, Azure VM, Oracle Cloud, or DigitalOcean Droplets — Docker behaves identically across all platforms.

"Once you understand how Docker works, cloud platforms become just environments — your skill travels everywhere."

