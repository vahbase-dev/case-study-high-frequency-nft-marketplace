# High-Frequency NFT Marketplace — Case Study (Docs-only)

![Cover](./cover.png)

## Overview
A high-throughput NFT marketplace architecture focused on deterministic execution, atomic integrity across off-chain and on-chain state, and sub-second trade finality under peak load.

## What I Built
- Custom synchronisation layer bridging off-chain PostgreSQL state with on-chain Polygon smart contracts
- Optimistic UI update model backed by rigorous event indexing
- Deterministic reconciliation to eliminate race conditions across ledgers

## Key Outcomes
- Reduced trade latency by 40% (sub-second execution)
- Processed 10k+ daily transactions with zero race-condition errors
- Cut gas costs by 30% via optimised Graph indexing

## Architecture Highlights
- **Server runtime:** Node.js
- **Database:** PostgreSQL
- **On-chain:** Solidity smart contracts (Polygon)
- **Indexing:** Event-driven indexer + reconciliation pipeline
- **Consistency model:** Optimistic writes + authoritative chain events + idempotent replay

## Docs
- [00 — Index](./docs/00-index.md)
- [01 — Architecture Overview](./docs/01-architecture-overview.md)
- [02 — Data Model and Ledger Synchronisation](./docs/02-data-model-and-ledger-sync.md)
- [03 — Event Indexing and Reconciliation](./docs/03-event-indexing-and-reconciliation.md)
- [04 — Performance and Observability](./docs/04-performance-and-observability.md)
- [05 — Security and Failure Modes](./docs/05-security-and-failure-modes.md)


## Notes
This repository is documentation-only and contains no proprietary source code.
