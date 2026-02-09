# OWASP Top 10 for Agentic Applications Mapping

**Zones of Distrust ↔ OWASP Agentic (December 2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

The OWASP Top 10 for Agentic Applications identifies the ten most critical security risks for autonomous AI systems. This document maps each OWASP risk to specific Zones of Distrust controls.

**Relationship:** OWASP tells you *what* can go wrong. ZoD tells you *how* to build defenses.

---

## Risk Mapping

### ASI01: Agent Goal Hijacking

**OWASP Definition:** Attackers manipulate agent objectives through prompt injection, context manipulation, or instruction override.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI01 |
|-------|---------|------------------------|
| L2 | Input screening | Detects prompt injection before agent processes it |
| L3 | Cognitive isolation | Compromised reasoning cannot directly execute |
| L4 | Semantic intent policy | Blocks actions outside authorized scope regardless of agent justification |
| L6 | Behavioral monitoring | Detects goal drift through request pattern analysis |

**Key Property:** P12 (Deterministic Semantic Enforcement) — Policy evaluates actions, not intentions.

---

### ASI02: Tool Misuse and Exploitation

**OWASP Definition:** Agents use tools in unintended ways or tools behave maliciously.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI02 |
|-------|---------|------------------------|
| L1 | Model provenance | Verifies tool authenticity before load |
| L4 | Semantic intent policy | Tool-specific constraints (e.g., email recipients, file paths) |
| L4 | Parameter binding | Token binds exact parameters—no parameter manipulation post-approval |
| L5 | Execution logging | All tool invocations recorded immutably |
| L6 | Behavioral monitoring | Detects unusual tool usage patterns |

**Key Property:** P4 (Parameter-Bound Execution) — Approved parameters cannot be modified.

**Real-World Example:** Postmark MCP BCC attack—semantic policy would detect unauthorized recipients.

---

### ASI03: Identity and Privilege Abuse

**OWASP Definition:** Agents operate with excessive privileges or impersonate other entities.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI03 |
|-------|---------|------------------------|
| L1 | Agent identity | Agent is distinct principal with own credentials |
| L1 | Scoped permissions | No standing privileges, just-in-time access |
| L1 | Credential broker | Agent never holds credentials directly |
| L4 | Authorization scope | Per-request scope evaluation |
| L4 | Token binding | Executor attestation—only designated executor can use token |

**Key Property:** P2 (Cryptographic Attribution) — Every action traceable to specific agent identity.

---

### ASI04: Supply Chain Vulnerabilities

**OWASP Definition:** Malicious or vulnerable components in agent dependencies, tools, or models.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI04 |
|-------|---------|------------------------|
| L1 | Model provenance | Cryptographic verification of model integrity |
| L1 | Supply chain verification | Component attestation before deployment |
| L2 | Tool output screening | Screens results from potentially compromised tools |
| L6 | Behavioral monitoring | Detects anomalies from compromised components |

**Key Property:** L1 model provenance attestation.

**Real-World Example:** PhantomRaven/Slopsquatting—provenance verification catches malicious packages.

---

### ASI05: Memory Poisoning

**OWASP Definition:** Attackers corrupt agent persistent memory to influence future behavior.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI05 |
|-------|---------|------------------------|
| L1 | Memory isolation | Memory inspectable through OS-level APIs |
| L2 | Input screening | Prevents poisoned content from entering memory |
| L4 | Behavioral baseline | Catches deviation from normal patterns (including poisoning effects) |
| L6 | Memory audit | External inspection of memory state, diff analysis, source verification |
| L6 | Anomaly detection | Flags entries without verified origin |

**Key Property:** P5 (Probabilistic Memory Poisoning Detection) — External memory audit as distinct monitoring function.

**Real-World Example:** Procurement agent memory poisoning—memory audit detects anomalous accumulation.

---

### ASI06: Human Trust Manipulation

**OWASP Definition:** Agents manipulate human operators through social engineering or false urgency.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI06 |
|-------|---------|------------------------|
| L4 | Semantic intent policy | Negative constraints block manipulation patterns |
| L6 | Behavioral monitoring | Detects unusual human interaction patterns |
| L7 | Risk-weighted escalation | High-impact actions require human approval regardless of agent persuasion |
| L7 | Policy opacity | Agent cannot see what triggers escalation |

**Key Property:** P11 (External Policy Authority) — Humans define policy, not agents.

---

### ASI07: Inter-Agent Communication Compromise

**OWASP Definition:** Malicious agents exploit trust in multi-agent systems to propagate attacks.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI07 |
|-------|---------|------------------------|
| L2 | Inter-agent screening | Agent-to-agent messages screened like external input |
| L4 | Multi-agent chain validation | Independent trust verification at each hop |
| L4 | Chain-of-custody | Request provenance tracked across agent network |
| L6 | Cross-agent correlation | Detects coordinated behavioral drift |

**Key Property:** P10 (Compromise Non-Propagation) — Compromised agent cannot issue valid tokens for peers.

**Real-World Example:** A2A session smuggling—chain validation prevents trust exploitation.

---

### ASI08: Cascading Failures

**OWASP Definition:** Failures in one component propagate through the system causing widespread impact.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI08 |
|-------|---------|------------------------|
| L2 | Degraded modes | Graceful degradation when screening fails |
| L4 | Scope isolation | Each request evaluated independently |
| L5 | Execution isolation | Failures contained to individual actions |
| All | Layered defense | Each layer operates independently |

**Key Property:** P9 (Graceful Degradation) — System maintains safety posture during partial failures.

**Degradation Modes:**
- Quarantine-with-summary (L2 overload)
- Reduced authorization scope (L4 degraded)
- Fail-closed execution (L5 uncertainty)

---

### ASI09: Rogue Agent Behavior

**OWASP Definition:** Agents act autonomously in unauthorized or harmful ways.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI09 |
|-------|---------|------------------------|
| L3 | Cognitive isolation | Reasoning separated from execution |
| L4 | Every-action validation | No action without CA approval |
| L4 | Autonomy levels | Escalating controls proportional to agent independence |
| L6 | Behavioral baselining | Detects deviation from established patterns |
| L6 | Continuous monitoring | Real-time observation of agent behavior |
| L7 | Human oversight | Kill switch and quarantine capabilities |

**Key Property:** P1 (No Unmediated Execution) — Agent cannot bypass CA for any action.

---

### ASI10: Inadequate Monitoring and Observability

**OWASP Definition:** Insufficient visibility into agent behavior prevents detection and response.

**ZoD Controls:**

| Layer | Control | How It Addresses ASI10 |
|-------|---------|------------------------|
| L5 | Immutable logging | All actions recorded with full context |
| L6 | Behavioral monitoring | Continuous observation with baseline comparison |
| L6 | Memory audit | Visibility into agent persistent state |
| L6 | Baseline integrity verification | Monitors the monitoring system itself |
| Integrity channel | Cross-layer correlation | Anomaly detection through signal correlation |
| Integrity channel | Immutable audit trail | Tamper-proof forensic record |

**Key Properties:** 
- P6 (Cross-Layer Correlation)
- P7 (Immutable Forensic Record)
- P8 (Baseline Integrity Verification)

---

## Summary Matrix

| OWASP Risk | Primary ZoD Layers | Key Properties |
|------------|-------------------|----------------|
| ASI01: Goal Hijacking | L2, L3, L4, L6 | P12 |
| ASI02: Tool Misuse | L1, L4, L5, L6 | P4 |
| ASI03: Privilege Abuse | L1, L4 | P2 |
| ASI04: Supply Chain | L1, L2, L6 | L1 provenance |
| ASI05: Memory Poisoning | L1, L2, L4, L6 | P5 |
| ASI06: Trust Manipulation | L4, L6, L7 | P11 |
| ASI07: Inter-Agent Compromise | L2, L4, L6 | P10 |
| ASI08: Cascading Failures | L2, L4, L5, All | P9 |
| ASI09: Rogue Agent | L3, L4, L6, L7 | P1 |
| ASI10: Inadequate Monitoring | L5, L6, Integrity | P6, P7, P8 |

---

## Verification Checklist

For each OWASP risk, verify ZoD controls are operational:

- [ ] **ASI01:** L2 screening active, L4 semantic policy enforced
- [ ] **ASI02:** Tool provenance verified, parameter binding enforced
- [ ] **ASI03:** Agent identity established, credential broker operational
- [ ] **ASI04:** Supply chain verification in CI/CD, provenance attestation
- [ ] **ASI05:** Memory audit scheduled, baseline established
- [ ] **ASI06:** Escalation thresholds defined, approval workflows tested
- [ ] **ASI07:** Inter-agent screening enabled, chain validation operational
- [ ] **ASI08:** Degraded modes tested, fail-closed verified
- [ ] **ASI09:** Behavioral baseline established, drift detection active
- [ ] **ASI10:** Logging immutable, cross-layer correlation operational

---

## References

- [OWASP Top 10 for Agentic Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [ZoD Architecture Specification](../docs/architecture.md)
- [ZoD Security Properties](../docs/security-properties.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
