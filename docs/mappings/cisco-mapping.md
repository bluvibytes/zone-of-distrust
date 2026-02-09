# Cisco Integrated AI Security Framework Mapping

**Zones of Distrust ↔ Cisco AI Security Framework (December 2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

Cisco's Integrated AI Security Framework is the most comprehensive threat taxonomy for agentic AI, cataloging 19 attacker objectives, 150+ techniques, and 112 subtechniques. This document maps Cisco's threat categories to ZoD controls.

**Relationship:** Cisco tells you *what attacks exist*. ZoD tells you *how to defend against them*.

**Key Cisco Resources:**
- OpenClaw dataset analysis (31,000+ agent skills, 26% vulnerable)
- Three scanners: MCP Scanner, A2A Scanner, Skill Scanner
- Regulatory mapping (EU AI Act, NIST, Budapest Convention)

---

## Attacker Objective Mapping

### Objective 1: Input Manipulation

**Cisco Techniques:** Prompt injection, context manipulation, instruction override, jailbreaking

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Direct prompt injection | L2 | Input screening before reasoning |
| Indirect prompt injection | L2 | Document/email content screening |
| Context window manipulation | L2 | Content classification, limitation ceiling |
| Instruction override | L2, L4 | Screening + semantic policy enforcement |
| Jailbreaking attempts | L2, L3 | Screening + cognitive isolation |

**Key Properties:** L2 screening, P12 (semantic enforcement regardless of justification)

---

### Objective 2: Social Engineering

**Cisco Techniques:** Agent-mediated phishing, trust exploitation, authority impersonation

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Agent-mediated phishing | L4 | Semantic policy blocks unauthorized communications |
| Trust exploitation | L7 | Human oversight for sensitive actions |
| Authority impersonation | L4 | Independent validation, not intent-based |
| Urgency manipulation | L6, L7 | Behavioral monitoring + escalation |

**Key Properties:** P11 (external policy authority), P12 (deterministic enforcement)

---

### Objective 3: Tool Exploitation

**Cisco Techniques:** Malicious tool injection, tool parameter manipulation, tool chaining attacks

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Malicious tool injection | L1 | Model provenance attestation |
| Tool parameter manipulation | L4 | Parameter binding (content hash) |
| Tool chaining attacks | L4 | Multi-step validation, scope checking |
| Unauthorized tool access | L4 | Semantic intent policy (tool-specific) |
| Tool output manipulation | L2 | Screen tool outputs like external input |

**Key Properties:** P4 (parameter-bound execution), L1 provenance

**Cisco Finding:** 26% of OpenClaw skills exploitable → L1 provenance verification critical

---

### Objective 4: Multi-Agent Coordination Attacks

**Cisco Techniques:** Agent collusion, trust chain exploitation, consensus manipulation

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Agent collusion | L6 | Cross-agent behavioral correlation |
| Trust chain exploitation | L4 | Independent validation at each hop |
| Consensus manipulation | L4 | Multi-agent chain validation |
| Peer agent injection | L2 | Inter-agent screening |
| Cascading compromise | L4, L6 | P10 non-propagation + correlation |

**Key Properties:** P10 (compromise non-propagation), P6 (cross-layer correlation)

---

### Objective 5: Memory Persistence Threats

**Cisco Techniques:** Memory poisoning, context persistence attacks, delayed execution

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Memory poisoning | L6 | Memory audit (scheduled inspection) |
| Context persistence attacks | L6 | Baseline comparison, source verification |
| Delayed execution | L4, L6 | Behavioral baseline + semantic policy |
| Memory exfiltration | L4 | Egress control policies |
| False memory injection | L2, L6 | Input screening + memory audit |

**Key Properties:** P5 (probabilistic memory poisoning detection)

---

### Objective 6: Multimodal Attack Vectors

**Cisco Techniques:** Image-based injection, audio manipulation, cross-modal attacks

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Image-based injection | L2 | Multimodal screening models |
| Audio manipulation | L2 | Audio content screening |
| Cross-modal attacks | L2 | Unified screening across modalities |
| Steganographic payloads | L2 | Deep content analysis |

**Key Properties:** L2 extensible screening architecture

---

### Objective 7: Cyber-Physical Manipulation

**Cisco Techniques:** Physical system compromise, IoT exploitation, robotic manipulation

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Physical system commands | L4 | Semantic policy (physical action constraints) |
| IoT device exploitation | L4, L7 | High-risk classification, human approval |
| Robotic manipulation | L7 | Critical risk requires multi-person approval |
| Safety system bypass | L4 | Negative constraints (prohibited actions) |

**Key Properties:** P11 (external policy authority), L7 escalation

---

### Objective 8: Detection Evasion

**Cisco Techniques:** Behavioral mimicry, gradual drift, monitoring bypass

**ZoD Controls:**

| Technique | ZoD Layer | Control |
|-----------|-----------|---------|
| Behavioral mimicry | L6 | Baseline comparison (statistical) |
| Gradual drift ("boiling frog") | L6 | Long-term baseline analysis |
| Monitoring bypass | L6 | Baseline integrity verification (P8) |
| Log manipulation | Integrity | Immutable, append-only channel |
| Alert flooding | L6 | Alert quality scoring, rate limiting |

**Key Properties:** P7 (immutable forensic record), P8 (baseline integrity verification)

---

## Cisco Scanner → ZoD Layer Mapping

| Cisco Scanner | Purpose | ZoD Integration Point |
|---------------|---------|----------------------|
| **MCP Scanner** | Detect malicious MCP servers | L1 (provenance), L2 (output screening) |
| **A2A Scanner** | Agent-to-agent vulnerabilities | L2 (inter-agent screening), L4 (chain validation) |
| **Skill Scanner** | Vulnerable agent skills | L1 (supply chain), L4 (scope restriction) |

---

## Cisco Lifecycle Phases → ZoD Layers

| Cisco Phase | ZoD Layers | Controls |
|-------------|------------|----------|
| **Design** | L7 | Policy definition, risk classification |
| **Development** | L1 | Provenance, supply chain verification |
| **Deployment** | L1, L3 | Identity establishment, isolation |
| **Operation** | L2, L4, L5 | Screening, validation, execution |
| **Monitoring** | L6, Integrity | Behavioral analysis, audit trail |
| **Retirement** | L1 | Credential revocation, identity cleanup |

---

## Summary Matrix

| Cisco Objective | Primary ZoD Layers | Key Properties |
|-----------------|-------------------|----------------|
| Input Manipulation | L2, L3, L4 | P12 |
| Social Engineering | L4, L6, L7 | P11 |
| Tool Exploitation | L1, L2, L4 | P4 |
| Multi-Agent Coordination | L2, L4, L6 | P10, P6 |
| Memory Persistence | L2, L4, L6 | P5 |
| Multimodal Attacks | L2 | L2 screening |
| Cyber-Physical | L4, L7 | P11 |
| Detection Evasion | L6, Integrity | P7, P8 |

---

## Verification Checklist

- [ ] MCP Scanner results inform L1/L2 configuration
- [ ] A2A Scanner results inform L4 chain validation rules
- [ ] Skill Scanner results inform L4 semantic policy
- [ ] All 19 attacker objectives mapped to controls
- [ ] Detection evasion countermeasures operational (P8)

---

## References

- [Cisco Integrated AI Security Framework](https://www.cisco.com/c/en/us/solutions/security/ai-security.html)
- [ZoD Architecture Specification](../docs/architecture.md)
- [ZoD Threat Model](../docs/threat-model.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
