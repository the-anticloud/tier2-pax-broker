# L5 Narrow / L2 General Classification — PAX_BROKER
**Platform:** Anticloud | **PAX:** 27B | **IP:** USPTO pending 2026, Anticloud FZ LLE

## L5 Narrow
PAX_BROKER operates at L5 Narrow: it routes inference requests to the correct PAX 27B specialty module (clinical, robotics, security, etc.) based on domain classification. It does not implement general-purpose message routing — it is scoped to the PAX inference pipeline topology.

## L2 General
L2 General means PAX_BROKER handles request routing for all 9 tiers from a single instance. Any new tier module registers with PAX_BROKER and immediately receives routed requests.

## PAX Integration
All inference requests from INTE11ECT_APP, MIIRAI_CHAT, and PAX_API_GATEWAY flow through PAX_BROKER, which classifies the domain and routes to the appropriate PAX module. Routing decisions are AIOSS-chained.

## AIOSS Audit Relevance
Every routing decision (request hash + domain classification + target module + latency) produced by PAX_BROKER is appended to the AIOSS chain.
Chain formula: H_n = SHA3-256(H_{n-1} || entry_hash_n || timestamp_n)
Tamper-evident, air-gap verifiable, no cloud dependency.

## Regulatory
NIST SP 800-53 SC-8 (transmission confidentiality), ISO 27001 A.13.2
