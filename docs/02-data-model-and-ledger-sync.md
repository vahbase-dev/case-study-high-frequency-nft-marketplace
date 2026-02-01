# 02 — Data Model and Ledger Synchronisation

## Off-Chain Ledger (PostgreSQL)
Primary tables (conceptual):
- `users`
- `wallets`
- `assets`
- `listings`
- `orders`
- `fills`
- `reservations`
- `tx_intents`
- `tx_receipts`
- `event_log` (append-only)

## Transactional Guarantees
- **Single-writer invariants** per wallet balance row (row-level locks)
- **Reservation-first** pattern to prevent double-spend
- **Append-only event_log** for traceability and replay

## Intent Lifecycle
1. Create `tx_intent` (type, payload hash, nonce, created_at)
2. Reserve funds / asset in DB transaction
3. Emit domain event: `intent.created`
4. Broadcast chain transaction
5. Record `tx_receipt` (hash, status, block, gas, timestamps)
6. Finalise: commit balance changes and close reservation

## Optimistic Updates
- UI is updated as soon as `intent.created` is accepted
- All optimistic states are marked `pending`
- Final state transitions only occur from indexed chain events or reconciled receipts

## Idempotency Strategy
- Unique key: `(intent_id, event_type, event_version)`
- Consumers must be replay-safe
- Duplicate on-chain events are ignored via dedupe keys

## Failure Modes
- **Dropped tx:** intent times out → reservation released
- **Reorg:** indexer replays from safe block → rebuilds derived views
- **Partial outage:** event_log ensures recovery without data loss

## Integrity Checks
- Periodic reconciliation: DB intent vs chain outcome
- Invariant audits: balance = deposits - withdrawals - open reservations
- Consistency alarms on mismatch thresholds
