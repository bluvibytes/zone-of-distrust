# NIST AI Risk Management Framework Mapping

**Zones of Distrust ↔ NIST AI RMF**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

The NIST AI Risk Management Framework (AI RMF) defines four core functions for managing AI risk: Govern, Map, Measure, and Manage. This document maps ZoD controls to NIST AI RMF requirements.

**Relationship:** NIST AI RMF defines *what* risk management functions are needed. ZoD provides *how* to implement them for agentic AI.

---

## Core Function Mapping

### GOVERN

**NIST Definition:** Cultivate a culture of risk management; establish policies, processes, and accountability structures.

**ZoD Implementation:**

| NIST Subcategory | ZoD Layer | ZoD Control |
|------------------|-----------|-------------|
| Policies and procedures | L7 | Human governance, policy authority |
| Roles and responsibilities | L7 | Escalation workflows, approval requirements |
| Risk tolerance | L7 | Risk classification, autonomy levels |
| Organizational commitment | L7 | GitOps policy management, change control |
| Legal compliance | L7, Integrity | Compliance mapping, audit trails |

**Key ZoD Property:** P11 (External Policy Authority)

**Implementation:**
```
L7 Human Governance
├── Risk classification definitions
├── Escalation thresholds
├── Policy change management (GitOps)
├── Approval workflows
└── Compliance documentation
```

---

### MAP

**NIST Definition:** Understand the AI system context; identify and document risks.

**ZoD Implementation:**

| NIST Subcategory | ZoD Layer | ZoD Control |
|------------------|-----------|-------------|
| System context | L1, L2, L3 | Infrastructure foundation, input surface, cognitive boundaries |
| Risk identification | L2 | Input screening identifies threat patterns |
| Stakeholder needs | L7 | Risk-weighted escalation reflects business priorities |
| Threat landscape | L2, L4 | Screening signatures, semantic policy constraints |
| Assumptions documentation | Threat Model | Trust assumptions, explicit non-goals |

**Key ZoD Artifact:** [Threat Model](../docs/threat-model.md)

**Implementation:**
```
L2 Input Control → Maps input attack surface
L3 Cognitive Isolation → Maps reasoning attack surface  
L4 CA/Validation → Maps action authorization surface
Threat Model → Documents assumptions and boundaries
```

---

### MEASURE

**NIST Definition:** Assess, analyze, and track AI risks; evaluate system performance.

**ZoD Implementation:**

| NIST Subcategory | ZoD Layer | ZoD Control |
|------------------|-----------|-------------|
| Risk assessment | L6 | Behavioral monitoring, drift detection |
| Performance evaluation | L6 | Baseline comparison, anomaly scoring |
| Metrics and thresholds | L6, L7 | Risk scores, escalation triggers |
| Continuous monitoring | L6 | Real-time behavioral analysis |
| Third-party evaluation | L6 | Baseline integrity verification |

**Key ZoD Properties:**
- P5 (Probabilistic Memory Poisoning Detection)
- P6 (Cross-Layer Correlation)
- P8 (Baseline Integrity Verification)

**Implementation:**
```
L6 Continuous Monitoring
├── Behavioral baseline establishment
├── Drift detection (>2σ deviation)
├── Memory audit (scheduled inspection)
├── Risk scoring (per-request evaluation)
└── Baseline integrity verification (watch the watcher)
```

---

### MANAGE

**NIST Definition:** Prioritize and act on AI risks; allocate resources for risk response.

**ZoD Implementation:**

| NIST Subcategory | ZoD Layer | ZoD Control |
|------------------|-----------|-------------|
| Risk prioritization | L4, L7 | Risk classification, tiered validation |
| Risk response | L4, L5 | Token revocation, quarantine, scope restriction |
| Resource allocation | L7 | Escalation queues, approval workflows |
| Incident management | L6, L7, Integrity | Detection, containment, recovery protocols |
| Continuous improvement | L7 | Post-incident review, policy updates |

**Key ZoD Properties:**
- P1 (No Unmediated Execution)
- P9 (Graceful Degradation)

**Implementation:**
```
L4 Request Validation → Risk-prioritized response
├── Low risk → Fast-path approval
├── Medium risk → Standard validation + enhanced logging
├── High risk → Human escalation
└── Critical risk → Multi-person approval

L5 Execution → Validated action with containment
Integrity Channel → Incident detection and forensics
L7 Governance → Recovery and improvement
```

---

## Detailed Subcategory Mapping

### GOVERN Subcategories

| NIST ID | Requirement | ZoD Implementation |
|---------|-------------|-------------------|
| GOVERN 1.1 | Legal/regulatory requirements | L7 compliance documentation, integrity channel audit trail |
| GOVERN 1.2 | Organizational policies | L7 policy authority, GitOps management |
| GOVERN 1.3 | Risk tolerance defined | L7 risk classification, autonomy levels |
| GOVERN 2.1 | Roles established | L7 escalation workflows, approval requirements |
| GOVERN 2.2 | Training provided | L7 governance dashboard, policy documentation |
| GOVERN 3.1 | Oversight mechanisms | L7 human oversight, L6 monitoring |
| GOVERN 4.1 | Culture of risk management | Architecture-wide defense-in-depth |
| GOVERN 5.1 | Documentation practices | Integrity channel, immutable audit logs |
| GOVERN 6.1 | Supply chain risks | L1 model provenance, supply chain verification |

### MAP Subcategories

| NIST ID | Requirement | ZoD Implementation |
|---------|-------------|-------------------|
| MAP 1.1 | System purpose documented | Threat model documentation |
| MAP 1.2 | Stakeholders identified | L7 escalation definitions |
| MAP 2.1 | Context established | L1-L7 layer specifications |
| MAP 2.2 | Assumptions documented | Threat model trust assumptions |
| MAP 3.1 | Risks identified | Threat model attacker positions |
| MAP 3.2 | Benefits/costs analyzed | Implementation guide performance budgets |
| MAP 4.1 | Risks prioritized | L4 risk classification |
| MAP 5.1 | Third-party dependencies | L1 supply chain verification |

### MEASURE Subcategories

| NIST ID | Requirement | ZoD Implementation |
|---------|-------------|-------------------|
| MEASURE 1.1 | Metrics defined | L6 behavioral metrics, security properties (P1-P12) |
| MEASURE 1.2 | Thresholds established | L6 baseline thresholds, L7 escalation triggers |
| MEASURE 2.1 | System assessed | L6 continuous monitoring |
| MEASURE 2.2 | Testing performed | Verification methods for each property |
| MEASURE 3.1 | Monitoring implemented | L6 behavioral monitoring, integrity channel |
| MEASURE 3.2 | Updates tracked | L6 drift detection |
| MEASURE 4.1 | Third-party assessment | P8 baseline integrity verification |
| MEASURE 4.2 | Independent verification | Cross-layer correlation (P6) |

### MANAGE Subcategories

| NIST ID | Requirement | ZoD Implementation |
|---------|-------------|-------------------|
| MANAGE 1.1 | Risk response planned | Recovery and remediation protocols |
| MANAGE 1.2 | Resources allocated | L7 escalation queues, approval workflows |
| MANAGE 2.1 | Responses executed | L4 token revocation, quarantine |
| MANAGE 2.2 | Escalation implemented | L7 risk-weighted escalation |
| MANAGE 3.1 | Incidents documented | Integrity channel forensic record |
| MANAGE 3.2 | Lessons learned | L7 post-incident review |
| MANAGE 4.1 | Risk treatment | Tiered validation (fast/standard/elevated) |
| MANAGE 4.2 | Continuous improvement | Policy updates, signature updates |

---

## Trustworthy AI Characteristics

NIST AI RMF identifies characteristics of trustworthy AI. ZoD mapping:

| Characteristic | ZoD Controls |
|----------------|--------------|
| **Valid and Reliable** | L6 behavioral monitoring, baseline verification |
| **Safe** | L3 cognitive isolation, L4 parameter binding, L5 execution containment |
| **Secure and Resilient** | All layers, P9 graceful degradation |
| **Accountable and Transparent** | Integrity channel, P2 cryptographic attribution, P7 immutable record |
| **Explainable and Interpretable** | L4 token binding captures decision rationale |
| **Privacy-Enhanced** | L4 semantic intent policy (data classification, egress control) |
| **Fair** | L7 policy authority, external governance |

---

## Summary Matrix

| NIST Function | Primary ZoD Layers | Key Properties |
|---------------|-------------------|----------------|
| GOVERN | L7, Integrity | P11 |
| MAP | L1, L2, L3, Threat Model | — |
| MEASURE | L6, Integrity | P5, P6, P8 |
| MANAGE | L4, L5, L6, L7 | P1, P9 |

---

## Verification Checklist

**GOVERN**
- [ ] Risk classification defined (L7)
- [ ] Escalation workflows operational (L7)
- [ ] Policy change management via GitOps (L7)
- [ ] Compliance mapping documented (L7)

**MAP**
- [ ] Threat model documented
- [ ] Trust assumptions explicit
- [ ] Attack surface documented per layer
- [ ] Non-goals documented

**MEASURE**
- [ ] Behavioral baseline established (L6)
- [ ] Drift detection active (L6)
- [ ] Memory audit scheduled (L6)
- [ ] Cross-layer correlation operational (Integrity)
- [ ] Baseline integrity verification active (P8)

**MANAGE**
- [ ] Tiered validation operational (L4)
- [ ] Token revocation tested (L4)
- [ ] Quarantine capability verified (L7)
- [ ] Recovery protocols documented
- [ ] Post-incident review process defined

---

## References

- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [ZoD Architecture Specification](../docs/architecture.md)
- [ZoD Security Properties](../docs/security-properties.md)
- [ZoD Threat Model](../docs/threat-model.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
