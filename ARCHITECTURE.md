# Architecture

This repository contains the Zones of Distrust (ZoD) specification and (in-progress) reference components for securing autonomous AI agents by separating reasoning from execution and enforcing CA-issued, parameter-bound execution tokens.

## Status

**Current state:** early-stage scaffolding.
- The white paper and companion spec define the ZoD model and requirements.
- Schemas define the core interchange formats (tokens, integrity events, memory events, delegation chains).
- Reference implementation components are under active development.

This file is intentionally concise and will expand as the reference implementation stabilizes.

## Core Components (Conceptual)

ZoD is organized into seven layers:

1. **OS Foundation**
2. **Input Control**
3. **Cognitive Isolation (Reasoning)**
4. **Request Validation (CA)**
5. **Execution (Executor)**
6. **Continuous Monitoring**
7. **Human Governance**

Directional logic:
- **Data flows up** (input → reasoning → request → validation → execution)
- **Policy flows down**
- **Monitoring flows across** (observing Layers 3–5)

## Minimal Deployable Reference Architecture (Today)

A practical ZoD deployment on current infrastructure (e.g., Kubernetes) typically uses:

- **Reasoning Pod (no-egress):** agent reasoning runs without direct network access to privileged services.
- **PEP Boundary (sidecar or mesh):** intercepts structured requests and routes them to the CA.
- **CA Service:** validates requests against policy, risk, baseline context, and chain context; issues execution tokens.
- **Executor Service:** validates tokens and performs external actions; logs everything immutably.
- **Integrity Signal Channel:** append-only log for issuance, denials, executions, mismatches, replays, monitoring alerts.
- **Monitoring Service:** drift detection + memory audit; correlates signals across CA and executor.

## Component Responsibilities (Normative Pointers)

This architecture document is descriptive; normative requirements live in:
- `specs/ZOD-SPEC-v0.1.md`

Key enforcement rules:
- The reasoning process **cannot** execute actions directly.
- Executors **must** validate token signature, TTL, nonce, parameter binding, and content binding.
- Orphaned actions, mismatched actions, and replay attempts must be logged to the Integrity Signal Channel.

## Repo Structure

- `specs/`
  - `ZOD-SPEC-v0.1.md` — Companion spec (normative)
  - `schemas/` — JSON schemas for tokens/events
- `docs/` — White paper and supporting material
- `attacks/` — (planned) adversarial test suite harness and test cases

## Implementation Roadmap (High Level)

Mapped to compliance levels in `COMPLIANCE.md`:

- **ZoD-L1:** CA + parameter-bound, time-bound, single-use execution tokens + executor enforcement
- **ZoD-L2:** semantic intent policy enforcement (tool-specific negative constraints)
- **ZoD-L3:** monitoring + memory audit + baseline integrity verification
- **ZoD-L4:** out-of-band integrity signal channel + correlation
- **ZoD-L5:** multi-agent chain validation

## Open Questions / Contributions Wanted

We actively welcome contributions and critique in:
- execution token schema design
- policy language design for semantic intent constraints
- adversarial test cases (prompt injection, memory poisoning, replay, chain abuse)
- reference architecture improvements for real-world infra

See `CONTRIBUTING.md` for how to participate.
