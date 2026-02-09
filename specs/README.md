# ZoD Specifications

This folder contains normative specifications and machine-readable schemas for the Zones of Distrust architecture.

## Contents

| File | Description | Status |
|------|-------------|--------|
| [ZOD-SPEC-v0.9-RFC.md](ZOD-SPEC-v0.9-RFC.md) | Companion Specification (normative requirements) | RFC Draft |
| [schemas/](schemas/) | JSON Schema definitions | Draft |

## Companion Specification

The **ZOD-SPEC** defines:

- Conformance language (RFC 2119)
- Definitions for all ZoD components
- Security model requirements (normative)
- Threat model and trust assumptions
- Layer-by-layer requirements (L1-L7)
- Execution token structure and cryptographic requirements
- Compliance levels (ZoD-L1 through ZoD-L5)
- Testability requirements

## Schemas

Machine-readable JSON Schema definitions for ZoD data structures:

| Schema | Purpose | Spec Reference |
|--------|---------|----------------|
| `execution-token.schema.json` | Execution token structure | Section 9 |
| `integrity-event.schema.json` | ISC event format | Section 13 |
| `delegation-chain.schema.json` | Multi-agent chain format | Section 14 |
| `memory-event.schema.json` | Memory audit event format | Section 12 |

### Schema Status

> **⚠️ Draft Status:** These schemas have not been validated against a reference implementation. They represent the intended structure based on the specification but may require adjustments during implementation.

See [ROADMAP.md](../ROADMAP.md) Milestone 1 for implementation timeline.

## Compliance Levels Summary

| Level | Name | Key Requirements |
|-------|------|------------------|
| **ZoD-L1** | Tokenized Execution | CA issuance, token validation, TTL + nonce, parameter binding |
| **ZoD-L2** | Semantic Intent Policy | Tool-specific constraints, policy engine, policy opacity |
| **ZoD-L3** | Monitoring & Memory Audit | Drift detection, memory audit, goal drift detection |
| **ZoD-L4** | Integrity Signal Channel | Append-only ISC, correlation, backpressure handling |
| **ZoD-L5** | Multi-Agent Chain | Delegation schema, chain validation, collective abuse detection |

## Related Documents

- [Architecture Specification](../docs/ARCHITECTURE.md)
- [Security Properties](../docs/security-properties.md)
- [Threat Model](../docs/threat-model.md)
- [Implementation Guide](../docs/implementation-guide.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
