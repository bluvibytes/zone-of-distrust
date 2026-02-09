# ENISA AI Threat Landscape Mapping

**Zones of Distrust ↔ ENISA AI Security (2024-2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

The European Union Agency for Cybersecurity (ENISA) has published guidance on AI security threats and countermeasures. This mapping aligns ZoD controls with ENISA's threat taxonomy and recommendations.

**Relationship:** ENISA provides EU-specific *threat intelligence* and *guidance*. ZoD provides *architectural controls* that implement ENISA recommendations.

---

## ENISA AI Threat Taxonomy

### Category 1: Adversarial Attacks

**ENISA Definition:** Attacks that manipulate AI system inputs or behavior through crafted adversarial examples.

| ENISA Threat | Description | ZoD Control | Layer |
|--------------|-------------|-------------|-------|
| Evasion attacks | Inputs crafted to cause misclassification | Input screening | L2 |
| Poisoning attacks | Corrupting training/operational data | Memory audit, provenance | L6, L1 |
| Model extraction | Stealing model through queries | Rate limiting, monitoring | L6 |
| Model inversion | Inferring training data | Semantic policy, monitoring | L4, L6 |

---

### Category 2: Data Threats

**ENISA Definition:** Threats targeting data used by AI systems.

| ENISA Threat | Description | ZoD Control | Layer |
|--------------|-------------|-------------|-------|
| Data poisoning | Manipulating training/input data | Input screening, memory audit | L2, L6 |
| Data exfiltration | Unauthorized data extraction | Egress control, semantic policy | L4 |
| Data integrity | Unauthorized modification | Content hashing, logging | L4, Integrity |
| Privacy violations | Exposing personal data | Data classification policy | L4 |

---

### Category 3: Model Threats

**ENISA Definition:** Threats targeting AI model integrity and availability.

| ENISA Threat | Description | ZoD Control | Layer |
|--------------|-------------|-------------|-------|
| Model theft | Unauthorized model access | Infrastructure security | L1 |
| Model tampering | Unauthorized modifications | Model provenance | L1 |
| Backdoor insertion | Hidden malicious functionality | Provenance, behavioral monitoring | L1, L6 |
| Model degradation | Performance degradation | Behavioral baseline | L6 |

---

### Category 4: Infrastructure Threats

**ENISA Definition:** Threats to AI system infrastructure.

| ENISA Threat | Description | ZoD Control | Layer |
|--------------|-------------|-------------|-------|
| Compute compromise | Unauthorized infrastructure access | Infrastructure integrity | L1 |
| Supply chain attacks | Malicious components | Supply chain verification | L1 |
| DoS attacks | Resource exhaustion | Input limits, degradation modes | L2 |
| Side-channel attacks | Information leakage | Infrastructure hardening | L1 |

---

### Category 5: Operational Threats

**ENISA Definition:** Threats arising from AI system operation.

| ENISA Threat | Description | ZoD Control | Layer |
|--------------|-------------|-------------|-------|
| Prompt injection | Manipulating system via inputs | Input screening | L2 |
| Output manipulation | Exploiting system outputs | Semantic policy, execution control | L4, L5 |
| Session hijacking | Unauthorized session control | Token validation, replay prevention | L4 |
| Privilege escalation | Gaining unauthorized access | Scoped authorization | L4 |

---

## ENISA AI Security Recommendations → ZoD

### Recommendation 1: Secure Development Lifecycle

| ENISA Guidance | ZoD Implementation |
|----------------|-------------------|
| Security requirements | Security properties (P1-P12) |
| Threat modeling | Threat model documentation |
| Secure design | Architecture specification |
| Security testing | Verification methods |

---

### Recommendation 2: Data Protection

| ENISA Guidance | ZoD Implementation |
|----------------|-------------------|
| Data classification | L4 semantic policy with classification |
| Access control | L4 authorization, scoped tokens |
| Data integrity | L4 content hashing |
| Privacy protection | L4 data minimization policies |

---

### Recommendation 3: Model Security

| ENISA Guidance | ZoD Implementation |
|----------------|-------------------|
| Model provenance | L1 cryptographic verification |
| Model integrity | L1 hash verification |
| Model monitoring | L6 behavioral baseline |
| Model access control | L1 infrastructure controls |

---

### Recommendation 4: Runtime Protection

| ENISA Guidance | ZoD Implementation |
|----------------|-------------------|
| Input validation | L2 input screening |
| Output validation | L4 semantic policy, L5 execution control |
| Anomaly detection | L6 behavioral monitoring |
| Incident response | Recovery protocols |

---

### Recommendation 5: Governance and Oversight

| ENISA Guidance | ZoD Implementation |
|----------------|-------------------|
| Risk management | L7 risk classification |
| Human oversight | L7 escalation workflows |
| Audit trails | Integrity channel |
| Compliance | Compliance documentation |

---

## ENISA Multilayer Framework Alignment

ENISA recommends a multilayer security approach. ZoD alignment:

| ENISA Layer | Description | ZoD Layer(s) |
|-------------|-------------|--------------|
| Data layer | Data protection | L2, L4, L6 |
| Model layer | Model security | L1, L6 |
| Application layer | Runtime protection | L3, L4, L5 |
| Infrastructure layer | System security | L1 |
| Governance layer | Oversight and compliance | L7, Integrity |

---

## EU AI Act Alignment via ENISA

ENISA guidance helps implement EU AI Act requirements:

| EU AI Act Article | ENISA Guidance | ZoD Implementation |
|-------------------|----------------|-------------------|
| Art. 9 (Risk management) | Risk assessment methodology | Threat model, L7 classification |
| Art. 10 (Data governance) | Data protection guidance | L2, L4 data policies |
| Art. 12 (Record-keeping) | Logging requirements | Integrity channel |
| Art. 14 (Human oversight) | Oversight mechanisms | L7 governance |
| Art. 15 (Accuracy/robustness) | Security testing | Verification methods |

---

## ENISA Threat Scenarios → ZoD Defense

### Scenario 1: Healthcare AI Manipulation

**ENISA Scenario:** Attacker manipulates medical AI diagnosis.

**ZoD Defense:**
- L2: Screen medical data inputs
- L4: Require human approval for diagnoses
- L6: Monitor for diagnostic pattern changes
- L7: Escalate high-confidence unusual diagnoses

---

### Scenario 2: Financial AI Exploitation

**ENISA Scenario:** Attacker exploits trading AI for market manipulation.

**ZoD Defense:**
- L4: Transaction limits and semantic policy
- L6: Behavioral baseline for trading patterns
- L7: Human approval for large/unusual trades
- Integrity: Immutable transaction records

---

### Scenario 3: Autonomous Vehicle Adversarial Attack

**ENISA Scenario:** Adversarial inputs cause misclassification.

**ZoD Defense:**
- L2: Input validation and anomaly detection
- L4: Safety constraints on actions
- L6: Real-time behavioral monitoring
- L7: Safe fallback modes

---

## ENISA Sectoral Guidance

ENISA provides sector-specific AI guidance:

| Sector | ENISA Focus | ZoD Support |
|--------|-------------|-------------|
| Healthcare | Patient safety, data protection | [Industry mapping](industry-mapping.md) |
| Finance | Fraud prevention, market integrity | [Industry mapping](industry-mapping.md) |
| Transport | Safety-critical systems | L7 safety controls |
| Energy | Critical infrastructure protection | [Industry mapping](industry-mapping.md) |
| Public sector | Citizen rights, transparency | L7 governance, audit trails |

---

## Verification Checklist

**Adversarial Attack Defense**
- [ ] L2 input screening for adversarial patterns
- [ ] L6 memory audit for poisoning detection
- [ ] L6 behavioral monitoring for evasion detection

**Data Protection**
- [ ] L4 data classification policies
- [ ] L4 egress controls
- [ ] Integrity channel for data access logging

**Model Security**
- [ ] L1 model provenance verification
- [ ] L6 behavioral baseline for model drift
- [ ] L1 supply chain verification

**Runtime Protection**
- [ ] L2 input validation active
- [ ] L4 semantic policy enforced
- [ ] L6 anomaly detection operational

**Governance**
- [ ] L7 risk classification defined
- [ ] L7 human oversight mechanisms
- [ ] Integrity channel for audit trails

---

## References

- [ENISA AI Security](https://www.enisa.europa.eu/topics/artificial-intelligence)
- [ENISA Threat Landscape](https://www.enisa.europa.eu/topics/threat-risk-management/threats-and-trends)
- [EU AI Act](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)
- [ZoD Compliance Mapping](compliance-mapping.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
