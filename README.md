# easy-rmq

Simplify RabbitMQ for most use cases : publish and consume messages easily, with retry and DLQ support.

## Why `easy-rmq` ?

Working with RabbitMQ usually means:

- Manually declaring exchanges, queues, and bindings.
- Handling retries, dead-letter queues, and connection issues.
- Naming everything... even if you don't care.

This library fixes that.

Just give us a **topic name**, and we’ll:

- Create all necessary exchanges/queues automatically.
- Handle retries and DLQ for you.
- Reconnect automatically on connection loss.

**Your job?** Focus on sending and handling messages.

---

## Features

- Automatic exchange/queue declaration  
- Topic-based routing  
- Built-in retry logic (configurable)  
- Dead-letter queue support  
- Auto-reconnect  
- Minimal configuration  
- Designed for simplicity and developer happiness

---

## Installation

```bash
npm install easy-rmq
```

## Quick Start

### Configure once

```js
// rmq.config.js
module.exports = {
  connection: {
    url: "amqp://localhost"
  },

  topic: "user.created",

  retry: {
    enabled: true,
    delay: 5000,
    maxAttempts: 3
  },

  deadLetter: true
};
```

You don’t need to name exchanges or queues — we'll take care of it using the topic name.

When a consumer throws or rejects: the message goes to a retry queue.
It will be re-queued after retry.delay (e.g. 5000ms).
After retry.maxAttempts, it ends up in a DLQ.
You can inspect DLQ queues manually or handle them separately.

| Key                 | Description                      | Required |
| ------------------- | -------------------------------- | -------- |
| `connection.url`    | AMQP URL to your RabbitMQ server | ✅       |
| `topic`             | Topic string used for routing    | ✅       |
| `retry.enabled`     | Enable retry mechanism           | optional |
| `retry.delay`       | Time in ms before requeue        | optional |
| `retry.maxAttempts` | Max retry attempts               | optional |
| `deadLetter`        | Enable DLQ after retries         | optional |

### Publish a message

```js
import rmq from "easy-rmq";

await rmq.launch();

await rmq.publish("user.created", {
  id: "abc123",
  email: "user@example.com"
});
```

### Consume messages

```js
await rmq.consume("user.created", async (payload) => {
  console.log("New user:", payload);
});
```

## API Reference

```js
// Start the connection and set up queues/exchanges
await rmq.launch(): Promise<void>

// Publish a message
await rmq.publish(topic: string, payload: any): Promise<void>

// Consume messages for a given topic
await rmq.consume(topic: string, handler: (message: any) => Promise<void>): Promise<void>
```

## Visualize your setup

A CLI or web view to generate a schema of:

- exchanges
- queues
- bindings
- retry and DLQ flows

Planned in the roadmap — contributions welcome!
