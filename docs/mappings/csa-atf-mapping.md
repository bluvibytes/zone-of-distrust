# CSA Agentic Trust Framework (ATF) Mapping

**Zones of Distrust ↔ CSA ATF (February 2026)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

The Cloud Security Alliance's Agentic Trust Framework (ATF) provides comprehensive technology-agnostic control requirements for AI agent security, integrating with CSA's Cloud Controls Matrix (CCM).

**Relationship:** ATF defines *control requirements*. ZoD provides *one compliant architectural pattern*.

**Key ATF Resources:**
- Technology-agnostic control requirements
- CCM integration
- Cloud-native AI security patterns
- Multi-stakeholder governance model

---

## ATF Control Domains → ZoD Implementation

### Domain 1: Agent Governance

**ATF Requirement:** Establish governance structures for AI agent deployment and operation.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Policy authority | L7 | Human governance, external policy |
| Risk management | L7 | Risk classification, escalation |
| Accountability | Integrity | Immutable audit trail, P2 attribution |
| Change management | L7 | GitOps policy management |
| Compliance management | L7, Integrity | Compliance mapping, audit evidence |

**Key Property:** P11 (External Policy Authority)

---

### Domain 2: Agent Identity

**ATF Requirement:** Establish and verify agent identities throughout lifecycle.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Identity establishment | L1 | Agent identity as OS principal |
| Identity verification | L4 | Token binding includes agent ID |
| Lifecycle management | L1 | Provisioning, rotation, decommissioning |
| Identity attributes | L1 | Distinct from user identity |
| Identity federation | L4 | Chain validation for multi-agent |

**Key Property:** P2 (Cryptographic Attribution)

---

### Domain 3: Access Management

**ATF Requirement:** Control agent access to resources and capabilities.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Authorization | L4 | Per-request scope evaluation |
| Least privilege | L4 | No standing permissions |
| Privilege separation | L3, L5 | Reasoning ≠ execution |
| Dynamic authorization | L4 | Runtime policy evaluation |
| Credential management | L1 | Credential broker |

**Key Property:** P4 (Parameter-Bound Execution)

---

### Domain 4: Data Protection

**ATF Requirement:** Protect data processed by and accessible to agents.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Data classification | L4 | Semantic policy (classification rules) |
| Data minimization | L4 | Scope restrictions |
| Data integrity | L6 | Memory audit, integrity verification |
| Data confidentiality | L4 | Egress control policies |
| Data retention | L6, Integrity | Memory management, log retention |

**Key Property:** P12 (Deterministic Semantic Enforcement)

---

### Domain 5: Communication Security

**ATF Requirement:** Secure all agent communications.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Channel security | L2 | Screened communication paths |
| Message integrity | L4 | Token includes content hash |
| Authentication | L4 | Token signature verification |
| Inter-agent security | L2, L4 | Inter-agent screening + chain validation |
| External communication | L4 | Egress policies, destination allowlists |

**Key Property:** P10 (Compromise Non-Propagation)

---

### Domain 6: Runtime Security

**ATF Requirement:** Protect agents during execution.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Input validation | L2 | Pre-reasoning screening |
| Execution isolation | L3, L5 | Cognitive isolation + execution separation |
| Memory protection | L1, L6 | OS isolation + memory audit |
| Resource constraints | L4 | Rate limiting, quotas |
| Error handling | L2, L4 | Degraded modes |

**Key Properties:** P1 (No Unmediated Execution), P9 (Graceful Degradation)

---

### Domain 7: Threat Detection

**ATF Requirement:** Detect threats to and from agents.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Behavioral monitoring | L6 | Continuous observation |
| Anomaly detection | L6 | Drift detection, statistical analysis |
| Threat intelligence | L2 | Screening signature updates |
| Attack detection | L6 | Cross-layer correlation |
| Insider threat detection | L6 | Behavioral baseline (agent as insider) |

**Key Properties:** P5, P6, P8

---

### Domain 8: Incident Response

**ATF Requirement:** Respond to security incidents involving agents.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Detection | L6 | Monitoring alerts |
| Containment | L4 | Token revocation, quarantine |
| Investigation | L6, Integrity | Forensic analysis, audit logs |
| Remediation | L6 | Memory rollback, baseline reset |
| Recovery | L7 | Probationary monitoring |

**Recovery Protocol:** Detection → Containment → Forensics → Remediation → Recovery → Review

---

### Domain 9: Supply Chain Security

**ATF Requirement:** Secure the agent software supply chain.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Model provenance | L1 | Cryptographic verification |
| Dependency management | L1 | Supply chain verification |
| Tool validation | L1 | Provenance attestation |
| Third-party risk | L1, L2 | Verification + output screening |
| Update management | L1 | Verified update process |

---

### Domain 10: Resilience

**ATF Requirement:** Ensure agent system resilience.

**ZoD Implementation:**

| ATF Control | ZoD Layer | ZoD Control |
|-------------|-----------|-------------|
| Availability | L2, L4 | Degraded modes |
| Fault tolerance | P9 | Graceful degradation |
| Recovery capability | Recovery | Documented protocols |
| Cascade prevention | L4 | Scope isolation |
| Business continuity | L7 | Risk-weighted operations |

**Key Property:** P9 (Graceful Degradation)

---

## ATF → CCM Integration

ATF integrates with CSA Cloud Controls Matrix. ZoD supports this integration:

| CCM Domain | ATF Domain | ZoD Layers |
|------------|------------|------------|
| Governance & Risk | Agent Governance | L7, Integrity |
| IAM | Agent Identity, Access Mgmt | L1, L4 |
| Data Security | Data Protection | L4, L6 |
| Infrastructure | Runtime Security | L1, L3, L5 |
| Logging & Monitoring | Threat Detection | L6, Integrity |
| Incident Response | Incident Response | L4, L6, L7 |
| Supply Chain | Supply Chain Security | L1 |
| BCM | Resilience | L2, L4, L7 |

---

## Summary Matrix

| ATF Domain | Primary ZoD Layers | Key Properties |
|------------|-------------------|----------------|
| Agent Governance | L7, Integrity | P11 |
| Agent Identity | L1, L4 | P2 |
| Access Management | L1, L3, L4, L5 | P4 |
| Data Protection | L4, L6, Integrity | P12 |
| Communication Security | L2, L4 | P10 |
| Runtime Security | L1, L2, L3, L5, L6 | P1, P9 |
| Threat Detection | L6, Integrity | P5, P6, P8 |
| Incident Response | L4, L6, L7, Integrity | — |
| Supply Chain Security | L1, L2 | L1 provenance |
| Resilience | L2, L4, L7 | P9 |

---

## Verification Checklist

**Governance**
- [ ] Policy authority documented (L7)
- [ ] Risk classification defined (L7)
- [ ] Change management via GitOps (L7)

**Identity & Access**
- [ ] Agent identity established (L1)
- [ ] Credential broker operational (L1)
- [ ] Per-request authorization (L4)

**Data & Communication**
- [ ] Data classification in policy (L4)
- [ ] Egress controls defined (L4)
- [ ] Inter-agent screening enabled (L2)
- [ ] Chain validation operational (L4)

**Runtime**
- [ ] Input screening active (L2)
- [ ] Cognitive isolation enforced (L3)
- [ ] Execution isolation enforced (L5)
- [ ] Degraded modes tested

**Detection & Response**
- [ ] Behavioral monitoring active (L6)
- [ ] Incident response documented
- [ ] Recovery protocols tested

**Supply Chain & Resilience**
- [ ] Provenance verification enabled (L1)
- [ ] Graceful degradation tested (P9)

---

## References

- [Cloud Security Alliance](https://cloudsecurityalliance.org/)
- [CSA Cloud Controls Matrix](https://cloudsecurityalliance.org/research/cloud-controls-matrix/)
- [ZoD Architecture Specification](../docs/architecture.md)
- [ZoD Security Properties](../docs/security-properties.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
