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
| **Monitoring is operational** | If L6 is down, behavioral detection doesn't happen |

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

```text
                              ┃ POLICY ┃
                              ┃ flows  ┃
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
                              ┃ flows  ┃
                              ┃   UP   ┃
