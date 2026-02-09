# Framework Mappings

**Zones of Distrust: Cross-Reference Mappings**

*Version 0.9 RFC — February 2026*

---

> **Important:** These mappings demonstrate *conceptual alignment* and *control coverage*, not certification or compliance. ZoD is **complementary to**, not a replacement for, established frameworks and standards. Mappings do not imply endorsement by or affiliation with referenced organizations. See [DISCLAIMER.md](../DISCLAIMER.md) and [TRADEMARKS.md](../TRADEMARKS.md).

---

## Overview

This directory contains detailed mappings between the Zones of Distrust architecture and major AI security frameworks, standards, and regulations.

**Purpose:** Enable organizations to understand how ZoD architectural controls align with and support implementation of framework requirements. These mappings provide audit-relevant evidence artifacts, not automatic compliance.

**Quick Start:** See [quick-reference.md](quick-reference.md) for a one-page cheatsheet.

---

## Complete Mapping Index

### Threat Taxonomies (What Can Go Wrong)

| Mapping | Framework | Focus |
|---------|-----------|-------|
| [OWASP Agentic](owasp-agentic-mapping.md) | OWASP Top 10 for Agentic Applications | ASI01-ASI10 agent-specific risks |
| [OWASP LLM](owasp-llm-mapping.md) | OWASP Top 10 for LLM Applications | LLM01-LLM10 model risks |
| [Cisco](cisco-mapping.md) | Cisco Integrated AI Security Framework | 150+ techniques, scanner integration |
| [Microsoft AIRT](microsoft-airt-mapping.md) | Microsoft Taxonomy of Failure Modes | Novel agentic failure patterns |
| [NVIDIA/Lakera](nvidia-lakera-mapping.md) | NVIDIA/Lakera AI Safety Framework | Autonomy levels, ARP methodology |
| [MITRE ATLAS](mitre-atlas-mapping.md) | MITRE ATLAS | AI-specific TTPs |

### Methodology & Governance (How to Assess/Manage)

| Mapping | Framework | Focus |
|---------|-----------|-------|
| [MAESTRO](maestro-mapping.md) | CSA MAESTRO | Multi-agent security assessment |
| [CSA ATF](csa-atf-mapping.md) | CSA AI Technology Framework | Cloud AI control domains |
| [NIST AI RMF](nist-ai-rmf-mapping.md) | NIST AI Risk Management Framework | Govern/Map/Measure/Manage |
| [Google SAIF](google-saif-mapping.md) | Google Secure AI Framework | Lifecycle phases |
| [CoSAI](cosai-mapping.md) | Coalition for Secure AI | Industry coalition risk map |
| [AWS Scoping](aws-scoping-mapping.md) | AWS GenAI + Agentic Scoping Matrices | Deployment risk & agency categorization |
| [Zero Trust](zero-trust-mapping.md) | NIST SP 800-207 | ZTA extension for agents |
| [ENISA](enisa-mapping.md) | ENISA AI Threat Landscape | EU threat intelligence |

### Regulatory & Compliance

| Mapping | Framework | Focus |
|---------|-----------|-------|
| [Compliance](compliance-mapping.md) | EU AI Act, SOC 2, ISO 27001, ISO 42001 | Certification requirements |
| [Industry](industry-mapping.md) | HIPAA, PCI DSS, FedRAMP, NERC CIP | Sector-specific regulations |

### Practical Application

| Mapping | Purpose | Focus |
|---------|---------|-------|
| [Incident Mapping](incident-mapping.md) | Case studies | Real attacks → ZoD controls |
| [Quick Reference](quick-reference.md) | Cheatsheet | One-page crosswalk |

---

## Framework Categories

### Threat Taxonomies

Frameworks that identify *what* can go wrong:

| Framework | Mapping | Key Contribution |
|-----------|---------|------------------|
| OWASP Agentic | [owasp-agentic-mapping.md](owasp-agentic-mapping.md) | 10 agent-specific risk categories |
| Cisco | [cisco-mapping.md](cisco-mapping.md) | 150+ techniques, scanner tools |
| Microsoft AIRT | [microsoft-airt-mapping.md](microsoft-airt-mapping.md) | Novel failure mode classification |
| NVIDIA/Lakera | [nvidia-lakera-mapping.md](nvidia-lakera-mapping.md) | Autonomy levels, ARP methodology |
| MITRE ATLAS | [mitre-atlas-mapping.md](mitre-atlas-mapping.md) | AI-specific TTPs |

### Methodology & Governance

Frameworks that define *how* to assess and govern:

| Framework | Mapping | Key Contribution |
|-----------|---------|------------------|
| MAESTRO | [maestro-mapping.md](maestro-mapping.md) | Multi-agent assessment methodology |
| CSA ATF | [csa-atf-mapping.md](csa-atf-mapping.md) | Cloud AI security patterns |
| NIST AI RMF | [nist-ai-rmf-mapping.md](nist-ai-rmf-mapping.md) | Govern/Map/Measure/Manage |
| Google SAIF | [google-saif-mapping.md](google-saif-mapping.md) | Lifecycle phases |
| AWS Scoping | [aws-scoping-mapping.md](aws-scoping-mapping.md) | Deployment & agency categorization |

### Regulatory & Compliance

Standards for certification and regulatory compliance:

| Standard | Mapping | Key Contribution |
|----------|---------|------------------|
| EU AI Act | [compliance-mapping.md](compliance-mapping.md) | High-risk AI requirements |
| SOC 2 | [compliance-mapping.md](compliance-mapping.md) | Trust Service Criteria |
| ISO 27001 | [compliance-mapping.md](compliance-mapping.md) | Information security controls |
| ISO 42001 | [compliance-mapping.md](compliance-mapping.md) | AI-specific certification |

---

## ZoD Layer → Framework Coverage

Which frameworks address which layers:

| ZoD Layer | OWASP | MAESTRO | ATF | NIST | SAIF | Cisco | MS | NVIDIA | MITRE | AWS | Compliance |
|-----------|-------|---------|-----|------|------|-------|-----|--------|-------|-----|------------|
| L1 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| L2 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| L3 | ✓ | ~ | ✓ | ~ | ~ | ✓ | ✓ | ✓ | ~ | ✓ | ✓ |
| L4 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ~ | ✓ | ✓ |
| L5 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ~ | ✓ | ✓ |
| L6 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| L7 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ~ | ✓ | ✓ |
| Integrity | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ~ | ✓ | ✓ | ✓ |

*✓ = explicitly addressed | ~ = partially addressed*

---

## Security Properties → Framework Coverage

Which frameworks validate which properties:

| Property | Description | Validated By |
|----------|-------------|--------------|
| P1 | No Unmediated Execution | OWASP, MAESTRO, ATF, Cisco |
| P2 | Cryptographic Attribution | NIST, ISO 27001, SOC 2 |
| P3 | Replay Prevention | Cisco, Microsoft AIRT |
| P4 | Parameter-Bound Execution | OWASP, Cisco |
| P5 | Memory Poisoning Detection | OWASP, Microsoft AIRT, MAESTRO |
| P6 | Cross-Layer Correlation | NIST, ATF |
| P7 | Immutable Forensic Record | EU AI Act, SOC 2, ISO 27001 |
| P8 | Baseline Integrity Verification | Not explicitly formalized in surveyed frameworks |
| P9 | Graceful Degradation | AWS, NIST |
| P10 | Compromise Non-Propagation | OWASP, MAESTRO, Cisco |
| P11 | External Policy Authority | EU AI Act, NIST, MAESTRO |
| P12 | Deterministic Semantic Enforcement | Not explicitly formalized in surveyed frameworks |

---

## How to Use These Mappings

### For Compliance Audits

1. Identify applicable standards (EU AI Act, SOC 2, ISO 27001, etc.)
2. Open [compliance-mapping.md](compliance-mapping.md)
3. Use verification checklists to demonstrate control implementation
4. Reference specific ZoD layers/properties for each requirement

### For Framework Alignment

1. Identify framework your organization follows (MAESTRO, NIST, etc.)
2. Open corresponding mapping document
3. Map your ZoD implementation to framework requirements
4. Use verification checklists to confirm coverage

### For Gap Analysis

1. Review all mappings against current implementation
2. Identify unchecked items in verification checklists
3. Prioritize based on risk and compliance requirements
4. Use [implementation guide](../docs/implementation-guide.md) for remediation

### For Security Assessments

1. Use threat taxonomy mappings (Cisco, OWASP, MITRE) to identify threats
2. Verify corresponding ZoD controls are implemented
3. Use scanner integration guidance (Cisco) for testing
4. Document findings against integrity channel records

---

## Mapping Statistics

| Metric | Value |
|--------|-------|
| Total mapping documents | 18 |
| Frameworks/standards covered | 20+ |
| Threat taxonomy frameworks | 6 |
| Methodology frameworks | 8 |
| Compliance standards | 8+ |
| Total verification items | 150+ |

---

## Related Documents

- [ZoD Architecture Specification](../ARCHITECTURE.md)
- [Security Properties](../security-properties.md) — P1-P12 specifications
- [Framework Positioning](../framework-positioning.md) — Strategic overview
- [Implementation Guide](../implementation-guide.md) — How to build it

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
