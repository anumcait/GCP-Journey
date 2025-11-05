# Day-22: Real-Time GCP Mega DevOps Project – End-to-End E-Commerce System

This mega project consolidates your **21 days of GCP and DevOps learning** into a **real-world, production-ready workflow**.

We will build a **real-time order processing system** for e-commerce with:

* React Frontend
* Node.js Backend
* Cloud SQL (PostgreSQL/MySQL)
* GKE Deployment
* Pub/Sub + Cloud Functions for async order processing
* Cloud Storage for assets
* Secret Manager for sensitive info
* CI/CD with Cloud Build
* Monitoring & Alerts

---

## **Project Architecture**

```text
React Frontend (Cloud Storage)
        |
        v
GKE Backend (Node.js APIs) <-> Cloud SQL
        |
        v
Pub/Sub Topic -----> Cloud Function (Process Order, send notifications)
        |
        v
Cloud Storage (Store invoices, images)
Monitoring & Logging tracks all activity
```

---

## **Step 1: Provision Infrastructure with Terraform**

**1. Install Terraform:**

```bash
brew install terraform     # MacOS
sudo apt-get install terraform  # Ubuntu/Debian
```

**2. Terraform project structure:**

```text
infra/
  ├─ main.tf
  ├─ variables.tf
  ├─ outputs.tf
  ├─ gke.tf
  ├─ cloudsql.tf
  ├─ pubsub.tf
  ├─ storage.tf
```

**3. Example: GKE Cluster (`gke.tf`)**

```hcl
resource "google_container_cluster" "gke_cluster" {
  name     = "mega-project-cluster"
  location = var.region
  initial_node_count = 3
  node_config {
    machine_type = "e2-medium"
  }
}
```

**4. Cloud SQL (`cloudsql.tf`):**

```hcl
resource "google_sql_database_instance" "db_instance" {
  name             = "ecommerce-db"
  database_version = "POSTGRES_15"
  region           = var.region

  settings {
    tier = "db-f1-micro"
  }
}

resource "google_sql_database" "app_db" {
  name     = "orders_db"
  instance = google_sql_database_instance.db_instance.name
}
```

**5. Pub/Sub (`pubsub.tf`):**

```hcl
resource "google_pubsub_topic" "orders_topic" {
  name = "orders-topic"
}

resource "google_pubsub_subscription" "orders_sub" {
  name  = "orders-sub"
  topic = google_pubsub_topic.orders_topic.name
}
```

**6. Cloud Storage (`storage.tf`):**

```hcl
resource "google_storage_bucket" "assets_bucket" {
  name     = "mega-project-assets-${var.project_id}"
  location = var.region
  force_destroy = true
}
```

**7. Apply Terraform:**

```bash
terraform init
terraform plan
terraform apply
```

✅ This sets up **GKE, Cloud SQL, Pub/Sub, and Cloud Storage**.

---

## **Step 2: Containerize Backend with Docker + Cloud Build**

**1. Dockerfile (Node.js backend):**

```dockerfile
FROM node:20
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

**2. Cloud Build (`cloudbuild.yaml`):**

```yaml
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/mega-backend:latest', '.']
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/mega-backend:latest']
images:
  - 'gcr.io/$PROJECT_ID/mega-backend:latest'
```

**3. Trigger build:**

```bash
gcloud builds submit --config cloudbuild.yaml .
```

✅ Docker image is now in **GCR**.

---

## **Step 3: Secure Secrets with Secret Manager**

**1. Store DB credentials:**

```bash
echo -n "db_password_here" | gcloud secrets create DB_PASSWORD --data-file=-
```

**2. Access in Kubernetes deployment:**

```yaml
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: password
```

---

## **Step 4: Deploy Backend on GKE**

**1. Kubernetes Deployment (`deployment.yaml`):**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mega-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mega-backend
  template:
    metadata:
      labels:
        app: mega-backend
    spec:
      containers:
      - name: backend
        image: gcr.io/<PROJECT_ID>/mega-backend:latest
        env:
          - name: DB_PASSWORD
            valueFrom:
              secretKeyRef:
                name: db-secret
                key: password
        ports:
        - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  type: LoadBalancer
  selector:
    app: mega-backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

**2. Deploy:**

```bash
kubectl apply -f deployment.yaml
kubectl get pods
kubectl get svc
```

✅ Backend running with **LoadBalancer access**.

---

## **Step 5: Integrate Pub/Sub + Cloud Function**

**1. Cloud Function to process orders (`orderProcessor.js`):**

```javascript
const {PubSub} = require('@google-cloud/pubsub');
const pubsub = new PubSub();

exports.processOrder = async (message, context) => {
  const orderData = JSON.parse(Buffer.from(message.data, 'base64').toString());
  console.log('Processing order:', orderData);
  // Add logic: update DB, store invoice in Cloud Storage
};
```

**2. Deploy Cloud Function:**

```bash
gcloud functions deploy processOrder \
  --runtime nodejs20 \
  --trigger-topic orders-topic \
  --region us-central1
```

✅ Orders published to Pub/Sub are processed **asynchronously**.

---

## **Step 6: Frontend Deployment (Optional)**

* Build React app
* Deploy to **Cloud Storage bucket** with static website hosting
* Configure **CDN for faster delivery**

```bash
gsutil cp -r build/* gs://<assets-bucket>
```

---

## **Step 7: CI/CD Pipeline**

* Use **Cloud Build triggers** on GitHub commits
* Automate **Docker build → push → GKE deployment**
* Integrate **unit tests** before deployment

---

## **Step 8: Monitoring & Alerts**

* Enable **Cloud Monitoring & Logging**
* Create alerts:

  * CPU > 80%
  * Pod restarts > 3
  * HTTP errors > 5%
* Set **email/Slack notifications**

```bash
gcloud monitoring channels create \
  --display-name="Mega Project Alerts" \
  --type=email \
  --channel-labels=email_address=you@example.com
```

✅ Your application now has **full observability**.

---

## **Step 9: Learning Outcomes**

* Provisioned **multi-service infra** with Terraform
* Built **Dockerized backend** and deployed on GKE
* Implemented **async workflow** with Pub/Sub + Cloud Functions
* Managed **secrets securely**
* Hosted frontend on Cloud Storage
* Automated CI/CD with Cloud Build
* Enabled **monitoring and alerting** for production readiness

This is a **true end-to-end DevOps project** using multiple GCP services, all integrated for **real-time operations**.

---

### Next Steps / Enhancements

* Add **Cloud Run microservices** for serverless tasks
* Horizontal Pod Autoscaling in GKE
* Implement **Redis / Memorystore** for caching
* Use **Terraform modules** for reusable infrastructure
* Add **payment gateway integration**

---
