# Deploy Guide — PAX_BROKER
**Platform:** Anticloud PAX 27B harness | Air-gap capable

## Prerequisites
Python 3.11+, pyzmq 25.0+, asyncio (stdlib), AIOSS_FORMAT

## Environment
CPU-only. 4GB RAM. ZeroMQ on localhost:5555-5560. No GPU required — broker is pure routing.

## AIOSS Integration
```bash
aioss init --module PAX_BROKER --output ./pax_broker.aioss
aioss append --chain ./pax_broker.aioss --payload ./output.bin --module PAX_BROKER
aioss verify --chain ./pax_broker.aioss
```

## Air-Gap Deployment
```bash
pip download -r requirements.txt -d ./wheels/
# Transfer to air-gap host
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(model_path="./pax-27b-q4.gguf", module="PAX_BROKER",
                     aioss_chain="./pax_broker.aioss",
                     classification="L5_NARROW_L2_GENERAL")
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./pax_broker.aioss --verbose
python -m pax_broker.tests.smoke
```
