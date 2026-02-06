# Roadmap

This roadmap describes near-term milestones for Zones of Distrust (ZoD) as an open specification and reference implementation.

## Milestone 0 — Docs + Spec Stabilization
- Publish ZoD white paper v3.1 (PDF)
- Publish companion spec v0.1 (normative)
- Publish JSON schemas for tokens, integrity events, delegation chains, memory events
- Define compliance levels (ZoD-L1..L5)

## Milestone 1 — ZoD-L1 Reference Implementation
- CA service issues parameter-bound, time-bound, single-use execution tokens
- Executor validates token signature, TTL, nonce, parameter binding, content binding
- Structured audit logs emitted for issuance + execution

## Milestone 2 — ZoD-L2 Policies
- Tool-specific semantic intent policy templates (email, web, DB read/write, file ops)
- Policy engine integration (OPA/Rego or equivalent)
- Deny + escalation behaviors

## Milestone 3 — ZoD-L3 Monitoring + Memory Audit MVP
- Baseline model: frequency, time-of-day, target distribution
- Drift alerts
- Memory event logging + periodic audit job
- Baseline integrity monitoring (drift-of-baseline)

## Milestone 4 — ZoD-L4 Integrity Signal Channel
- Append-only log backend adapter(s)
- Correlation queries (issued vs executed; mismatches; replay)
- Governance review dashboard hooks (optional)

## Milestone 5 — ZoD-L5 Multi-Agent Chain Validation
- Delegation chain schema + validation
- Chain risk scoring + aggregate abuse detection

## How to Help
We welcome contributions in:
- adversarial test cases,
- policy templates,
- executor adapters,
- schema reviews,
- threat modeling critiques.
