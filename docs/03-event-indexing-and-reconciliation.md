# 03 — Event Indexing and Reconciliation

## Indexing Goals
- Convert on-chain events into queryable, low-latency projections
- Provide deterministic settlement signals to the off-chain ledger
- Support replay from a safe block to withstand reorgs

## Indexer Pipeline
- **Block fetcher:** pulls blocks by height with confirmations
- **Log decoder:** filters contract topics, decodes ABI events
- **Event normaliser:** maps to domain events (versioned schema)
- **Projection writers:** updates materialised views for reads
- **Checkpointing:** stores last safe processed block

## Confirmations and Finality
- Process events only after `N` confirmations
- Store raw logs + decoded payload for audits
- Maintain a reorg buffer (safe block window)

## Reconciliation Worker
Inputs:
- `tx_intents` (pending / unknown)
- indexed chain events
- `tx_receipts`

Actions:
- Match intent → receipt → canonical chain event
- Apply final state transitions:
  - `pending` → `confirmed`
  - `pending` → `failed`
  - `pending` → `expired`

## Deterministic Resolution Rules
- On-chain event wins when present
- Receipt failure without event → mark failed
- Missing receipt beyond timeout → release reservation and mark expired
- Reorg detection triggers projection rebuild from safe checkpoint

## Race Condition Controls
- Single DB transaction for:
  - locking rows
  - applying final state
  - closing reservation
- Idempotent reconciliation: can run repeatedly without double-applying

## Observability
- Per-intent trace id propagated across:
  - API logs
  - indexer logs
  - reconciliation logs
- Metrics:
  - intent → confirm latency
  - indexer lag (blocks)
  - reconciliation backlog
  - mismatch rate
