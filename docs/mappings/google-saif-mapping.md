# Google Secure AI Framework (SAIF) Mapping

**Zones of Distrust ↔ Google SAIF / SAIF 2.0 (June 2023, SAIF 2.0 October 2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

Google's Secure AI Framework (SAIF) organizes AI security around six core elements and four lifecycle phases. SAIF provides lifecycle guidance that ZoD operationalizes through concrete architectural controls.

> **SAIF 2.0 (October 2025):** Google expanded SAIF to address autonomous AI agents. SAIF 2.0 adds an Agent Risk Map and establishes three core agent security principles: (1) agents must have well-defined human controllers, (2) their powers must be carefully limited, and (3) their actions and planning must be observable. These principles align directly with ZoD's L7 (human oversight), L4 (authorization limits), and L6 (behavioral monitoring).

**Relationship:** SAIF tells you *when* to apply controls. ZoD tells you *what* those controls look like.

**Key SAIF Resources:**
- Six core elements (SAIF 1.0)
- Agent Risk Map and agent security principles (SAIF 2.0)
- Four lifecycle phases (Development, Deployment, Execution, Monitoring)
- SAIF Map: Four component areas (Data, Infrastructure, Model, Application)
- CoSAI (Coalition for Secure AI) Risk Map contribution (September 2025)

---

## SAIF Core Elements → ZoD Implementation

### Element 1: Expand Strong Security Foundations

**SAIF Guidance:** Extend existing security controls to AI systems; don't treat AI as separate.

**ZoD Implementation:**

| SAIF Recommendation | ZoD Layer | Control |
|--------------------|-----------|---------|
| Identity management | L1 | Agent identity as OS principal |
| Access control | L4 | Authorization scope, semantic policy |
| Network security | L3 | Cognitive isolation (network separation) |
| Encryption | L4 | Token signatures, content hashing |
| Logging | Integrity | Immutable audit trail |

**Key Insight:** ZoD extends Zero Trust foundation to AI-specific concerns.

---

### Element 2: Extend Detection and Response

**SAIF Guidance:** Adapt detection capabilities to AI-specific threats and behaviors.

**ZoD Implementation:**

| SAIF Recommendation | ZoD Layer | Control |
|--------------------|-----------|---------|
| AI-specific monitoring | L6 | Behavioral baselining |
| Anomaly detection | L6 | Drift detection, statistical analysis |
| Threat intelligence | L2 | Screening signature updates |
| Incident response | L6, L7 | Detection → containment → recovery |
| Forensics | Integrity | Immutable logs, memory snapshots |

**Key Property:** P6 (cross-layer correlation)—AI-specific detection patterns.

---

### Element 3: Automate Defenses

**SAIF Guidance:** Use automation to scale AI security; manual approaches don't work.

**ZoD Implementation:**

| SAIF Recommendation | ZoD Layer | Control |
|--------------------|-----------|---------|
| Automated input screening | L2 | ML-based screening models |
| Automated validation | L4 | Policy engine (not manual review) |
| Automated monitoring | L6 | Continuous behavioral analysis |
| Automated response | L4 | Token revocation on anomaly |
| Human-in-loop for high-risk | L7 | Escalation (not all actions) |

**Key Insight:** ZoD automates L2, L4, L6; humans at L7 for policy and high-risk.

---

### Element 4: Harmonize Platform-Level Controls

**SAIF Guidance:** Implement controls at the platform level, not per-application.

**ZoD Implementation:**

| SAIF Recommendation | ZoD Layer | Control |
|--------------------|-----------|---------|
| Platform identity | L1 | OS-level agent identity |
| Platform isolation | L3 | Container/process separation |
| Platform monitoring | L6 | Centralized behavioral analysis |
| Platform policy | L7 | Governance applies to all agents |
| Platform logging | Integrity | Unified audit channel |

**Key Insight:** ZoD layers are platform infrastructure, not app-specific.

---

### Element 5: Adapt Controls for AI

**SAIF Guidance:** Traditional controls need adaptation for AI-specific risks.

**ZoD Implementation:**

| Traditional Control | AI Adaptation | ZoD Layer |
|--------------------|---------------|-----------|
| Input validation | Prompt injection screening | L2 |
| Session management | Cognitive isolation | L3 |
| Authorization | Semantic intent policy | L4 |
| Audit logging | Behavioral logging | L6 |
| Incident response | Memory forensics | L6 |

**Key Insight:** ZoD provides the AI-adapted versions of familiar controls.

---

### Element 6: Contextualize Risks

**SAIF Guidance:** Assess risks in context; not all AI deployments have same risk profile.

**ZoD Implementation:**

| SAIF Recommendation | ZoD Layer | Control |
|--------------------|-----------|---------|
| Risk assessment | L7 | Risk classification definitions |
| Context-aware controls | L4 | Autonomy levels, risk thresholds |
| Proportional response | L4, L7 | Tiered validation (fast/standard/elevated) |
| Business alignment | L7 | Policy reflects business priorities |

**Key Property:** L7 defines context; L4 enforces proportionally.

---

## SAIF Lifecycle Phases → ZoD Layers

### Secure Development

**Phase Focus:** Build security into AI systems from the start.

**ZoD Mapping:**

| Development Activity | ZoD Layer | Control |
|---------------------|-----------|---------|
| Model selection | L1 | Model provenance attestation |
| Dependency management | L1 | Supply chain verification |
| Architecture design | L3 | Cognitive isolation built-in |
| Security requirements | Security Properties | P1-P12 as requirements |
| Threat modeling | Threat Model | Documented threats, assumptions |

---

### Secure Deployment

**Phase Focus:** Deploy AI systems with appropriate controls in place.

**ZoD Mapping:**

| Deployment Activity | ZoD Layer | Control |
|--------------------|-----------|---------|
| Identity provisioning | L1 | Agent identity established |
| Network configuration | L3 | Isolation enforced |
| Policy configuration | L4, L7 | Semantic policy, risk classification |
| Monitoring setup | L6 | Baseline establishment |
| Logging configuration | Integrity | Channel configured |

---

### Secure Execution

**Phase Focus:** Runtime security during AI system operation.

**ZoD Mapping:**

| Execution Control | ZoD Layer | Control |
|-------------------|-----------|---------|
| Input screening | L2 | Pre-reasoning filtering |
| Request validation | L4 | Semantic policy enforcement |
| Action execution | L5 | Token-bound execution |
| Credential management | L1 | Credential broker |
| Real-time policy | L4 | Dynamic authorization |

---

### Secure Monitoring

**Phase Focus:** Continuous observation and response.

**ZoD Mapping:**

| Monitoring Activity | ZoD Layer | Control |
|--------------------|-----------|---------|
| Behavioral monitoring | L6 | Baseline comparison |
| Anomaly detection | L6 | Drift detection |
| Memory auditing | L6 | Scheduled inspection |
| Integrity verification | L6 | Baseline integrity (P8) |
| Alert correlation | Integrity | Cross-layer correlation |
| Incident response | L7 | Escalation, containment |

---

## SAIF Control Types → ZoD Layers

### Governance Controls

| SAIF Control | ZoD Implementation |
|--------------|-------------------|
| Risk management | L7 risk classification |
| Policy management | L7 GitOps policy |
| Roles and responsibilities | L7 escalation workflows |
| Compliance | Integrity audit trail |

### Assurance Controls

| SAIF Control | ZoD Implementation |
|--------------|-------------------|
| Red teaming | Validation using ARP methodology |
| Vulnerability management | L2 signature updates, L1 provenance |
| Security testing | Verification methods for P1-P12 |
| Third-party assessment | Baseline integrity verification (P8) |

### Application Controls

| SAIF Control | ZoD Implementation |
|--------------|-------------------|
| Input validation | L2 screening |
| Output filtering | L4 semantic policy |
| Access control | L1 identity, L4 authorization |
| Monitoring | L6 behavioral analysis |

---

## SAIF 2.0: Agent Security Principles → ZoD

> **October 2025 Update:** SAIF 2.0 introduces specific guidance for autonomous AI agents, establishing three core principles that map directly to ZoD controls.

### Principle 1: Well-Defined Human Controllers

**SAIF 2.0 Requirement:** Agents must have clearly defined human oversight and accountability.

| SAIF 2.0 Guidance | ZoD Layer | Control |
|-------------------|-----------|---------|
| Accountability assignment | L7 | Human governance, policy ownership |
| Override capability | L7 | Kill switch, emergency stop |
| Escalation paths | L7 | Escalation workflows |
| Delegation limits | L4 | Chain validation, delegation depth |

### Principle 2: Carefully Limited Powers

**SAIF 2.0 Requirement:** Agent capabilities must be constrained to necessary scope.

| SAIF 2.0 Guidance | ZoD Layer | Control |
|-------------------|-----------|---------|
| Least privilege | L4 | Scoped authorization tokens |
| Action boundaries | L4 | Semantic policy enforcement |
| Resource limits | L5 | Execution constraints |
| Capability enumeration | L4 | Tool allowlists |

### Principle 3: Observable Actions and Planning

**SAIF 2.0 Requirement:** Agent reasoning and actions must be transparent and auditable.

| SAIF 2.0 Guidance | ZoD Layer | Control |
|-------------------|-----------|---------|
| Action logging | L5, Integrity | Execution logging, immutable audit |
| Planning transparency | L4 | Token captures decision context |
| Behavioral monitoring | L6 | Baseline comparison, drift detection |
| Memory auditing | L6 | Scheduled inspection |

### SAIF 2.0 Agent Risk Map → ZoD

| Agent Risk Category | ZoD Control |
|---------------------|-------------|
| Unauthorized actions | L4 semantic policy, L7 escalation |
| Scope creep | L4 authorization limits |
| Chain-of-thought manipulation | L2 screening, L3 isolation |
| Tool misuse | L4 tool allowlists, L5 execution bounds |
| Memory poisoning | L6 memory audit, integrity verification |
| Uncontrolled delegation | L4 chain validation, delegation depth |

---

## CoSAI Alignment

Google contributed SAIF risk data to the Coalition for Secure AI (CoSAI), an OASIS Open Project, in September 2025.

> **CoSAI Growth (2024–2025):** CoSAI has expanded to 45+ partner organizations including premier sponsors EY, Google, IBM, Microsoft, NVIDIA, Palo Alto Networks, PayPal, Protect AI, Snyk, Trend Micro, and Zscaler. Four active workstreams address: (1) AI Supply Chain Security, (2) Preparing Defenders, (3) AI Risk Governance, and (4) Secure Design for Agentic Systems.

**CoSAI-RM (Risk Map) → ZoD:**

| CoSAI-RM Category | ZoD Control |
|-------------------|-------------|
| Input risks | L2 screening |
| Processing risks | L3, L4, L5 controls |
| Output risks | L4 semantic policy |
| Memory risks | L6 memory audit |
| Integration risks | L1 supply chain, L4 chain validation |
| Agentic risks | L4, L6, L7 (SAIF 2.0 principles) |

**CoSAI Workstream Alignment:**

| CoSAI Workstream | ZoD Relevance |
|------------------|---------------|
| WS1: Supply Chain | L1 provenance, model attestation |
| WS2: Defenders | L6 monitoring, incident response |
| WS3: Risk Governance | L7 governance, threat model |
| WS4: Agentic Systems | L4, L6, L7 agent controls |

---

## Summary Matrix

| SAIF Component | ZoD Layers |
|----------------|------------|
| Element 1: Security Foundations | L1, L3, L4, Integrity |
| Element 2: Detection & Response | L6, L7, Integrity |
| Element 3: Automate Defenses | L2, L4, L6 |
| Element 4: Platform Controls | L1, L3, L6, L7, Integrity |
| Element 5: AI-Adapted Controls | L2, L3, L4, L6 |
| Element 6: Contextualize Risks | L4, L7 |
| **SAIF 2.0: Human Controllers** | **L7** |
| **SAIF 2.0: Limited Powers** | **L4, L5** |
| **SAIF 2.0: Observable Actions** | **L5, L6, Integrity** |
| Phase: Secure Development | L1, L3, Threat Model |
| Phase: Secure Deployment | L1, L3, L4, L6, L7 |
| Phase: Secure Execution | L2, L4, L5 |
| Phase: Secure Monitoring | L6, Integrity |

---

## Verification Checklist

**Core Elements (SAIF 1.0)**
- [ ] Element 1: Zero Trust extended to AI (L1, L4)
- [ ] Element 2: AI-specific detection operational (L6)
- [ ] Element 3: Automation in place (L2, L4, L6)
- [ ] Element 4: Platform-level implementation
- [ ] Element 5: AI-adapted controls active
- [ ] Element 6: Risk classification defined (L7)

**Agent Security Principles (SAIF 2.0)**
- [ ] Human controllers defined with override capability (L7)
- [ ] Agent powers limited via scoped tokens (L4)
- [ ] Actions and planning observable and logged (L5, L6)
- [ ] Delegation depth enforced (L4)

**Lifecycle Phases**
- [ ] Secure Development: Provenance, threat model
- [ ] Secure Deployment: Identity, isolation, policy
- [ ] Secure Execution: Screening, validation, execution
- [ ] Secure Monitoring: Baseline, drift, audit

**Control Types**
- [ ] Governance: Policy management via GitOps
- [ ] Assurance: Testing with verification methods
- [ ] Application: L2, L4, L6 controls active

---

## References

### Google SAIF
- [SAIF Resource Hub](https://saif.google/)
- [SAIF Map and Risk Assessment](https://saif.google/secure-ai-framework)
- [Google Safety Center - SAIF Overview](https://safety.google/cybersecurity-advancements/saif/)
- [SAIF 2.0 Announcement (October 2025)](https://blog.google/technology/safety-security/ai-security-frontier-strategy-tools/)
- [Introducing SAIF (June 2023)](https://blog.google/technology/safety-security/introducing-googles-secure-ai-framework/)

### Coalition for Secure AI (CoSAI)
- [CoSAI Website](https://www.coalitionforsecureai.org/)
- [CoSAI GitHub](https://github.com/cosai-oasis)
- [SAIF Data Donation to CoSAI (September 2025)](https://www.coalitionforsecureai.org/google-donates-secure-ai-framework-saif-data-to-coalition-for-secure-ai/)

### ZoD Documentation
- [ZoD Architecture Specification](../ARCHITECTURE.md)
- [ZoD Implementation Guide](../implementation-guide.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
