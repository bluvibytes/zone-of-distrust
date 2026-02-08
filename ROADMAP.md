# Roadmap

This roadmap describes near-term milestones for Zones of Distrust (ZoD) as an open specification and reference implementation.

ZoD is a **7-layer security architecture** (OSI-style) for autonomous AI agents.  
ZoD is intended to be **complementary** to existing security standards and frameworks, not a replacement.

ZoD also defines **compliance maturity levels** (ZoD-L1 through ZoD-L5) that represent progressively stronger enforcement and operational maturity across the architecture.

---

## ZoD Architecture Layers (Reference)

ZoD is organized into seven architectural layers:

1. **OS Foundation**
2. **Input Control**
3. **Cognitive Isolation (Reasoning)**
4. **Request Validation (CA)**
5. **Execution (Executor)**
6. **Continuous Monitoring**
7. **Human Governance**

---

## Milestone 0 — Specification + Documentation Stabilization (RFC v0.9)

Goal: establish a stable public baseline for peer review and collaboration.

- Publish ZoD White Paper (RFC v0.9)
- Publish ZoD Companion Spec (normative requirements, RFC v0.9)
- Publish architecture reference (`ARCHITECTURE.md`)
- Publish threat model baseline (`THREAT_MODEL.md`)
- Publish security properties (`SECURITY_PROPERTIES.md`)
- Publish implementation guide (`IMPLEMENTATION_GUIDE.md`)
- Publish JSON schemas for:
  - execution tokens
  - integrity events
  - delegation chains
  - memory events
- Define compliance maturity levels (ZoD-L1 through ZoD-L5)
- Publish mapping suite (OWASP, NIST, MITRE ATLAS, Cisco, CSA, ENISA, CoSAI, SAIF, ISO/SOC2, etc.)
- Establish contribution and governance scaffolding:
  - `SECURITY.md`
  - `CODE_OF_CONDUCT.md`
  - `CONTRIBUTING.md`
  - `GOVERNANCE.md`
  - dual licensing structure (CC BY + Apache 2.0)

---

## Milestone 1 — ZoD-L1 Minimal Reference Implementation (Core Enforcement)

Goal: implement the minimum enforceable separation between reasoning and execution.

This milestone primarily operationalizes Layers 3–5.

- CA service issues parameter-bound, time-bound, single-use execution tokens
- Executor validates:
  - token signature
  - TTL expiration
  - nonce / replay protection
  - parameter binding
  - content binding (where applicable)
- Structured audit logs emitted for issuance + execution events
- Minimal integration example with a simple agent framework (reference-only)

Deliverable: a minimal end-to-end demo where the reasoning agent cannot directly execute privileged actions.

---

## Milestone 2 — Adversarial Test Suite (ZoD Attack Harness v0.1)

Goal: provide reproducible adversarial tests that validate ZoD security claims.

- Prompt injection test cases targeting action execution
- Token replay / substitution test cases
- Parameter tampering test cases
- Orphaned action test cases (executor executes without CA issuance)
- Tool misuse escalation attempts (multi-step abuse patterns)
- Memory poisoning test cases (baseline examples)
- Test harness emits structured integrity events

Deliverable: a reusable benchmark suite for evaluating ZoD-L1 compliance.

---

## Milestone 3 — ZoD-L2 Policy Layer (Semantic Intent Enforcement)

Goal: move beyond allowlists into enforceable semantic policy constraints.

This milestone strengthens Layer 4 and expands enforcement depth.

- Tool-specific semantic intent policy templates (email, web, DB read/write, file ops)
- Policy engine integration (OPA/Rego or equivalent)
- Deny + escalation behaviors
- Policy opacity support (policy not exposed to reasoning agent by default)
- Expanded test suite coverage validating semantic constraints

Deliverable: reference policies + enforcement patterns that demonstrate practical intent-based constraints.

---

## Milestone 4 — ZoD-L3 Monitoring + Memory Audit MVP (Behavioral Integrity)

Goal: detect drift, poisoning, and anomalous behavior over time.

This milestone operationalizes Layer 6.

- Baseline behavior model:
  - frequency
  - time-of-day
  - target distribution
  - tool invocation distribution
- Drift alerts and anomaly scoring
- Memory event logging + periodic audit job
- Provenance tagging for memory writes
- Baseline integrity monitoring (drift-of-baseline detection)

Deliverable: a minimal monitoring plane capable of detecting suspicious behavioral changes and memory anomalies.

---

## Milestone 5 — ZoD-L4 Integrity Signal Channel (Correlation Plane)

Goal: enable cryptographically trustworthy audit correlation across CA + executor.

This milestone strengthens the integrity plane supporting Layers 4–6.

- Append-only integrity log backend adapter(s)
- Correlation queries:
  - issued vs executed mismatches
  - denied vs executed anomalies
  - replay attempts
  - orphaned actions
- Governance review hooks (optional dashboards)

Deliverable: a correlation-capable integrity signal channel enabling auditability and detection of enforcement bypass attempts.

---

## Milestone 6 — ZoD-L5 Multi-Agent Chain Validation (Delegation Security)

Goal: secure delegation and chained autonomy across agents.

This milestone expands Layer 5 enforcement for multi-agent environments.

- Delegation chain schema + validation
- Chain context binding into execution tokens
- Chain risk scoring + abuse detection
- Aggregate monitoring across multi-agent execution graphs
- Test suite expansion for delegation abuse an
