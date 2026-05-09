# Compliance Standards Mapping

**Zones of Distrust ↔ Regulatory and Compliance Frameworks**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

This document maps ZoD controls to major regulatory and compliance requirements for organizations deploying AI agents in production.

---

## EU AI Act

The EU AI Act requires risk assessment, transparency, human oversight, and robustness for high-risk AI systems.

### Article Mapping

| EU AI Act Requirement | Article | ZoD Implementation |
|----------------------|---------|-------------------|
| **Risk management system** | Art. 9 | L6 monitoring, L7 risk classification, threat model |
| **Data governance** | Art. 10 | L2 input screening, L6 memory audit |
| **Technical documentation** | Art. 11 | Architecture spec, security properties, threat model |
| **Record-keeping** | Art. 12 | Integrity channel (immutable audit trail) |
| **Transparency** | Art. 13 | L4 token binding (decision capture), P2 attribution |
| **Human oversight** | Art. 14 | L7 human governance, escalation workflows |
| **Accuracy, robustness, security** | Art. 15 | All layers, P9 graceful degradation |
| **Quality management** | Art. 17 | L7 policy management, verification methods |

### High-Risk AI Requirements

| Requirement | ZoD Control | Verification |
|-------------|-------------|--------------|
| Appropriate level of accuracy | L6 behavioral baseline | Baseline drift <2σ |
| Robustness against errors | P9 graceful degradation | Degraded mode testing |
| Resilience against manipulation | L2 screening, L3 isolation | Injection test suites |
| Human oversight capability | L7 escalation, kill switch | Intervention testing |
| Logging of operations | Integrity channel | Append-only verification |

### Prohibited Practices

ZoD helps ensure compliance with prohibited AI practices:

| Prohibition | ZoD Safeguard |
|-------------|---------------|
| Subliminal manipulation | L2 screens for manipulation patterns |
| Exploitation of vulnerabilities | L4 semantic policy blocks harmful actions |
| Social scoring | L4 negative constraints |
| Real-time biometric identification | L4 scope restrictions |

---

## SOC 2 Trust Service Criteria

SOC 2 defines five Trust Service Criteria. ZoD mapping by criteria:

### Security (Common Criteria)

| SOC 2 Control | ZoD Implementation |
|---------------|-------------------|
| **CC1: Control environment** | L7 human governance, policy authority |
| **CC2: Communication and information** | Integrity channel, L6 monitoring alerts |
| **CC3: Risk assessment** | L6 behavioral monitoring, threat model |
| **CC4: Monitoring activities** | L6 continuous monitoring, P6 cross-layer correlation |
| **CC5: Control activities** | L1-L7 layered controls |
| **CC6: Logical access** | L1 agent identity, L4 authorization |
| **CC7: System operations** | L5 execution controls, L6 monitoring |
| **CC8: Change management** | L7 GitOps policy management |
| **CC9: Risk mitigation** | P9 graceful degradation, recovery protocols |

### Availability

| Requirement | ZoD Implementation |
|-------------|-------------------|
| System availability | P9 degraded modes maintain service |
| Recovery procedures | Recovery and remediation protocols |
| Capacity management | L4 tiered validation (fast/standard/elevated) |
| Incident response | L6 detection, L7 containment, recovery |

### Processing Integrity

| Requirement | ZoD Implementation |
|-------------|-------------------|
| Complete processing | P1 no unmediated execution |
| Accurate processing | P4 parameter-bound execution |
| Timely processing | Performance budgets |
| Authorized processing | L4 semantic intent policy, P12 |

### Confidentiality

| Requirement | ZoD Implementation |
|-------------|-------------------|
| Data classification | L4 semantic policy (data classification rules) |
| Access restrictions | L1 scoped permissions, L4 authorization |
| Data transmission | L4 egress control policies |
| Data disposal | L1 credential lifecycle, memory management |

### Privacy

| Requirement | ZoD Implementation |
|-------------|-------------------|
| Notice and consent | L7 governance (policy definitions) |
| Collection limitation | L4 semantic policy (data minimization) |
| Use and retention | L4 scope restrictions, L6 memory audit |
| Access controls | L1 identity, L4 authorization |
| Disclosure controls | L4 egress policies |

### SOC 2 Evidence Collection

| ZoD Component | SOC 2 Evidence |
|---------------|----------------|
| Integrity channel | Immutable audit logs |
| L4 token binding | Authorization decisions |
| L6 monitoring | Behavioral analysis reports |
| L7 escalation | Human approval records |
| Recovery protocols | Incident response documentation |

---

## ISO/IEC 27001:2022

ISO 27001 defines controls for information security management systems (ISMS).

### Annex A Control Mapping

| ISO 27001 Control | ZoD Implementation |
|-------------------|-------------------|
| **A.5 Organizational** | L7 human governance, policy authority |
| **A.6 People** | L7 escalation workflows, training documentation |
| **A.7 Physical** | L1 infrastructure (out of scope for ZoD) |
| **A.8 Technology** | L1-L6 technical controls |

### Technology Controls (A.8) Detail

| Control | Description | ZoD Implementation |
|---------|-------------|-------------------|
| A.8.1 | User endpoint devices | L1 agent identity as distinct principal |
| A.8.2 | Privileged access rights | L1 scoped permissions, L4 authorization |
| A.8.3 | Information access restriction | L4 semantic intent policy |
| A.8.4 | Access to source code | L1 model provenance |
| A.8.5 | Secure authentication | L1 credential broker, L4 token binding |
| A.8.6 | Capacity management | Performance budgets, tiered validation |
| A.8.7 | Protection against malware | L2 input screening |
| A.8.8 | Technical vulnerability management | L1 supply chain verification |
| A.8.9 | Configuration management | L7 GitOps policy management |
| A.8.10 | Information deletion | Memory audit, credential lifecycle |
| A.8.11 | Data masking | L4 data classification policies |
| A.8.12 | Data leakage prevention | L4 egress control |
| A.8.13 | Information backup | Recovery protocols |
| A.8.14 | Redundancy | P9 graceful degradation |
| A.8.15 | Logging | Integrity channel, L5 execution logs |
| A.8.16 | Monitoring activities | L6 continuous monitoring |
| A.8.17 | Clock synchronization | Token TTL, temporal bounds |
| A.8.18 | Use of privileged utility programs | L4 tool-specific constraints |
| A.8.19 | Installation of software | L1 model provenance |
| A.8.20 | Networks security | L3 network isolation |
| A.8.21 | Security of network services | L4 authorization |
| A.8.22 | Segregation of networks | L3 cognitive isolation (network separation) |
| A.8.23 | Web filtering | L2 input screening |
| A.8.24 | Use of cryptography | L4 token signatures, content hashing |
| A.8.25 | Secure development lifecycle | Implementation guide |
| A.8.26 | Application security requirements | Security properties (P1-P12) |
| A.8.27 | Secure system architecture | Architecture specification |
| A.8.28 | Secure coding | N/A (guidance for implementers) |
| A.8.29 | Security testing | Verification methods |
| A.8.30 | Outsourced development | L1 supply chain verification |
| A.8.31 | Separation of environments | L3 cognitive isolation, L5 execution isolation |
| A.8.32 | Change management | L7 GitOps, approval workflows |
| A.8.33 | Test information | N/A |
| A.8.34 | Protection during audit testing | Integrity channel (immutable) |

---

## ISO/IEC 42001:2023

ISO 42001 is the first AI-specific certification standard, addressing AI management systems.

### Control Mapping

| ISO 42001 Area | ZoD Implementation |
|----------------|-------------------|
| **AI policy** | L7 policy authority, governance dashboard |
| **AI risk assessment** | Threat model, L6 monitoring |
| **AI system lifecycle** | Implementation guide (phased approach) |
| **Data management** | L2 input control, L6 memory audit |
| **AI system development** | Architecture spec, security properties |
| **AI system operation** | L4 execution governance, L5 controls |
| **Performance evaluation** | L6 behavioral monitoring, verification methods |
| **Improvement** | Recovery protocols, post-incident review |

### AI-Specific Controls

| Requirement | ZoD Implementation |
|-------------|-------------------|
| AI system impact assessment | Threat model (attacker positions, trust assumptions) |
| AI system documentation | Full documentation suite |
| Data quality for AI | L2 input screening, L6 memory audit |
| AI model management | L1 model provenance |
| Human oversight of AI | L7 human governance |
| AI transparency | L4 token binding, integrity channel |
| AI robustness | P9 graceful degradation, all-layer defense |
| AI security | Security properties P1-P12 |
| AI privacy | L4 semantic policy (data controls) |

---

## FedRAMP 20x

FedRAMP 20x is the federal authorization framework for cloud service providers (CSPs), reframed under the 2022 FedRAMP Authorization Act and OMB Memorandum M-24-15 from a control-narrative model to an outcome-based, continuously-validated capability assessment. Phase 2 of the 20x pilot is active through March 2026; Phase 3 widescale adoption is expected Q3-Q4 2026.

**See the dedicated mapping document:** [FedRAMP 20x Mapping](fedramp-20x-mapping.md)

The dedicated document maps approximately 25 specific Key Security Indicators (KSIs) across 7 theme categories to ZoD architectural layers, with three-tier classification (Direct, Extends, Adjacent) and a section identifying where FedRAMP 20x goes beyond current ZoD scope.

### Summary of Coverage

| KSI Theme | ZoD Implementation |
| --- | --- |
| AFR — Authorization by FedRAMP | L1 model provenance, L4 CA validation, L6 baseline monitoring, ISC immutable state |
| CNA — Cloud Native Architecture | L3 cognitive isolation, L4 token-bound traffic flow, L1 cryptographic attestation |
| IAM — Identity and Access Management | L4 action-first authorization, L5 constrained execution, L6/L7 anomaly response |
| MLA — Monitoring, Logging, and Auditing | ISC SIEM integration, L4 log access governance, L6 configuration evaluation |
| SVC — Service Configuration | L4/L7 policy-as-code, L1/L4 resource integrity, ISC communication validation |
| CMT — Change Management | ISC change attribution, L1 immutable validation, L4-L6 continuous validation |
| SCR — Supply Chain Risk | L1 model provenance, L4 third-party constraints, L6 behavioral monitoring |

### Beyond Current ZoD Scope

FedRAMP 20x requires the full breadth of KSI coverage. ZoD provides architectural mechanisms for technical controls in CNA, IAM, MLA, SVC, CMT, SCR, and parts of AFR. The procedural and organizational themes (CED — Cybersecurity Education, INR — Incident Response, RPL — Recovery Planning, PIY — Policy and Inventory) require additional CSP-side processes that ZoD does not provide. See the dedicated mapping for detail.

---

## Treasury FS AI Risk Management Framework

The Treasury Financial Services AI Risk Management Framework (FS AI RMF), released February 2026, operationalizes NIST AI RMF specifically for financial services. Developed with 100+ financial institutions, the Cyber Risk Institute, and federal/state regulators, the framework introduces examination-oriented control objectives across multiple domains with evidence requirements aligned to financial services supervisory practice.

**See the dedicated mapping document:** [Treasury FS AI RMF Mapping](treasury-fs-ai-rmf-mapping.md)

### Summary of Coverage

| Domain | ZoD Implementation |
| --- | --- |
| Governance | L7 human governance, policy-as-code |
| Data Practices | L4 semantic policy, L6 access monitoring |
| Model Validation | L1 model provenance, L6 behavioral baseline |
| Monitoring | L6 cross-layer correlation, integrity channel |
| Third-Party Risk | L1 supply chain verification, L2 inter-agent screening |
| Consumer Protection | L7 risk-weighted escalation, L4 financial action validation |
| AI-Specific Cybersecurity | All layers (P1-P12) |

### Adoption Stages

FS AI RMF maturity stages map to ZoD implementation phases:

| Stage | ZoD Implementation Path |
| --- | --- |
| Initial | Minimal Deployable Architecture; L4 validation for pilot agents |
| Minimal | Add L6 behavioral monitoring; expand semantic policy |
| Evolving | Full 7-layer implementation; multi-agent chain validation |
| Embedded | HSM-backed CA; enterprise-wide integrity channel; advanced memory audit |

---

## Compliance Matrix Summary

| Requirement Area | EU AI Act | SOC 2 | ISO 27001 | ISO 42001 | FedRAMP 20x | Treasury FS AI RMF |
|-----------------|-----------|-------|-----------|-----------|-------------|---------------------|
| Risk management | Art. 9 | CC3 | A.5.1 | 6.1 | KSI-AFR | Governance |
| Human oversight | Art. 14 | CC1 | A.5.4 | 6.6 | KSI-AFR | Consumer Protection |
| Transparency | Art. 13 | CC2 | A.5.14 | 6.5 | Machine-readable evidence | Governance, Consumer Protection |
| Robustness | Art. 15 | CC7 | A.8.14 | 6.3 | KSI-CNA continuous monitoring | Model Validation |
| Security | Art. 15 | CC6-7 | A.8 | 6.4 | All KSI domains | AI Cybersecurity |
| Logging | Art. 12 | CC4 | A.8.15 | 7.1 | KSI-MLA | Monitoring |
| Data governance | Art. 10 | PI | A.8.10-12 | 7.2 | KSI-SVC, KSI-CMT | Data Practices |
| Incident response | — | CC7.5 | A.5.24-26 | 7.3 | KSI-INR | Monitoring |

---

## ZoD Layer → Compliance Mapping

| ZoD Layer | EU AI Act | SOC 2 | ISO 27001 | ISO 42001 | FedRAMP 20x | Treasury FS AI RMF |
|-----------|-----------|-------|-----------|-----------|-------------|---------------------|
| L1 | Art. 10 | CC6 | A.8.1-5 | 7.2 | KSI-CNA-DFP, KSI-IAM-AAM, KSI-SCR-MIT | Model Validation, Third-Party Risk |
| L2 | Art. 10, 15 | CC7 | A.8.7, 23 | 7.2 | KSI-CNA scope | AI Cybersecurity, Third-Party Risk |
| L3 | Art. 15 | CC5 | A.8.22, 31 | 6.3 | KSI-CNA-MAT | AI Cybersecurity |
| L4 | Art. 13, 14 | PI, CC6 | A.8.3, 24 | 6.5, 6.6 | KSI-IAM-JIT, KSI-IAM-ELP, KSI-SVC | Data Practices, Consumer Protection |
| L5 | Art. 12 | PI | A.8.15 | 7.1 | KSI-IAM-ELP, KSI-CMT-VTD | AI Cybersecurity |
| L6 | Art. 9 | CC4 | A.8.16 | 7.1 | KSI-MLA-EVC, KSI-CNA-RVP, KSI-IAM-SUS | Monitoring |
| L7 | Art. 14 | CC1 | A.5 | 6.6 | KSI-SVC-ACM, KSI-AFR-SCN | Governance, Consumer Protection |
| Integrity | Art. 12, 13 | CC4 | A.8.15 | 7.1 | KSI-MLA-OSM, KSI-MLA-RVL, KSI-SVC-VCM | Monitoring, Governance |

---

## Audit Preparation

### Documentation Checklist

| Document | Purpose | Compliance Support |
|----------|---------|-------------------|
| Architecture spec | Technical design | All frameworks |
| Security properties | Measurable controls | SOC 2, ISO 27001 |
| Threat model | Risk assessment | EU AI Act, ISO 42001 |
| Implementation guide | Operational procedures | SOC 2, ISO 27001 |
| Recovery protocols | Incident response | SOC 2, ISO 27001 |

### Evidence Collection

| ZoD Component | Evidence Type | Audit Use |
|---------------|---------------|-----------|
| Integrity channel | Immutable logs | All audits |
| L4 tokens | Authorization records | Access control audits |
| L6 baselines | Monitoring records | Operational audits |
| L7 approvals | Human oversight evidence | EU AI Act, governance audits |
| Test results | Verification evidence | All audits |

---

## Verification Checklist

**EU AI Act**
- [ ] Risk management system documented (L6, L7)
- [ ] Human oversight implemented (L7)
- [ ] Transparency requirements met (L4, Integrity)
- [ ] Logging operational (Integrity channel)

**SOC 2**
- [ ] Security controls implemented (L1-L6)
- [ ] Availability controls tested (P9)
- [ ] Processing integrity verified (P1, P4)
- [ ] Confidentiality controls operational (L4)

**ISO 27001**
- [ ] Technology controls mapped (A.8)
- [ ] Access controls implemented (L1, L4)
- [ ] Cryptographic controls active (L4)
- [ ] Logging and monitoring operational (L6, Integrity)

**ISO 42001**
- [ ] AI-specific controls addressed
- [ ] Human oversight documented (L7)
- [ ] AI robustness demonstrated (P9)
- [ ] AI transparency achieved (L4, Integrity)

**FedRAMP 20x**
- [ ] Continuous monitoring operational (L6)
- [ ] Machine-readable evidence schemas defined (Integrity channel)
- [ ] Policy-as-code via GitOps (L7)
- [ ] Production-derived evidence available (L5, L6)
- [ ] Configuration management automation (L7)
- [ ] Identity assurance controls (L1, L4)
- [ ] See [fedramp-20x-mapping.md](fedramp-20x-mapping.md) for full KSI-level verification

**Treasury FS AI RMF**
- [ ] Governance domain controls operational (L7)
- [ ] Data Practices controls operational (L4, L6)
- [ ] Model Validation evidence generated (L1, L6)
- [ ] Monitoring evidence active (L6, Integrity)
- [ ] Third-Party Risk controls operational (L1, L2)
- [ ] Consumer Protection escalation operational (L7)
- [ ] See [treasury-fs-ai-rmf-mapping.md](treasury-fs-ai-rmf-mapping.md) for full domain verification


---

## References

- [EU AI Act](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)
- [SOC 2 Trust Services Criteria (AICPA 2017, revised 2022)](https://www.aicpa-cima.com/topic/audit-assurance/audit-and-assurance-greater-than-soc-2)
- [ISO/IEC 27001:2022](https://www.iso.org/standard/27001)
- [ISO/IEC 42001:2023](https://www.iso.org/standard/42001)
- [FedRAMP 20x Overview](https://www.fedramp.gov/20x/)
- [FedRAMP Machine-Readable Documentation (FRMR)](https://github.com/FedRAMP/docs)
- [FedRAMP Authorization Act (44 USC § 3609)](https://fedramp.gov/docs/authority/law/)
- [OMB Memorandum M-24-15](https://www.whitehouse.gov/wp-content/uploads/2024/07/M-24-15-Modernizing-the-Federal-Risk-and-Authorization-Management-Program-FedRAMP.pdf)
- [Treasury FS AI Risk Management Framework](https://cyberriskinstitute.org/artificial-intelligence-risk-management/)
- [NIST SP 800-53 Rev. 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [FedRAMP 20x ZoD Mapping](fedramp-20x-mapping.md)
- [Treasury FS AI RMF ZoD Mapping](treasury-fs-ai-rmf-mapping.md)
- [ZoD Architecture Specification](../ARCHITECTURE.md)
- [ZoD Security Properties](../security-properties.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
