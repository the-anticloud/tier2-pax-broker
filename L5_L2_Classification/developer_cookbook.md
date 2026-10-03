# Developer Cookbook — PAX_BROKER
**Stack:** Python 3.11, asyncio, ZeroMQ, AIOSS_FORMAT

## Core Usage Patterns

## Start broker
```python
from pax_broker import PAXBroker
broker = PAXBroker(aioss_chain="./broker.aioss")
broker.register_module("CLINICAL", endpoint="tcp://localhost:5556")
broker.register_module("ROBOTICS", endpoint="tcp://localhost:5557")
broker.start()
```

## Send request (producer)
```python
import zmq
ctx = zmq.Context()
sock = ctx.socket(zmq.DEALER)
sock.connect("tcp://localhost:5555")
sock.send_json({"domain": "clinical", "prompt": "Analyze ECG", "session": "s001"})
```

## Receive response (consumer)
```python
response = sock.recv_json()
print(response["text"], response["chain_hash"])
```

## Monitor queue depth
```python
metrics = broker.metrics()
print(f"Queue depth: {metrics.queue_depth}, Avg latency: {metrics.avg_latency_ms:.1f}ms")
```

## AIOSS Append
```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()
```

## Performance
ZeroMQ DEALER/ROUTER pattern for async request routing. Worker pool per domain module. Queue depth monitoring: alert if > 100 pending requests. Use affinity routing to keep session requests on same worker for KV cache reuse.

## Integration
Routes to PAX_INFERENCE_CORE, PAX_REASONING, PAX_PLANNING, PAX_VISION_ENGINE. Receives from PAX_API_GATEWAY, KAZCADE_RUNTIME. AIOSS-chained via AIOSS_FORMAT.
