# Architecture Specification

**Zones of Distrust: Seven-Layer Reference Architecture**

*Version 0.9 RFC — February 2026*

---

## Overview

> **Breach is the premise. Containment is the architecture.**

The Zones of Distrust (ZoD) are organized as seven layers with a directional logic. Like the OSI reference model, each layer provides services to the layer above it and depends on the layer below it. However, where OSI layers represent protocol abstractions, ZoD layers represent *enforcement boundaries*—points where trust is withdrawn and validation is required.

**Each layer assumes every layer above it has already been compromised.**

ZoD is intended to be complementary to existing security standards and frameworks, not a replacement.

---

## Assumptions and Non-Goals

### Assumptions (What ZoD Requires)

ZoD provides value only when these conditions are met:

| Assumption | Consequence if Violated |
|------------|--------------------------|
| **Infrastructure is trustworthy** | If OS, hypervisor, or hardware is compromised, ZoD controls can be bypassed |
| **Cryptographic primitives work** | If signatures can be forged, token binding fails |
| **Network segmentation is enforced** | If L3 can reach L5 directly, isolation is defeated |
| **Implementation is correct** | Bugs in CA, executor, or monitoring negate architectural properties |
| **Configuration is correct** | Overly permissive policies or disabled monitoring remove protection |
| **Monitoring is operational** | If L6 is down, behavioral detection doesn’t happen |

### Non-Goals (What ZoD Does Not Attempt)

| Non-Goal | Why |
|----------|-----|
| **Making agents trustworthy** | ZoD assumes agents cannot be trusted; it governs untrusted actors |
| **Preventing all attacks** | ZoD increases attack cost and limits blast radius; no architecture stops everything |
| **Replacing compliance programs** | ZoD provides controls and evidence; compliance requires broader organizational work |
| **Solving infrastructure security** | ZoD assumes secure infrastructure; it does not replace IAM, KMS, network security, or OS hardening |
| **Detecting all poisoning** | Some detection is probabilistic; novel attacks may evade monitoring |
| **Guaranteeing performance** | Security controls add latency; ZoD does not guarantee sub-millisecond validation |

---

## Architecture Diagram

~~~text
                              ┃ POLICY ┃
                              ┃ FLOWS  ┃
                              ▼ DOWN   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L7  HUMAN GOVERNANCE                                               │   │
│   │      Risk-weighted escalation • Policy-setting • Consumes signals   │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                         │                         │
│         │ Alerts, anomalies                       │ Policy, thresholds      │
│         │                                         ▼                         │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L6  CONTINUOUS MONITORING            ◄───────────────────────────► │   │
│   │      Behavioral baseline • Drift detection • Memory audit           │   │
│   │      ┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈   │   │
│   │      Watches L3-L5 from OUTSIDE (cannot be influenced by agent)     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                         │ MONITORING              │
│         │ Immutable logs                          │ flows ACROSS            │
│         │                                         ▼                         │
│   ╔═════════════════════════════════════════════════════════════════════╗   │
│   ║  L5  EXECUTION                                                      ║   │
│   ║      Isolated process • Agent has NO ACCESS • Logs immutably        ║   │
│   ║─────────────────────────────────────────────────────────────────────║   │
│   ║         ▲ Tokens                        │ Results                   ║   │
│   ╠═════════════════════════════════════════════════════════════════════╣   │
│   ║  L4  REQUEST VALIDATION (Certificate Authority)                     ║   │
│   ║      Independent CA • Scope • Baseline • Risk • Semantic intent     ║   │
│   ║─────────────────────────────────────────────────────────────────────║   │
│   ║         ▲ Requests                      │ Denials/Constraints       ║   │
│   ╠═════════════════════════════════════════════════════════════════════╣   │
│   ║  L3  COGNITIVE ISOLATION                                            ║   │
│   ║      Agent can REASON but cannot ACT • Reasoning ≠ Execution        ║   │
│   ╚═════════════════════════════════════════════════════════════════════╝   │
│         ▲                                         │                         │
│         │ Sanitized input                         │ (No direct path out)    │
│         │                                         ▼                         │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L2  INPUT CONTROL                                                  │   │
│   │      Adversarial screening • Prompt injection • Persuasion patterns │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                                                   │
│         │ Raw input                                                         │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L1  OS FOUNDATION                                                  │   │
│   │      Agent identity • Process isolation • Credential brokering •    │   │
│   │      Scoped permissions • Model provenance attestation              │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                                                   │
└─────────────────────────────────────────────────────────────────────────────┘
                              ┃  DATA  ┃
                              ┃ FLOWS  ┃
                              ┃   UP   ┃
~~~

The double-line box highlights **L3–L4–L5** as the **core trust boundary**: the critical separation between reasoning and execution.

---

## Directional Logic

ZoD layers interact through three directional flows.

### Data Flows Up (L1 → L2 → L3 → L4 → L5)

External data enters through Layer 1, is screened at Layer 2, reaches the agent’s reasoning at Layer 3, produces a structured request that flows to Layer 4 for validation, and if approved, results in execution at Layer 5.

**Security requirement:** there must be no privileged “backchannel” from the agent directly to execution (e.g., direct tool calls, direct credential access, or direct network reachability to executor services).

### Policy Flows Down (L7 → L6 → L4 → L2)

Layer 7 governance defines policy and thresholds that propagate downward:

- Monitoring thresholds → Layer 6  
- Validation rules and risk thresholds → Layer 4  
- Screening configurations and degraded modes → Layer 2  

**Security requirement:** policy never flows up. The agent cannot modify enforcement rules, thresholds, or monitoring configuration.

### Monitoring Flows Across (L6 observes L3–L5)

Layer 6 observes the reasoning-to-action pipeline from the outside:

- Reasoning/request formation signals (where observable)  
- CA decisions and token issuance/denial patterns  
- Execution outcomes and audit signals  

**Security requirement:** monitoring must not rely on agent self-reporting.

---

## The Failure Cascade

Each layer assumes layers above it may fail:

| If This Fails… | This Layer Catches It |
|---|---|
| L2: input screening is bypassed | L3: agent still cannot execute directly |
| L3: reasoning is compromised | L4: CA refuses unauthorized execution |
| L4: CA is misconfigured/fooled | L6: correlation + drift/anomaly detection |
| L6: monitoring misses a pattern | L7: escalation thresholds and human review for high-risk actions |

The design objective is not “perfect prevention,” but **forcing an attacker to defeat multiple independent controls** to cause harm.

---

## Layer Specifications

### Layer 1: OS Foundation

Layer 1 provides the security substrate that makes higher layers enforceable:

- workload/process isolation  
- identity and credentials separation  
- network segmentation primitives  
- provenance and integrity signals  

**Implementable today (example mapping):**
- Agent identity → Kubernetes ServiceAccounts + SPIFFE/SPIRE  
- Isolation → Containers + NetworkPolicy  
- Credential brokering → Vault / STS / short-lived credentials  
- Scoped permissions → RBAC + admission controls  
- Provenance → signed images + attestations  

**Key requirement:** the reasoning environment must not have direct access to execution credentials or execution networks.

---

### Layer 2: Input Control

Layer 2 screens and annotates inbound data (email/web/docs/messages/inter-agent data) for injection patterns and manipulation attempts.

**Key requirement:** Layer 2 is treated as a *risk reduction layer*, not a guarantee. ZoD assumes sophisticated attacks will bypass screening.

**Degraded modes (operational safety):**
- Quarantine-with-summary  
- Dual-path processing  
- Adaptive sensitivity fallback (block → tag when false positives are high)  

Degraded mode selection is governed by Layer 7 policy, not by the agent.

---

### Layer 3: Cognitive Isolation

Layer 3 is where the agent reasons. It can plan and propose actions, but cannot execute privileged operations.

**Key requirement:** reasoning produces structured requests, not direct tool calls.

**Security property:** even fully compromised reasoning must not equate to privileged execution.

---

### Layer 4: Request Validation (AI Certificate Authority)

Layer 4 validates requests from the agent against:

- authorization scope  
- semantic constraints (policy)  
- risk thresholds and escalation rules  
- behavioral context signals (from Layer 6)  
- multi-agent chain context (when applicable)  

**Output:** a single-use, time-bound execution token that is bound to the approved action and its parameters.

**Key requirement:** the agent cannot bypass Layer 4 to reach Layer 5.

---

### Layer 5: Execution

Layer 5 is the isolated executor that performs actions only when presented with a valid token.

**Key requirement:** the executor must validate:
- signature  
- TTL  
- nonce / replay protection  
- parameter/content binding  
- executor identity binding (where used)  

Every execution emits audit events into the integrity signal channel.

---

### Layer 6: Continuous Monitoring

Layer 6 observes Layers 3–5 from outside the agent process.

**Minimum viable signals (examples):**
- token issuance/denial rate  
- novelty of action/target pairs  
- tool usage distribution drift  
- execution outcomes vs expected patterns  
- memory event provenance signals (when applicable)  

Layer 6 exists to detect:
- anomalies (sharp deviations)  
- drift (slow compromise / poisoning effects)  
- correlation patterns across layers  

---

### Layer 7: Human Governance

Layer 7 defines policy, risk thresholds, escalation logic, and operational controls.

**Risk-weighted escalation:**
- low risk → automatic  
- medium risk → enhanced monitoring/logging  
- high risk → human review required  

**Key requirement:** policy authority is human-controlled and propagates downward only.

---

## Cross-Layer Compromise Reporting

A compromised layer cannot reliably report its own compromise. ZoD therefore treats integrity reporting as cross-cutting and correlation-based.

### Integrity Signal Channel

All layers emit integrity/audit events into a channel that is:

- **Immutable** (append-only, cryptographically verifiable)  
- **Out-of-band** (not suppressible by agent-controlled channels)  
- **Correlation-based** (patterns across layers matter more than self-reports)  

Example implementations include WORM storage, transparency logs, or signed append-only event ledgers.

---

## What This Is and What This Is Not

- **Not just prompt injection filtering:** Layer 2 is expected to fail sometimes.
- **Not model alignment:** ZoD assumes reasoning can be manipulated.
- **Not conventional IAM:** ZoD governs *what* gets executed (with bindings), not only *who* asked.
- **An execution governance architecture:** it constrains action even when reasoning is compromised.

> *Security is not about making the agent trustworthy. It is about building a system that works even when the agent is not.*

---

## Related Documents

- `security-properties.md`
- `threat-model.md`
- `implementation-guide.md`

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
