# PAX Broker — Message Brokering & Event Streaming

**Status:** Production | **Version:** 1.0.0 | **Author:** PAX Infrastructure Team  
**Domain:** 0-1.gg/pax/broker

---

## What Is PAX Broker?

PAX Broker enables asynchronous communication between PAX components via message queuing and event streaming, supporting both task distribution and real-time event propagation with guaranteed delivery.

---

## Key Specifications

| Aspect | Details |
|--------|---------|
| **Protocols** | AMQP, NATS, Kafka, MQTT |
| **Throughput** | 1M messages/sec (single broker) |
| **Latency** | <100ms P95 (end-to-end) |
| **Durability** | At-least-once, exactly-once delivery |
| **Partitioning** | Topic-based, request-based routing |
| **Retention** | 7 days (configurable) |

---

## Architecture

### Layer 1: Message Ingress
- Protocol translation (OpenAI → PAX format)
- Message validation and serialization

### Layer 2: Topic Management
- Topics for each PAX component type
- Dead-letter queues for failed messages
- Retention policies

### Layer 3: Consumer Groups
- Scaling across multiple consumers
- Offset management
- Rebalancing

### Layer 4: Reliability
- Message persistence
- Acknowledgment tracking
- Retry with backoff

---

## Quick Start

### Installation
```bash
pip install pax-broker
```

### Configuration
```yaml
broker:
  backend: "nats"  # or kafka, rabbitmq
  url: "nats://localhost:4222"
  
  topics:
    - name: "pax_inference_requests"
      partitions: 10
      retention_hours: 24
    
    - name: "pax_events"
      retention_hours: 24
```

### Python API
```python
from pax_broker import Broker

broker = Broker.from_config("config.yaml")

# Publish message
broker.publish(
    topic="pax_inference_requests",
    message={"model": "pax-27b", "prompt": "..."}
)

# Subscribe to topic
async def handle_message(msg):
    print(f"Received: {msg}")

broker.subscribe("pax_events", handle_message)
```

---

## Integration Points

### Primary Consumers
- **PAX_ROUTER** — Asynchronous request distribution
- **PAX_SCHEDULER** — Task queue management
- **PAX_MONITOR_SYSTEM** — Event streaming
- **ANTICLOUD_AGENT** (Tier 1) — Code generation requests

### Deployment
- **Kubernetes** — Message broker operators
- **Cloud services** — AWS SQS, Google Pub/Sub, Azure Service Bus
- **On-premise** — NATS, RabbitMQ, Kafka clusters

---

**Next:** See APPENDIX/ for broker patterns
