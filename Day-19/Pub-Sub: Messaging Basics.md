# 🚀 Day 19 – Pub/Sub: Messaging Basics

## 🎯 Use-case: Asynchronous Processing in E-commerce

Today we’ll explore one of Google Cloud’s most powerful services — **Pub/Sub** — the messaging backbone for event-driven systems.  
We’ll cover:

- What is Pub/Sub  
- Async processing in e-commerce  
- Creating topics & publishing messages  
- Push vs Pull subscriptions  
- Connecting Pub/Sub with Cloud Functions  

---

## 🧠 1. What is Pub/Sub?

![Pub/Sub Architecture](https://docs.cloud.google.com/static/pubsub/images/pubsub-components.png)

**Pub/Sub = Publish / Subscribe**

It’s a *fully-managed*, *asynchronous*, *scalable* messaging service that decouples producers and consumers.

**Key Concepts**

| Component | Description |
|------------|-------------|
| **Topic** | Named resource where publishers send messages |
| **Message** | Payload sent by publishers |
| **Publisher** | Sends messages to a topic |
| **Subscription** | Stream of messages from a topic to a subscriber |
| **Subscriber** | Receives and processes messages from a subscription |

---

### 💡 Why Use Pub/Sub in E-commerce?

E-commerce platforms involve multiple services: Orders, Inventory, Payments, Email, Analytics.  
You want these to communicate *asynchronously* — so one service doesn’t block another.

**Example:**  
When an order is placed, the *Order Service* publishes an event to the `orders-placed` topic.  
Different services subscribe to it:

- `inventory-sub` → updates stock  
- `email-sub` → sends confirmation email  
- `analytics-sub` → logs the event  

This allows decoupled, scalable, and resilient architecture.

---

## 🏗️ 2. E-commerce Workflow Example

![E-commerce Pub/Sub Flow](https://miro.medium.com/v2/resize%3Afit%3A1200/1%2AFxOuUdGuwaBLfgfNbghkEA.png)

```json
{
  "orderId": "12345",
  "userId": "u987",
  "items": [
    { "sku": "SKU1", "qty": 2 },
    { "sku": "SKU7", "qty": 1 }
  ],
  "totalAmount": 345.50,
  "timestamp": "2025-11-05T12:34:56Z"
}
```
### Subscribers:

- Inventory Service (pull)
- Email Service (push)
- Analytics Service (pull)

Each receives the same message asynchronously.

---

### ✅ Benefits

- Decoupled architecture
- Scalable under heavy load
- Resilient to subscriber failure
- Easy to add new consumers

### ⚙️ Considerations

- At-least-once delivery → handle duplicates
- Keep messages ≤ 10 MB
- Use attributes for filtering (e.g., region, priority)

---

## 🧩 3. Create Topic and Publish Messages

### 🔧 Setup Steps
```
# Create topic
gcloud pubsub topics create orders-placed

# Publish message
gcloud pubsub topics publish orders-placed \
  --message '{"orderId":"12345", "userId":"u987", "total":345.50}'
```
## 🧰 Programmatic Example (Node.js)
```bash
const { PubSub } = require('@google-cloud/pubsub');
const pubsub = new PubSub();

const topic = pubsub.topic('orders-placed');
const data = JSON.stringify({ orderId: '12345', userId: 'u987' });
const dataBuffer = Buffer.from(data);

await topic.publish(dataBuffer, { source: 'orderService' });
```
---

### 💡 Best Practices

- Use meaningful topic names (orders-placed, inventory-updated)
- Define schemas for validation
- Keep payloads small
- Acknowledge messages after successful processing

---

## 🔁 4. Push vs Pull Subscriptions
|Feature|	Pull|	Push|
|-------|-----|-----|
|Message| retrieval	Subscriber pulls manually|	Pub/Sub pushes automatically|
|Real-time|	Slight delay|	Near instant|
|Control|	High|	Low
|Endpoint|	Client calls API	Needs HTTPS| endpoint|
|Example|	Inventory batch updates|	Email notification trigger|

**Create Pull Subscription**
```bash
gcloud pubsub subscriptions create inventory-sub \
  --topic=orders-placed --ack-deadline=30
```

**Create Push Subscription**

```bash
gcloud pubsub subscriptions create email-sub \
  --topic=orders-placed \
  --push-endpoint=https://your-service.example.com/pubsub \
  --ack-deadline=10
```
💬 Example Design

- email-sub → push for instant processing
- inventory-sub → pull for controlled batch updates
- Add new services anytime (e.g., fraud detection)
- Ensure idempotency → avoid duplicate processing

---

## ⚡ 5. Connect Pub/Sub with Cloud Functions

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/822a8c38-360b-48d2-acb8-d29af5700a36" />

Serverless functions are perfect for reacting to Pub/Sub messages.

### 📦 Steps
```bash
# Create topic
gcloud pubsub topics create orders-placed

# Deploy Cloud Function
gcloud functions deploy processOrder \
  --runtime nodejs18 \
  --trigger-topic orders-placed \
  --region asia-south1
```

**Function code (index.js):**
```bash
exports.processOrder = (message, context) => {
  const data = Buffer.from(message.data, 'base64').toString();
  console.log(`Received order: ${data}`);
  // Business logic: send email, update DB, etc.
};
```
Publish a message and watch Cloud Function logs.

---

## 💎 Best Practices

- Decode message.data (base64)
- Functions should be idempotent
- Ensure Pub/Sub service account can invoke function
- Use Eventarc for advanced routing
- Monitor using Cloud Logging

---

## 🧭 6. Full Workflow Summary
```
# Step 1: Create topic
gcloud pubsub topics create orders-placed

# Step 2: Create pull and push subscriptions
gcloud pubsub subscriptions create inventory-sub --topic=orders-placed
gcloud pubsub subscriptions create email-sub \
  --topic=orders-placed \
  --push-endpoint=https://YOUR_EMAIL_SERVICE_ENDPOINT/pubsub

# Step 3: Deploy Cloud Function
gcloud functions deploy sendOrderConfirmation \
  --runtime nodejs18 \
  --trigger-topic orders-placed \
  --region asia-south1

# Step 4: Publish message
gcloud pubsub topics publish orders-placed \
  --message '{"orderId":"12345","userId":"u987","total":345.50}'
```

---

## ⚠️ 7. Tips & Pitfalls

- At-least-once delivery: expect duplicates
- Ack deadlines: adjust to processing time
- Ordering: use ordering keys if strict order needed
- Monitoring: check backlog metrics
- Dead-letter topics: handle repeated failures
- Security: HTTPS endpoints + authentication for push subs
- Cost optimization: minimize message retention & API calls

## 🧪 8. Hands-on Practice

- Create a new topic order-completed
- Add one pull and one push subscription
- Deploy a Cloud Function to log messages
- Write a Node.js script to pull messages manually
- Test retries by stopping subscribers
- Add filters (priority=high) and test selective delivery
- Record delivery times and redelivery behavio
- Publish a message and watch Cloud Function logs.


