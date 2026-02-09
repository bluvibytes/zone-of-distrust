# Zones of Distrust Documentation

**Version 0.9 RFC — February 2026**

This directory contains the complete technical documentation for the Zones of Distrust security architecture.

---

## Core Documents

| Document | Description |
|----------|-------------|
| **[Architecture Specification](ARCHITECTURE.md)** | Complete technical specification of the seven-layer model |
| **[Executive Brief](executive-brief.md)** | High-level summary for leadership and decision-makers |

---

## Layer Documentation

The [Architecture Specification](ARCHITECTURE.md) contains detailed specifications for each layer:

| Layer | Name | Focus |
|-------|------|-------|
| L1 | OS Foundation | Process isolation, credential brokering, agent identity |
| L2 | Input Control | Content screening, injection detection, degraded modes |
| L3 | Cognitive Isolation | Reasoning/execution separation |
| L4 | Request Validation (CA) | Certificate Authority, semantic policy, token binding |
| L5 | Execution | Token-gated action execution, immutable logging |
| L6 | Continuous Monitoring | Behavioral baselines, drift detection, memory audit |
| L7 | Human Governance | Policy management, escalation, break-glass procedures |

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
| **[Framework Positioning](framework-positioning.md)** | How ZoD relates to and extends existing frameworks |
| **[The AI's Perspective](ai-perspective.md)** | Honest assessment of structural vulnerabilities from the agent's point of view |

---

## Framework Mappings

Complete mappings to industry frameworks are available in the [mappings/](mappings/README.md) directory:

**Threat Taxonomies**
- [OWASP Agentic Top 10](mappings/owasp-agentic-mapping.md) — ASI01-ASI10 agent-specific risks
- [OWASP LLM Top 10](mappings/owasp-llm-mapping.md) — LLM01-LLM10 model risks
- [Cisco AI Security](mappings/cisco-mapping.md) — 150+ techniques, scanner integration
- [Microsoft AIRT](mappings/microsoft-airt-mapping.md) — Taxonomy of failure modes
- [NVIDIA/Lakera](mappings/nvidia-lakera-mapping.md) — Autonomy levels, ARP methodology
- [MITRE ATLAS](mappings/mitre-atlas-mapping.md) — AI-specific TTPs

**Methodology & Governance**
- [MAESTRO](mappings/maestro-mapping.md) — Multi-agent security assessment
- [NIST AI RMF](mappings/nist-ai-rmf-mapping.md) — Govern/Map/Measure/Manage
- [Google SAIF](mappings/google-saif-mapping.md) — Secure AI Framework lifecycle
- [AWS Scoping](mappings/aws-scoping-mapping.md) — GenAI + Agentic deployment matrices
- [Zero Trust](mappings/zero-trust-mapping.md) — NIST SP 800-207 extension

**Compliance**
- [Compliance Mapping](mappings/compliance-mapping.md) — EU AI Act, SOC 2, ISO 27001, ISO 42001
- [Industry Mapping](mappings/industry-mapping.md) — HIPAA, PCI DSS, FedRAMP, sector-specific

See the [complete mapping index](mappings/README.md) for all available mappings.

---

## Coming Q2 2026

*Detailed supplementary content in development:*

- Agent Framework Integration Guide
- Enterprise Deployment Guide  
- Security Operations Playbook
- Attack Scenario Walkthroughs

For current implementation guidance, see the [Implementation Guide](implementation-guide.md).

---

## Quick Start

1. **New to ZoD?** Start with the [Executive Brief](executive-brief.md)
2. **Security professional?** Read the [Architecture Specification](ARCHITECTURE.md)
3. **Implementing?** See the [Implementation Guide](implementation-guide.md)
4. **Evaluating security claims?** Review [Security Properties](security-properties.md)
5. **Understanding threats?** Study the [Threat Model](threat-model.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
