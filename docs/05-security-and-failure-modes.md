# 05 — Security and Failure Modes

## Security Model
- Server-authoritative intent validation
- Strict authentication and request signing
- Rate limiting + anomaly detection
- Least-privilege secrets management for chain keys

## Smart Contract Safety (Conceptual)
- Reentrancy protections where applicable
- Explicit nonce / replay protection
- Event completeness for all state transitions
- Upgrade and admin controls documented and audited

## Off-Chain Protections
- Transaction boundaries for all ledger mutations
- Row-level locks to prevent double-spend
- Reservation system with expiry and recovery
- Append-only audit log for intent and reconciliation

## Failure Modes and Responses
- **Chain congestion:** intents remain pending; UI reflects delayed finality
- **Dropped tx:** timeout → intent expired → reservation released
- **Reorg:** rebuild projections from safe checkpoint
- **Indexer outage:** backlog grows; reconciliation pauses non-critical finality
- **DB contention:** degrade non-critical writes; prioritise ledger path
- **Partial deploy:** feature flags + rollback plan

## Data Integrity Checks
- Scheduled balance audits
- Sampling audits on fills vs events
- Alerting on mismatch rate thresholds

## Incident Playbooks
- Pause new intents (circuit breaker)
- Drain queues and stabilise indexer
- Reconcile pending intents in batches
- Post-incident report with timelines and corrective actions
