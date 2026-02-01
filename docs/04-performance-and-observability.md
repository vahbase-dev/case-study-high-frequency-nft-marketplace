# 04 — Performance and Observability

## Performance Goals
- Sub-second perceived execution for trade intents
- Predictable tail latency during burst traffic
- Stable throughput under peak loads

## Latency Optimisations
- Optimistic acknowledgement for intents
- Batched writes for projections where safe
- Backpressure via queues for indexer and reconciliation
- Hot-path SQL tuned around:
  - indexed foreign keys
  - partial indexes on `pending` statuses
  - minimal row payloads for locks

## Throughput Controls
- Stateless API scaling behind a load balancer
- Dedicated workers for:
  - chain adapter
  - indexer
  - reconciliation
- Partitioning strategies (if needed):
  - by contract / collection
  - by wallet hash range

## Monitoring
- Metrics:
  - P50/P95/P99 for intent acceptance and finality
  - DB lock wait time
  - queue depth
  - indexer block lag
  - error budgets by subsystem
- Logs:
  - structured JSON logs
  - trace id per intent
- Alerts:
  - finality lag over threshold
  - mismatch rate spikes
  - reorg rebuild loops
  - DB saturation indicators

## Load Testing
- Synthetic order bursts
- Concurrent intents across same wallet (race tests)
- Chain event replay tests (idempotency validation)

## Reported Outcomes
- Reduced trade latency by ~40% (sub-second execution targets met)
- Sustained high daily throughput without race-condition errors
- Reduced on-chain gas overhead through indexing and batching strategies
