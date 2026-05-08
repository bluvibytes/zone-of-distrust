# Treasury FS AI RMF Mapping

**Zones of Distrust ↔ Treasury Financial Services AI Risk Management Framework**

*Version 0.9 RFC — March 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---

## Overview

The Treasury Financial Services AI Risk Management Framework (FS AI RMF), released February 2026, operationalizes NIST AI RMF specifically for financial services. Developed with 100+ financial institutions, the Cyber Risk Institute, and federal/state regulators, the framework introduces 230 control objectives across six domains.

**Relationship:** FS AI RMF defines *what* controls financial institutions need. ZoD provides *how* to implement them with cryptographic enforcement and examination-ready evidence.

**Key Difference from NIST AI RMF:** FS AI RMF is examination-oriented—controls map to specific evidence artifacts examiners will request.

---

## Framework Components

| Component | Purpose |
|-----------|---------|
| AI Adoption Stage Questionnaire | Maturity assessment (Initial, Minimal, Evolving, Embedded) |
| Risk and Control Matrix (RCM) | 230 control objectives mapped to adoption stages |
| User Guidebook | Implementation manual |
| Control Objective Reference Guide | 400+ examples of effective evidence |

---

## Control Domain Mapping

### GOVERNANCE

**FS AI RMF Definition:** Policies, processes, accountability structures, and organizational commitment to AI risk management.

**ZoD Implementation:**

| FS AI RMF Requirement | ZoD Layer | ZoD Control |
|-----------------------|-----------|-------------|
| AI risk management policies | L7 | Human governance, policy-as-code |
| Accountability structures | L7 | Escalation workflows, approval requirements |
| Roles and responsibilities | L7 | Multi-party approval, role-based escalation |
| Organizational commitment | L7 | GitOps policy management, change control |
| Stakeholder engagement | L7, Integrity | Cross-layer reporting, audit access |
| Third-party policies | L1, L4 | Model provenance, API constraints |

**Key ZoD Properties:** P11 (External Policy Authority), P12 (Policy Provenance)

**Evidence Artifacts:**
```
Integrity Channel
├── Policy version history
├── Approval logs (who, when, what)
├── Escalation records
├── Change management audit trail
└── Role assignment logs
```

---

### DATA PRACTICES

**FS AI RMF Definition:** Data classification, handling, lifecycle governance, and privacy management.

**ZoD Implementation:**

| FS AI RMF Requirement | ZoD Layer | ZoD Control |
|-----------------------|-----------|-------------|
| Data classification | L4 | Semantic intent policy, classification constraints |
| Data handling requirements | L4 | Egress controls, classification ceiling |
| Data lifecycle governance | L6, L4 | Access monitoring, token-bound classification |
| Privacy and consent | L4, L7 | Egress controls, human approval for sensitive data |
| Data quality | L6 | Behavioral monitoring, drift detection |

**Key ZoD Properties:** P4 (Parameter-Bound Execution)

**Evidence Artifacts:**
```
L4 Certificate Authority
├── Classification ceiling configurations
├── Egress denial logs
├── Token classification metadata
└── Data access patterns

L6 Monitoring
├── Access pattern baselines
├── Classification violation alerts
└── Data flow logs
```

---

### MODEL DEVELOPMENT AND VALIDATION

**FS AI RMF Definition:** Model provenance, integrity, validation, testing, and performance monitoring.

**ZoD Implementation:**

| FS AI RMF Requirement | ZoD Layer | ZoD Control |
|-----------------------|-----------|-------------|
| Model provenance | L1 | Cryptographic signatures, supply chain attestation |
| Model integrity | L1 | Supply chain verification at model load |
| Model validation | L6 | Behavioral baseline comparison |
| Model testing | L6 | Baseline deviation detection |
| Performance monitoring | L6 | Behavioral drift detection, anomaly scoring |

**Key ZoD Properties:** P8 (Baseline Integrity Verification)

**Evidence Artifacts:**
```
L1 OS Foundation
├── Signed model manifests
├── Supply chain verification logs
├── Provenance attestations
└── Model version history

L6 Monitoring
├── Baseline deviation reports
├── Validation test results
├── Drift metrics over time
└── Anomaly detection logs
```

---

### MONITORING

**FS AI RMF Definition:** Continuous monitoring, anomaly detection, alerting, and incident detection/response.

**ZoD Implementation:**

| FS AI RMF Requirement | ZoD Layer | ZoD Control |
|-----------------------|-----------|-------------|
| Continuous monitoring | L6 | Cross-layer correlation, behavioral baselining |
| Anomaly detection | L6 | Drift detection (>2σ deviation), alert scoring |
| Alerting | L6, L7 | Alert quality scoring, escalation triggers |
| Incident detection | L6, Integrity | Compromise indicators, cross-layer correlation |
| Incident response | L7, Recovery | Containment protocols, remediation workflows |

**Key ZoD Properties:** P5 (Memory Poisoning Detection), P6 (Cross-Layer Correlation), P7 (Immutable Forensic Record)

**Evidence Artifacts:**
```
L6 Continuous Monitoring
├── Behavioral profiles
├── Drift detection alerts
├── Memory audit logs
├── Cross-layer correlation dashboards
└── Alert fatigue metrics

Integrity Channel
├── Incident timelines
├── Compromise correlation reports
├── Forensic event sequences
└── Recovery action logs
```

---

### THIRD-PARTY RISK

**FS AI RMF Definition:** Third-party AI risk assessment, contract requirements, and ongoing monitoring.

**ZoD Implementation:**

| FS AI RMF Requirement | ZoD Layer | ZoD Control |
|-----------------------|-----------|-------------|
| Third-party risk assessment | L1 | Supply chain verification, vendor attestation |
| Contract requirements | L4 | Tool-specific constraints, API limitations |
| Ongoing third-party monitoring | L6 | Behavioral baselines for integrations |
| Inter-agent communication | L2 | Screens inter-agent messages, external system inputs |
| Third-party model risks | L1, L6 | Model provenance, behavioral comparison |

**Key ZoD Properties:** P10 (Compromise Non-Propagation)

**Evidence Artifacts:**
```
L1 OS Foundation
├── Vendor attestation records
├── Supply chain audit logs
├── Third-party model signatures
└── Integration verification logs

L2 Input Control
├── Inter-agent screening logs
├── External input filtering records
└── Quarantine events

L4 Certificate Authority
├── API constraint configurations
├── Third-party execution logs
└── Tool-specific policy records
```

---

### CONSUMER PROTECTION

**FS AI RMF Definition:** Transparency, explainability, human oversight, and consumer harm prevention.

**ZoD Implementation:**

| FS AI RMF Requirement | ZoD Layer | ZoD Control |
|-----------------------|-----------|-------------|
| Transparency | L4, Integrity | Token binding captures decision rationale |
| Explainability | L7, Integrity | Decision audit logs with policy justification |
| Human oversight | L7 | Risk-weighted escalation, multi-party approval |
| High-risk decision review | L7 | Human escalation gates, scope expansion approval |
| Consumer harm prevention | L4, L7 | Financial action validation, transfer escalation |

**Key ZoD Properties:** P11 (Risk-Weighted Human Escalation)

**Evidence Artifacts:**
```
L7 Human Governance
├── Escalation logs
├── Human approval records
├── Threshold configurations
├── Scope expansion approvals
└── Multi-party approval chains

Integrity Channel
├── Decision audit trails
├── Policy justification records
├── Consumer-impacting action logs
└── Harm prevention evidence
```

---

### AI-SPECIFIC CYBERSECURITY

**FS AI RMF Definition:** Agent-specific security controls including prompt injection, goal hijacking, memory poisoning, and unauthorized execution.

**ZoD Implementation:**

| FS AI RMF Requirement | ZoD Layer | ZoD Control |
|-----------------------|-----------|-------------|
| Prompt injection protection | L2, L3, L4 | Input screening, cognitive isolation, consequence containment |
| Agent goal hijacking prevention | L4, L7 | Semantic intent policy, risk-weighted escalation |
| Memory poisoning detection | L6 | Memory audit, context verification |
| Unauthorized execution prevention | L4, L5 | Token-bound execution, parameter verification |
| Credential protection | L1, L4 | Credential brokering, single-use tokens |
| Multi-agent security | L2, L4 | Inter-agent screening, chain validation |

**Key ZoD Properties:** P1 (No Unmediated Execution), P2 (Cryptographic Attribution), P3 (Replay Prevention), P4 (Parameter-Bound Execution)

**Evidence Artifacts:**
```
L2 Input Control
├── Injection detection logs
├── Quarantine events
├── Screening signature matches
└── Degraded mode activations

L4 Certificate Authority
├── Token issuance logs
├── Policy denial records
├── Parameter binding evidence
└── Chain validation logs

L5 Execution
├── Execution validation records
├── Parameter verification logs
└── Unauthorized execution denials

L6 Monitoring
├── Memory audit logs
├── Poisoning detection alerts
└── Context verification records
```

---

## Adoption Stage Alignment

FS AI RMF categorizes institutions by AI maturity. ZoD implementation scales accordingly:

| Adoption Stage | Institution Profile | ZoD Implementation Path |
|----------------|---------------------|-------------------------|
| **Initial** | Limited AI use, exploration | Minimal Deployable Architecture on Kubernetes; L4 validation for pilot agents |
| **Minimal** | Specific use cases, early governance | Add L6 behavioral monitoring; expand semantic policy |
| **Evolving** | Multiple use cases, maturing governance | Full 7-layer implementation; multi-agent chain validation |
| **Embedded** | AI integral to operations, mature governance | HSM-backed CA; enterprise-wide integrity channel; advanced memory audit |

---

## Summary Matrix

| FS AI RMF Domain | Primary ZoD Layers | Key Properties | Evidence Location |
|------------------|-------------------|----------------|-------------------|
| Governance | L7, Integrity | P11, P12 | Integrity Channel |
| Data Practices | L4, L6 | P4 | L4 logs, L6 monitoring |
| Model Validation | L1, L6 | P8 | L1 attestations, L6 baselines |
| Monitoring | L6, Integrity | P5, P6, P7 | L6 dashboards, Integrity Channel |
| Third-Party Risk | L1, L2, L4 | P10 | L1 attestations, L2 logs, L4 constraints |
| Consumer Protection | L4, L7, Integrity | P11 | L7 escalation, Integrity Channel |
| AI Cybersecurity | L1-L7 | P1-P4 | All layers |

---

## Verification Checklist

**GOVERNANCE**
- [ ] Policy-as-code via GitOps operational (L7)
- [ ] Escalation workflows defined and tested (L7)
- [ ] Multi-party approval for scope expansion (L7)
- [ ] Policy change audit trail active (Integrity)

**DATA PRACTICES**
- [ ] Classification ceiling configured (L4)
- [ ] Egress controls enforced (L4)
- [ ] Data access monitoring active (L6)
- [ ] Classification violation alerts configured (L6)

**MODEL VALIDATION**
- [ ] Model provenance attestation enabled (L1)
- [ ] Supply chain verification at load (L1)
- [ ] Behavioral baseline established (L6)
- [ ] Drift detection thresholds set (L6)

**MONITORING**
- [ ] Cross-layer correlation operational (L6, Integrity)
- [ ] Alert quality scoring active (L6)
- [ ] Memory audit scheduled (L6)
- [ ] Incident detection configured (Integrity)

**THIRD-PARTY RISK**
- [ ] Vendor attestation records maintained (L1)
- [ ] Third-party API constraints configured (L4)
- [ ] Inter-agent screening active (L2)
- [ ] Third-party behavioral baselines established (L6)

**CONSUMER PROTECTION**
- [ ] Risk-weighted escalation configured (L7)
- [ ] High-value action thresholds defined (L4, L7)
- [ ] Human approval gates operational (L7)
- [ ] Decision audit trail active (Integrity)

**AI CYBERSECURITY**
- [ ] Input screening operational (L2)
- [ ] Cognitive isolation enforced (L3)
- [ ] Token-bound execution active (L4, L5)
- [ ] Memory audit detecting poisoning (L6)
- [ ] Multi-agent chain validation operational (L4)

---

## References

- [Treasury FS AI RMF](https://cyberriskinstitute.org/artificial-intelligence-risk-management/)
- [FSSCC AIEOG Deliverables](https://fsscc.org/aieog-ai-deliverables/)
- [Treasury Press Release](https://home.treasury.gov/news/press-releases/sb0401)
- [ZoD Architecture Specification](../ARCHITECTURE.md)
- [ZoD Security Properties](../security-properties.md)
- [ZoD Threat Model](../threat-model.md)
- [NIST AI RMF Mapping](./nist-ai-rmf-mapping.md)

---

*Zones of Distrust v0.9 RFC — March 2026 — BluVi*
