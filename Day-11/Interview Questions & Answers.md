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
A **multi-stage** build allows 
