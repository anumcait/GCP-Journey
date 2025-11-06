# Day 19 — Pub/Sub: Messaging Basics

**End-to-End Q&A (Complete)**
*Use-case: Async processing in e-commerce — create topic & publish messages, push vs pull subscriptions, connect Pub/Sub with Cloud Functions.*

---

## 1 — Quick summary

**Q:** What is Google Cloud Pub/Sub?
**A:** Pub/Sub is a fully-managed messaging service for asynchronous, decoupled communication between producers (publishers) and consumers (subscribers). It's ideal for event-driven architectures: publishers send messages to topics; subscribers receive them via subscriptions (push or pull).

**Q:** Why use Pub/Sub in e-commerce?
**A:** To decouple order placement from downstream tasks (inventory update, email, analytics). This improves user latency, scalability, and fault tolerance: the order service returns quickly while consumers process messages asynchronously.

---

## 2 — Fundamentals Q&A

**Q:** What is a topic, subscription, and message?
**A:**

* **Topic:** Named resource where publishers send messages.
* **Subscription:** Named resource representing a stream of messages from a topic; subscribers attach to subscriptions.
* **Message:** Payload (often JSON) plus optional attributes (metadata). Message data is base64 encoded when delivered.

**Q:** What delivery semantics does Pub/Sub provide?
**A:** Pub/Sub guarantees **at-least-once** delivery (messages may be delivered multiple times) and best-effort ordering unless ordering keys are used.

**Q:** What are message attributes?**
**A:** Key/value metadata attached to messages used for routing, filtering, and lightweight metadata without changing the payload.

**Q:** What is ack/deadline?**
**A:** When a subscriber receives a message it must acknowledge (ack). If not acked within the ack deadline (default ~10s), the message becomes eligible for redelivery.

---

## 3 — Practical commands & samples Q&A

**Q:** How do I create a topic with `gcloud`?**
**A:**

```bash
gcloud pubsub topics create orders-placed
```

**Q:** How to publish a message from CLI?**
**A:**

```bash
gcloud pubsub topics publish orders-placed \
  --message '{"orderId":"12345","userId":"u987","total":345.50}'
```

**Q:** How to create a pull subscription?**
**A:**

```bash
gcloud pubsub subscriptions create inventory-sub \
  --topic=orders-placed \
  --ack-deadline=30
```

**Q:** How to create a push subscription?**
**A:**

```bash
gcloud pubsub subscriptions create email-sub \
  --topic=orders-placed \
  --push-endpoint=https://your-service.example.com/pubsub \
  --ack-deadline=10
```

**Q:** Node.js example to publish a message programmatically?**
**A:**

```js
// publish.js
const { PubSub } = require('@google-cloud/pubsub');
const pubsub = new PubSub();

async function publish() {
  const topicName = 'orders-placed';
  const data = { orderId: '12345', userId: 'u987', total: 345.50 };
  const dataBuffer = Buffer.from(JSON.stringify(data));
  const messageId = await pubsub.topic(topicName).publish(dataBuffer, { source: 'orderService' });
  console.log(`Published message ${messageId}`);
}

publish().catch(console.error);
```

**Q:** Cloud Function (Node.js) to handle Pub/Sub messages?**
**A:**

```js
// index.js
exports.processOrder = (message, context) => {
  const raw = message.data ? Buffer.from(message.data, 'base64').toString() : '{}';
  const order = JSON.parse(raw);
  console.log(`Received order ${order.orderId} for user ${order.userId}`);
  // Add business logic: send email, update DB, etc.
};
```

Deploy:

```bash
gcloud functions deploy processOrder \
  --runtime nodejs18 \
  --trigger-topic orders-placed \
  --region asia-south1
```

---

## 4 — Design, patterns & best practices Q&A

**Q:** When should I use pull vs push?**
**A:**

* **Pull:** Use when you want control over flow, batching, and client-side scaling (e.g., heavy DB writes, analytics batching).
* **Push:** Use for near-real-time triggers where you can expose a secure HTTPS endpoint (e.g., notifications via an internal microservice).

**Q:** How do I ensure idempotency?**
**A:** Design consumers to detect duplicate processing (persist processed message IDs or use dedup keys), use database unique constraints, or include message sequence IDs in payloads.

**Q:** Message ordering?**
**A:** Pub/Sub is not strictly ordered by default. Use **ordering keys** if you need per-key ordering (e.g., all events for a single order must be processed in sequence).

**Q:** What about schema and validation?**
**A:** Use Pub/Sub Schemas (Avro/Protocol Buffers) or validate payloads in subscribers to ensure structured data.

**Q:** How to handle poison messages?**
**A:** Use **dead-letter topics** to route messages that repeatedly fail; monitor and inspect DLQ to debug.

---

## 5 — Troubleshooting & edge cases Q&A

**Q:** Messages are not being delivered to my push endpoint — what to check?**
**A:**

1. Ensure endpoint is publicly reachable via HTTPS.
2. Confirm that endpoint responds with 2xx on valid messages.
3. Check Pub/Sub push retry policy and logs.
4. Validate IAM/auth if you use verification tokens or OIDC.

**Q:** Why am I seeing duplicates?**
**A:** At-least-once delivery and redeliveries due to missed acks cause duplicates. Ensure proper ack handling and idempotency.

**Q:** Subscriber experiencing slowdowns/backlog?**
**A:** Scale consumers (increase instances, threads), use Flow Control, or use pull with controlled concurrency. Also check ack deadlines and modify if processing takes longer.

**Q:** Large message payloads cause failure — what to do?**
**A:** Keep messages <10MB. For large data, store in Cloud Storage and send a reference (URI) in the message.

---

## 6 — Lab exercise: end-to-end (step-by-step) + expected answers

### Lab Goal

Simulate order events and process them with two subscribers: a Cloud Function (push/background trigger) for sending emails, and a pull subscriber (Node.js) for inventory updates.

### Steps (student)

1. Create topic `orders-placed`.
2. Deploy Cloud Function `sendOrderConfirmation` triggered by `orders-placed`.
3. Create pull subscription `inventory-sub`.
4. Publish 5 test messages simulating orders.
5. Implement a Node.js pull subscriber that logs and acks messages.
6. Stop the pull subscriber and publish 3 more messages. Restart subscriber and validate redelivery.
7. Configure a dead-letter topic `orders-dlq` for messages that exceed 5 delivery attempts.

### Answers / Observations (expected)

* Cloud Function logs show 5 invocations for 5 messages (background functions create internal sub).
* Pull subscriber receives 5 messages and acks them — no redelivery.
* When subscriber stopped and messages published, the messages remain in subscription backlog; once restarted, messages are delivered.
* Dead-letter topic receives messages that repeatedly fail (simulate by intentionally throwing errors and not acking, exceeding max delivery attempts).

---

## 7 — Security, cost & monitoring Q&A

**Q:** How to secure push endpoints?**
**A:** Require authentication (JWT/OIDC), verify tokens, use HTTPS, and optionally use Cloud IAP or verify Pub/Sub signed tokens. Use VPC Service Controls for stronger isolation.

**Q:** What IAM roles are relevant?**
**A:** `roles/pubsub.publisher` for publishers, `roles/pubsub.subscriber` for subscribers. For admin tasks, `roles/pubsub.admin`.

**Q:** What are cost drivers?**
**A:** Number of messages, message size (egress), retention duration, and API calls. Functions invoked by Pub/Sub add compute costs too.

**Q:** How to monitor Pub/Sub?**
**A:** Use Cloud Monitoring/Logging to track: subscription backlog, publish/ack rates, oldest unacked message, message delivery errors, and function invocations. Set alerts for backlog or high error rates.

---

## 8 — Cheatsheet: commands & snippets

### Create topic

```bash
gcloud pubsub topics create orders-placed
```

### Publish (CLI)

```bash
gcloud pubsub topics publish orders-placed --message '{"orderId":"1","userId":"u1"}'
```

### Create pull subscription

```bash
gcloud pubsub subscriptions create inventory-sub --topic=orders-placed --ack-deadline=30
```

### Create push subscription

```bash
gcloud pubsub subscriptions create email-sub \
  --topic=orders-placed \
  --push-endpoint=https://your-service.example.com/pubsub \
  --ack-deadline=10
```

### Deploy Cloud Function (triggered by Pub/Sub)

```bash
gcloud functions deploy processOrder \
  --runtime nodejs18 \
  --trigger-topic orders-placed \
  --region asia-south1
```

### Pull messages (Node.js sample)

```js
const { PubSub } = require('@google-cloud/pubsub');
const client = new PubSub();
async function pullMessages() {
  const sub = client.subscription('inventory-sub');
  const [messages] = await sub.pull({ maxMessages: 10 });
  for (const m of messages) {
    console.log('Message:', m.data.toString());
    await sub.acknowledge(m.ackId);
  }
}
pullMessages().catch(console.error);
```

---

## Final notes & suggested further reading (self study)

* Practice: implement the lab and record findings (latency, duplicates, backlog).
* Extend: add attribute filtering, dead-letter topics, and ordering keys.
* Consider integration with Dataflow for streaming transformations or BigQuery for long-term analytics ingestion.

---
