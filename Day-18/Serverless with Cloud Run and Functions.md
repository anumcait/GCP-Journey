# 🌩️ Day 18 — Serverless with Cloud Run and Cloud Functions

## 🚀 Introduction

Today's topic was **Serverless Architecture** — one of the most exciting revolutions in cloud computing.  
Imagine building and running applications **without managing any servers** — no virtual machines, no scaling headaches, no patching!  
That's exactly what serverless brings to the table.

Platforms like **Google Cloud Run** and **Google Cloud Functions** let us focus purely on writing code, while Google Cloud takes care of everything else behind the scenes — scaling, performance, and cost optimization.

---

## ☁️ What is Serverless Architecture?

Let’s start with a simple explanation.

In traditional setups, developers had to manage servers — even if an app had no traffic at night, the server still ran and cost money.

**Serverless** changes that game.

It means:
- You **don't manage servers**.
- You **pay only when your app runs**.
- Your app **scales automatically** when users increase.
- When there's no traffic, it scales **down to zero**, saving cost.

### 💡 Real-world Example
Think of serverless like **a taxi ride**:
- You don’t buy the car (no infrastructure setup).
- You pay only for the ride (on-demand billing).
- The driver (cloud provider) takes care of maintenance and fuel (infrastructure and scaling).

---

## 🧩 When to Use Serverless

Serverless works best when:
- Your app runs occasionally or unpredictably.  
- You want quick prototypes or APIs.  
- You need to respond to **events** (like a file upload or payment trigger).  
- You want to save costs for low-traffic workloads.

---

## ⚙️ Cloud Run — Running Containers Without Servers

**Cloud Run** lets you deploy **any containerized application** — Node.js, Python, Java, Go, etc.  
You just package your app into a Docker container, push it to Google Cloud, and Cloud Run runs it on-demand.

### 🧭 Steps to Deploy a Container

#### Create a simple app (if you haven’t already)

Example app.js:
```
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello from Cloud Run!");
});

const PORT = process.env.PORT || 8080;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```
#### Create a Dockerfile in the same folder
```
# Use official Node.js image
FROM node:18

# Create app directory
WORKDIR /usr/src/app

# Copy package files (if any)
COPY package*.json ./

# Install dependencies
RUN npm install --only=production

# Copy app source
COPY . .

# Expose port
EXPOSE 8080

# Start the app
CMD ["node", "app.js"]
```

---


#### 1️⃣ Build your container image
You can use **Cloud Build** to create a container and store it in **Google Container Registry**.

```bash
gcloud builds submit --tag gcr.io/<PROJECT_ID>/hello-cloudrun
```
#### 2️⃣ Deploy to Cloud Run

Run the below command to deploy your containerized app to Cloud Run:
```
gcloud run deploy hello-cloudrun \
  --image gcr.io/<PROJECT_ID>/hello-cloudrun \
  --platform managed \
  --region asia-south1 \
  --allow-unauthenticated
```
#### 3️⃣ Access your app

Cloud Run gives you a public HTTPS URL after deployment.
Open that link — congratulations 🎉 your app is live without any server setup!

---

## 🧱 Why Cloud Run is Powerful

- Any language, any framework: If it runs in a container, it runs on Cloud Run.
- Autoscaling: From 0 to thousands of requests instantly.
- Cost-effective: Pay only for CPU and memory during request handling.
- Fully managed: Google handles scaling, networking, and availability.

## 💡 Example Use Cases

- Hosting APIs for mobile or web apps.
- Running scheduled jobs (using Cloud Scheduler).
- Handling webhooks from external systems.
- Processing background tasks like image conversion.

---

## ⚡ Cloud Functions — Code Without Containers

If Cloud Run is for containerized apps, Cloud Functions is for small snippets of code that react to events.
It’s Google’s Function-as-a-Service (FaaS) — meaning you just write a function and Google runs it whenever an event triggers it.

### 🧭 Create a Simple Function

#### 1️⃣ Deploy your function

Run this command to deploy a basic function:
```bash
gcloud functions deploy helloFunction \
  --runtime nodejs20 \
  --trigger-http \
  --allow-unauthenticated
```

#### 2️⃣ Function code example
```js
exports.helloFunction = (req, res) => {
  res.send('Hello from Cloud Functions!');
};
```
#### 3️⃣ Access it

After deployment, you’ll get an HTTPS URL.
Visit it in your browser or use curl — and your function runs instantly!

---

## 🧩 When to Use Cloud Functions

- Event-driven tasks (like file uploads, database changes, or Pub/Sub messages).
- Webhooks from Stripe, GitHub, or Slack.
- Sending automated emails or notifications.
- Lightweight APIs or data transformers.

## 💡 Real Example

Imagine a user uploads an image to Cloud Storage.
You can trigger a Cloud Function to:
  - Resize the image
  - Save a thumbnail version
  - Notify the user via email
All automatically — no servers required!

## 💰 Comparing Serverless Services

|Feature / Service|	Cloud Functions|Cloud Run|	GKE (Kubernetes Engine)|
|-----------------|----------------|---------|-------------------------|
|Type|	Function (FaaS)|	Container (CaaS)	Cluster-based (K8s)
|Setup Time|Very fast|	Fast|	Complex|
|Scaling|	Automatic|	Automatic|	Manual/configurable|
|Billing|	Per execution|	Per request|	Per node|
|Languages|	Limited (Node, Python, Go, etc.)|	Any (container-based)|	Any|
|Best For|	Event-driven cod|	APIs & microservices|Complex multi-container apps|

---

## 🧩 Real-Life Scenarios

#### 🔹 Background Jobs

- Process large files asynchronously.
- Send emails or reports in the background.
- Convert data formats or compress uploads.

#### 🔹 Webhooks

- Payment success callbacks from Razorpay or Stripe.
- Notifications from GitHub or Slack.
- Automation between cloud services.

#### 🔹 Microservices

- Build REST APIs that scale automatically.
- Manage authentication and user management.
- Run data transformation services for analytics.

---

## 🧠 Key Takeaways

- Serverless removes infrastructure worries — just write and deploy your logic.
- Cloud Run is perfect for running containerized microservices.
- Cloud Functions is best for lightweight, event-based operations.
- Both options are scalable, secure, and cost-efficient compared to GKE.
- Ideal for developers, startups, and DevOps engineers who want to focus more on features than servers.

---

