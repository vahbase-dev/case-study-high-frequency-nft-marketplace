# 01 — Architecture Overview

## Goal
Deliver sub-second trade execution with deterministic settlement while maintaining atomic integrity across:
- Off-chain state (PostgreSQL)
- On-chain state (Polygon smart contracts)

## Core Principles
- Server-authoritative processing
- Deterministic outcomes once inputs are known
- Event-driven state propagation
- Idempotent consumers and replay-safe pipelines

## High-Level Components
- **API Gateway (Node.js):** authenticated client entrypoint, request validation, rate limiting
- **Trade Orchestrator:** validates orders, locks resources, emits domain events
- **PostgreSQL Ledger:** canonical off-chain balances, orders, fills, reservations
- **Chain Adapter:** submits transactions, tracks confirmations, maps receipts to domain events
- **Event Indexer:** ingests chain events, builds queryable materialised views
- **Reconciliation Worker:** resolves divergences between DB intents and chain results
- **Cache / Queue (optional):** Redis + message bus for fan-out and backpressure

## Data Flow
1. Client submits intent (place bid / list / cancel)
2. Orchestrator creates DB intent + reservation (transactional)
3. UI receives optimistic acknowledgement (pending state)
4. Chain Adapter broadcasts transaction and tracks lifecycle
5. Indexer ingests on-chain events and persists canonical outcome
6. Reconciliation finalises DB state and resolves pending intents

## Consistency Model
- **Optimistic UI:** fast feedback
- **Authoritative settlement:** on-chain events
- **Atomicity:** DB transaction boundaries for intents + reservations
- **Safety:** replayable consumers + idempotent writes

## Performance Targets
- Sub-second perceived execution
- Predictable tail latency under burst load
- Zero race conditions across concurrent intents
