# Zones of Distrust Documentation

**Version 0.9 RFC — February 2026**

This directory contains the complete technical documentation for the Zones of Distrust security architecture.

---

## Core Documents

| Document | Description |
|----------|-------------|
| **[Full Architecture Specification](architecture.md)** | Complete technical specification of the seven-layer model |
| **[White Paper](whitepaper.md)** | Comprehensive overview for security professionals |
| **[Executive Brief](executive-brief.md)** | High-level summary for leadership and decision-makers |

---

## Layer Documentation

The [Architecture Specification](architecture.md) contains detailed specifications for each layer:

| Layer | Name | Section |
|-------|------|---------|
| L1 | OS Foundation | [Process isolation, credential brokering, identity](architecture.md#layer-1-os-foundation) |
| L2 | Input Control | [Content screening, injection detection](architecture.md#layer-2-input-control) |
| L3 | Cognitive Isolation | [Reasoning/execution separation](architecture.md#layer-3-cognitive-isolation) |
| L4 | Request Validation (CA) | [Certificate Authority, semantic policy](architecture.md#layer-4-request-validation--certificate-authority--policy-decision-plane) |
| L5 | Execution | [Token-gated action execution](architecture.md#layer-5-execution) |
| L6 | Continuous Monitoring | [Behavioral baselines, anomaly detection](architecture.md#layer-6-continuous-monitoring) |
| L7 | Human Governance | [Policy management, escalation, oversight](architecture.md#layer-7-human-governance) |

---

## Security & Implementation

| Document | Description |
|----------|-------------|
| **[Security Properties](security-properties.md)** | 12 measurable security properties (P1-P12) with verification criteria |
| **[Threat Model](threat-model.md)** | Attacker positions, trust assumptions, degradation modes |
| **[Implementation Guide](implementation-guide.md)** | Practical deployment guidance and reference architecture |

---

## Research & Context

| Document | Description |
|----------|-------------|
| **[Threat Model](threat-model.md)** | Threat landscape analysis and attack surface mapping |
| **[Framework Positioning](framework-positioning.md)** | How ZoD relates to and extends existing frameworks |
| **[Zero Trust Mapping](../mappings/zero-trust-mapping.md)** | Detailed comparison to Zero Trust architecture |
| **[Industry Landscape](../mappings/industry-mapping.md)** | Analysis of current agentic security landscape |

---

## Perspectives

| Document | Description |
|----------|-------------|
| **[The AI's Perspective](ai-perspective.md)** | Honest assessment of structural vulnerabilities from the agent's point of view |

---

## Framework Mappings

Complete mappings to industry frameworks are available in the [mappings/](../mappings/) directory:

- [OWASP Agentic Top 10](../mappings/owasp-agentic-mapping.md)
- [OWASP LLM Top 10](../mappings/owasp-llm-mapping.md)
- [MITRE ATLAS](../mappings/mitre-atlas-mapping.md)
- [NIST AI RMF](../mappings/nist-ai-rmf-mapping.md)
- [Google SAIF](../mappings/google-saif-mapping.md)
- [Microsoft AIRT](../mappings/microsoft-airt-mapping.md)
- [Cisco AI Security](../mappings/cisco-mapping.md)
- [NVIDIA/Lakera](../mappings/nvidia-lakera-mapping.md)
- [And more...](../mappings/README.md)

---

## Implementation Guides

*Detailed implementation guides coming Q2 2026:*

- Agent Framework Integration Guide
- Enterprise Deployment Guide
- Security Operations Playbook

For current implementation guidance, see the [Implementation Guide](implementation-guide.md).

---

## Attack Scenarios

Documented attack scenarios demonstrating ZoD defenses are available in the [attacks/](../attacks/) directory:

- [Prompt Injection](../attacks/prompt-injection/)
- [Memory Poisoning](../attacks/memory-poisoning/)
- [Tool Misuse](../attacks/tool-misuse/)
- [Chain Delegation](../attacks/chain-delegation/)
- [Replay Attacks](../attacks/replay/)

---

## Quick Start

1. **New to ZoD?** Start with the [Executive Brief](executive-brief.md)
2. **Security professional?** Read the [White Paper](whitepaper.md)
3. **Implementing?** See the [Implementation Guide](implementation-guide.md)
4. **Evaluating security claims?** Review [Security Properties](security-properties.md)
5. **Understanding threats?** Study the [Threat Model](threat-model.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
