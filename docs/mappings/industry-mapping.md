# Industry-Specific Compliance Mapping

**Zones of Distrust ↔ Sector Regulations**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

This document maps ZoD controls to industry-specific regulations for sectors deploying AI agents. Each section addresses the unique compliance requirements of that industry.

---

## Healthcare (HIPAA, HITRUST, FDA)

### HIPAA Security Rule

| HIPAA Requirement | § Reference | ZoD Implementation |
|-------------------|-------------|-------------------|
| Access controls | §164.312(a)(1) | L1 agent identity, L4 authorization |
| Audit controls | §164.312(b) | Integrity channel, L5 logging |
| Integrity controls | §164.312(c)(1) | L4 content hashing, parameter binding |
| Transmission security | §164.312(e)(1) | L4 token encryption |
| Authentication | §164.312(d) | L1 identity, L4 token validation |

### HIPAA Privacy Rule for AI Agents

| Privacy Requirement | ZoD Implementation |
|---------------------|-------------------|
| Minimum necessary | L4 semantic policy—access only required PHI |
| Access logging | Integrity channel—all PHI access logged |
| Disclosure tracking | L5 execution logs capture all disclosures |
| Patient authorization | L7 escalation for non-routine disclosures |

### PHI-Specific Semantic Policy

```yaml
healthcare:
  phi_access:
    require_purpose: true
    allowed_purposes: ["treatment", "payment", "operations"]
    minimum_necessary: true
    log_all_access: true
  
  phi_disclosure:
    internal: "allowed_with_logging"
    external: "requires_human_approval"
    de_identified: "allowed"
    
  high_risk_actions:
    - "bulk_phi_export"
    - "phi_to_external_system"
    - "phi_in_training_data"
```

### HITRUST CSF Mapping

| HITRUST Control | Category | ZoD Layer |
|-----------------|----------|-----------|
| 01.a Access Control Policy | Access | L4, L7 |
| 01.c Privilege Management | Access | L1, L4 |
| 09.aa Audit Logging | Audit | Integrity |
| 10.h Control of Technical Vulnerabilities | Vuln Mgmt | L1 |
| 12.c Responsibilities and Procedures | Incident | L7 |

### FDA AI/ML Medical Device Guidance

| FDA Requirement | ZoD Implementation |
|-----------------|-------------------|
| Algorithm transparency | L4 token captures decision context |
| Change control | L7 GitOps policy management |
| Performance monitoring | L6 behavioral baseline |
| Risk management | Threat model, L7 risk classification |

---

## Financial Services (SOX, GLBA, PCI DSS, SEC)

### SOX Section 404 (Internal Controls)

| SOX Requirement | ZoD Implementation |
|-----------------|-------------------|
| Access controls | L1 identity, L4 authorization |
| Change management | L7 GitOps with approval |
| Audit trail | Integrity channel—immutable |
| Segregation of duties | L3 isolation, L4/L5 separation |
| Testing of controls | Verification methods for each property |

### GLBA Safeguards Rule

| Safeguard | ZoD Implementation |
|-----------|-------------------|
| Access controls | L4 semantic policy for NPI |
| Information systems | L1 infrastructure integrity |
| Detecting unauthorized access | L6 behavioral monitoring |
| Response program | Recovery protocols, L7 incident response |

### PCI DSS 4.0 for AI Payment Agents

| PCI Requirement | ZoD Implementation |
|-----------------|-------------------|
| **Req 1:** Network security | L3 network isolation |
| **Req 2:** Secure configurations | L1 infrastructure integrity |
| **Req 3:** Protect stored data | L4 semantic policy—cardholder data |
| **Req 4:** Encrypt transmission | L4 token encryption |
| **Req 5:** Protect from malware | L2 input screening |
| **Req 6:** Secure systems | L1 provenance, supply chain |
| **Req 7:** Restrict access | L4 authorization, scoped tokens |
| **Req 8:** Identify users | L1 agent identity |
| **Req 9:** Physical security | Out of scope |
| **Req 10:** Log and monitor | L6, Integrity channel |
| **Req 11:** Test security | Verification methods |
| **Req 12:** Security policies | L7 human governance |

### PCI-Specific Semantic Policy

```yaml
payment:
  cardholder_data:
    access: "never_store_in_agent_memory"
    transmission: "tokenized_only"
    display: "masked"
    
  transaction_limits:
    single_transaction: 10000
    daily_aggregate: 50000
    require_approval_above: 5000
    
  high_risk_actions:
    - "pan_access"
    - "batch_transactions"
    - "refund_processing"
    - "account_modification"
```

### SEC AI Guidance (2024-2025)

| SEC Focus Area | ZoD Implementation |
|----------------|-------------------|
| AI disclosure | Documentation in threat model, architecture |
| Conflicts of interest | L4 semantic policy—disclosure requirements |
| Fiduciary duty | L7 escalation for material decisions |
| Recordkeeping | Integrity channel—immutable audit |

---

## Government (FedRAMP, FISMA, CISA)

### FedRAMP for AI Systems

| FedRAMP Control Family | ZoD Mapping |
|------------------------|-------------|
| AC (Access Control) | L1, L4 |
| AU (Audit and Accountability) | L6, Integrity |
| CA (Assessment, Authorization) | Verification methods |
| CM (Configuration Management) | L7 GitOps |
| CP (Contingency Planning) | Recovery protocols |
| IA (Identification and Authentication) | L1, L4 |
| IR (Incident Response) | L7, Recovery |
| MP (Media Protection) | L4 data classification |
| PE (Physical/Environmental) | Out of scope |
| PL (Planning) | Documentation |
| PS (Personnel Security) | Out of scope |
| RA (Risk Assessment) | Threat model |
| SA (System Acquisition) | L1 provenance |
| SC (System and Communications) | L2, L3, L4 |
| SI (System and Information Integrity) | L6, Integrity |

### FedRAMP High Baseline AI Controls

| Control | Requirement | ZoD Implementation |
|---------|-------------|-------------------|
| AC-2 | Account Management | L1 agent identity lifecycle |
| AC-3 | Access Enforcement | L4 CA validation |
| AC-6 | Least Privilege | L4 scoped tokens |
| AU-2 | Audit Events | Integrity channel events |
| AU-3 | Audit Content | L5 execution logging |
| AU-9 | Protection of Audit | Integrity channel—immutable |
| CM-3 | Configuration Change Control | L7 GitOps |
| IA-2 | Identification and Authentication | L1 identity |
| IR-4 | Incident Handling | Recovery protocols |
| SC-7 | Boundary Protection | L3 isolation |
| SI-4 | System Monitoring | L6 monitoring |

### FISMA Requirements

| FISMA Requirement | ZoD Implementation |
|-------------------|-------------------|
| Risk categorization | L7 risk classification |
| Security controls | L1-L7 layered controls |
| Risk assessment | Threat model |
| Continuous monitoring | L6 behavioral monitoring |
| Authorization | L4 CA, L7 governance |

### CISA AI Security Guidelines

| CISA Guideline | ZoD Implementation |
|----------------|-------------------|
| Secure-by-design | Architecture spec |
| AI-specific threats | Threat model |
| Supply chain security | L1 provenance |
| Incident response | Recovery protocols |

---

## Critical Infrastructure (NERC CIP, TSA)

### NERC CIP for Energy Sector AI

| CIP Standard | Requirement | ZoD Implementation |
|--------------|-------------|-------------------|
| CIP-003 | Security Management | L7 governance |
| CIP-004 | Personnel & Training | Out of scope |
| CIP-005 | Electronic Security | L3 isolation |
| CIP-006 | Physical Security | Out of scope |
| CIP-007 | System Security | L1, L2, L4 |
| CIP-008 | Incident Response | Recovery protocols |
| CIP-009 | Recovery Plans | Recovery protocols |
| CIP-010 | Configuration Management | L7 GitOps |
| CIP-011 | Information Protection | L4 semantic policy |
| CIP-013 | Supply Chain | L1 provenance |

### AI-Specific Considerations for OT/ICS

```yaml
critical_infrastructure:
  safety_critical_actions:
    - "modify_setpoint"
    - "override_safety_limit"
    - "change_control_logic"
    require: "human_approval_always"
    
  monitoring:
    real_time_alerting: true
    safety_system_correlation: true
    
  isolation:
    network: "air_gap_or_data_diode"
    execution: "read_only_unless_approved"
```

---

## Insurance (NAIC Model Laws)

### NAIC AI Model Bulletin

| NAIC Requirement | ZoD Implementation |
|------------------|-------------------|
| Governance framework | L7 human governance |
| Risk management | Threat model, L6 monitoring |
| Transparency | L4 token captures decision context |
| Fairness testing | L6 behavioral monitoring for bias |
| Human oversight | L7 escalation workflows |
| Audit trail | Integrity channel |

### AI Underwriting Controls

```yaml
insurance:
  underwriting:
    protected_classes:
      - "race"
      - "religion"
      - "national_origin"
      - "gender"
      block_from_model_input: true
      
    adverse_actions:
      require_human_review: true
      document_reasoning: true
      
    premium_changes:
      threshold_for_review: 0.10  # 10% change
```

---

## Cross-Industry Summary

### Control Mapping by Sector

| Control | Healthcare | Finance | Government | Critical Infra |
|---------|------------|---------|------------|----------------|
| L1 Identity | HIPAA | SOX, PCI | FedRAMP | CIP |
| L2 Screening | HIPAA | GLBA | FISMA | CIP |
| L3 Isolation | HIPAA | PCI | FedRAMP | CIP |
| L4 Authorization | HIPAA | PCI, SOX | FedRAMP | CIP |
| L5 Execution | HIPAA | PCI | FedRAMP | CIP |
| L6 Monitoring | HIPAA | SOX, SEC | FISMA | CIP |
| L7 Governance | HIPAA | SOX, SEC | FedRAMP | CIP |
| Integrity | HIPAA | SOX, PCI | FedRAMP | CIP |

### Industry-Specific Escalation Thresholds

| Industry | Auto-Approve | Enhanced Review | Human Required |
|----------|--------------|-----------------|----------------|
| Healthcare | Read non-PHI | Read PHI | Disclose PHI externally |
| Finance | Read-only queries | Transactions < $1K | Transactions > $10K |
| Government | Unclassified | CUI | Classified |
| Critical Infra | Monitor only | Normal operations | Safety-critical changes |

---

## Verification Checklist

### Healthcare
- [ ] PHI access logging (Integrity)
- [ ] Minimum necessary enforcement (L4)
- [ ] Disclosure approval workflow (L7)
- [ ] HITRUST controls mapped

### Financial Services
- [ ] Transaction limits enforced (L4)
- [ ] Audit trail immutable (Integrity)
- [ ] PCI scope isolation (L3)
- [ ] SOX change control (L7)

### Government
- [ ] FedRAMP control families addressed
- [ ] Continuous monitoring (L6)
- [ ] Configuration management (L7)
- [ ] Supply chain verification (L1)

### Critical Infrastructure
- [ ] Safety-critical action controls (L7)
- [ ] OT/IT isolation (L3)
- [ ] Real-time monitoring (L6)
- [ ] Recovery procedures tested

---

## References

- [HIPAA Security Rule](https://www.hhs.gov/hipaa/for-professionals/security/)
- [PCI DSS v4.0](https://www.pcisecuritystandards.org/)
- [FedRAMP](https://www.fedramp.gov/)
- [NERC CIP Standards](https://www.nerc.com/pa/Stand/Pages/CIPStandards.aspx)
- [ZoD Compliance Mapping](compliance-mapping.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
