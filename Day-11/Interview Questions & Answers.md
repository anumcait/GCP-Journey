# 🧠 GCP Day-11 — Docker on GCP: Interview Questions, Scenarios & Mock Test

**Focus Area:** Docker • Containerization • GCP Compute Engine • DevOps Practices  
**Level:** Beginner → Advanced  

---

## 🧩 Conceptual Questions

### 1️⃣ What is Docker and why do we use it?
Docker is a **containerization platform** that packages applications with all dependencies into a lightweight, portable unit called a **container**.  
It ensures that software runs consistently across environments — solving the “It works on my machine” problem.

---

### 2️⃣ What problem does Docker solve?
1. **Environment inconsistency:** Same app runs differently in dev vs prod → Docker eliminates this.  
2. **Resource utilization:** Multiple lightweight containers can run on a single host without separate OS overhead.

---

### 3️⃣ What’s the difference between a container and a virtual machine?
| Virtual Machine | Docker Container |
|-----------------|------------------|
| Heavy (runs full OS per VM) | Lightweight (shares host OS kernel) |
| Slow startup | Fast startup |
| Uses GBs of memory | Uses MBs of memory |
| Example: VMware, VirtualBox | Example: Docker, Podman |

---

### 4️⃣ What is a Docker image?
A Docker image is a **read-only template** containing instructions for creating a container — like a snapshot of the app’s filesystem, libraries, and runtime.

---

### 5️⃣ What is a Docker container?
A Docker container is a **running instance of an image**.  
It runs isolated from other containers but shares the same OS kernel.

---

### 6️⃣ What is a Dockerfile?
A **Dockerfile** is a text file containing build instructions (e.g., base image, dependencies, ports, and startup command).

Example:
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]
```
### 7️⃣ What is a Multi stage Dockerfile?
A **multi-stage** build allows building and packaging an app in separate stages to reduce image size and improve security.
```bash
FROM node:18 AS build
RUN npm run build
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
```
### 8️⃣ What is the difference between `CMD` and `ENTRYPOINT`?
|CMD|ENTRYPOINT|
|---|----------|
|Default command for the container|Defines the main executable|
|Can be overridden easily|Harder to Override|
|Example:`CMD ["npm", "start"]`|Example:`ENTRYPOINT ["python"]`|

### 9️⃣ How can you persist data inside a Docker container?
Use **Docker volumes** or **bind mounts**

Example

```bash
docker run -v /host/data:/container/data myapp
```
### 🔟 What are Docker networks?
Docker networks allow containers to communicate securely with each other.
Types:`bridges`,`host`,`overlay`,`none`

---
## 🧰 Hands-On / Scenario Questions

### 1️⃣ Your app runs locally but fails in Docker. What would you check?
- Logs: `docker logs container_name`
- ports and env variables
- Missing dependencies or incorrect `WORKDIR`
- Incorrect `EXPOSE` or network mode

### 2️⃣ You deployed a container on port 8080 but can't access it externally on GCP. Why?
**possible causes:**
- GCP firewall not open for port 8080
- Container not mapped (-p 8080:8080)
- App listening on `localhost` instead of 0.0.0.0

### 3️⃣ Your container stops immediately after running. Why?
Because the main process (PID 1) has exited.
Solution: Make sure CMD runs a foreground process(e.g., node index.js, not background script).

### 4️⃣ How do you see container resource usage?
```bash
docker stats
```
### 5️⃣ How do you reduce Docker image size?
- Use **multi-stage** builds
- Use **Alpine** base image
- Add **.dockerignore**
- Clean cache and temp files

### 6️⃣ How do you access a shell inside a running container?
```bash
docker exec -it bash container /bin/sh
```

### 7️⃣ How do you debug a failed Docker build?
```bash
docker build -t myapp .
# then check
docker history myapp
```
or build with
```bash
docker build --progress=plain --no-cache .
```
---
## ☁️ GCP-Specific Docker Questions
### 1️⃣ How do you install Docker on GCP Compute Engine?
```bash
sudo apt update
sudo apt install docker.io -y
```
### 2️⃣ How do you run docker without sudo?
```bash
sudo usermod -aG docker $USER
newgrp docker
```
### 3️⃣ How do you open a port for Docker app on GCP?
```bash
gcloud compute firewall-rules create allow-tcp-8080 \
  --allow tcp:8080 \
  --target-tags=docker-app \
  --description="Allow external access to port 8080"
```
### 4️⃣ How do you push Docker image to artifact Registry?
```bash
gcloud auth configure-docker REGION-docker.pkg.dev
docker tag app:latest REGION-docker.pkg.dev/PROJECT/REPO/app:v1
docker push REGION-docker.pkg.dev/PROJECT/REPO/app:v1
```
### 5️⃣ What is the difference between Docker Hub and Artifact Registry?
|DockerHub|Artifact Registry|
|---------|-----------------|
|Public/Private registry for all users| Private, Google-managed Registry|
|Hosted globally|Region-based within GCP|
|Free/Paid tiers|Integrated with IAM roles|

---

## 💬 Behavioral & DevOps Mindset Questions

### 1️⃣ How does docker fit in Devops lifecycle?
Docker ensures environment consistency between development, testing and production - crucial for CI/CD pipelines

---

### 2️⃣ As a DevOps engineer, how much you should understand?
You should know:
- App entry point (npm start, gunicorn app:app, etc.)
- Required environment variables
- Dependencies and build steps
- Ports, logs, and health checks

**Example:**

If a container keeps restarting, you should check the app’s startup command, environment variable configuration, and exposed ports — not rewrite the code.

---

### 3️⃣ How does Docker improve CI/CD pipelines?

- Enables consistent builds in Jenkins/GitHub Actions
- Speeds up deployment (immutable images)
- Simplifies rollback (versioned images)

---

### 4️⃣ Difference between Docker and Kubernetes?
|Docker|Kubernetes|
|------|----------|
|Containerization tool|Container orchestration platform|
|Build & runs containers| Manages, scales, heals containers|
|Single host|Multi-node cluster|

---

## 🧾 Mock Test — Round 1 (Basic)

### Q1. What command is used to list all Docker containers?

✅ docker ps -a

### Q2. Which file defines how a Docker image is built?

✅ Dockerfile

### Q3. Which command removes all stopped containers?

✅ docker container prune

### Q4. What port is exposed in this command?

docker run -p 8080:80 nginx

✅ Host 8080 → Container 80

### Q5. What does docker-compose do?

✅ Orchestrates multiple containers as services.

---

## ⚡ Mock Test — Round 2 (Advanced Scenarios)

### Scenario 1:

A Node.js app runs fine locally but fails after Dockerization — “PORT already in use.”

✅ Fix: Change container’s exposed port or host mapping.

### Scenario 2:
Your GCP VM runs a Docker container on port 8080 but is unreachable externally.

✅ Fix: Add firewall rule or assign correct network tag.

### Scenario 3:
Container logs show Error: Cannot find module 'express'.

✅ Fix: Install dependencies properly before building image.

### Scenario 4:
App works locally, but in container, static files are missing.

✅ Fix: Add proper build step (npm run build) before copying files.

### Scenario 5:
Docker image is 1GB in size. How can you reduce it?

✅ Use Alpine base image + multi-stage builds.
