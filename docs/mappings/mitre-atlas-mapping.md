# MITRE ATLAS Mapping

**Zones of Distrust ↔ MITRE ATLAS (Adversarial Threat Landscape for AI Systems)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

MITRE ATLAS extends the ATT&CK framework to AI/ML systems, providing standardized vocabulary for adversarial machine learning tactics, techniques, and procedures (TTPs). This document maps ATLAS techniques to ZoD controls.

**Relationship:** ATLAS provides *standardized threat vocabulary*. ZoD provides *architectural defenses*.

**Scope Note:** This mapping is *partial* and focuses on agent-relevant ATLAS techniques. ATLAS contains hundreds of techniques; we prioritize those most applicable to autonomous AI agents in production environments. Contributions to expand coverage are welcome.

**Key ATLAS Resources:**
- Tactic and technique matrices
- Case study repository
- Integration with ATT&CK
- ML-specific attack patterns

---

## ATLAS Tactics → ZoD Mapping

### Reconnaissance (ML)

**Tactic Goal:** Gather information about the AI system to plan attacks.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0000: Search for Victim's AI Assets | L1 | Minimize exposed information |
| AML.T0001: Search for AI Models | L1 | Model provenance controls |
| AML.T0002: Gather AI Infrastructure Info | L1 | Infrastructure hardening |

**ZoD Response:** L1 minimizes attack surface; security through architecture, not obscurity.

---

### Resource Development (ML)

**Tactic Goal:** Establish resources to support attacks against AI systems.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0003: Acquire Public ML Models | L1 | Provenance verification required |
| AML.T0004: Develop Adversarial ML Attacks | L2 | Screening signature updates |
| AML.T0005: Obtain Capabilities | L1 | Supply chain verification |

**ZoD Response:** L1 provenance verification; L2 signature updates from threat intelligence.

---

### Initial Access (ML)

**Tactic Goal:** Gain initial access to the AI system.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0006: Supply Chain Compromise | L1 | Supply chain verification, provenance |
| AML.T0007: Phishing for ML Credentials | L1 | Credential broker (no standing credentials) |
| AML.T0008: Valid Accounts | L1, L4 | Agent identity, per-request authorization |
| AML.T0009: API Access | L4 | Authorization scope, semantic policy |

**ZoD Response:** L1 prevents supply chain compromise; L4 validates every API request.

---

### ML Model Access

**Tactic Goal:** Gain access to ML models or model information.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0010: Model API Access | L4 | Semantic policy, rate limiting |
| AML.T0011: Physical Access | L1 | Out of scope (physical security) |
| AML.T0012: Model Artifacts Access | L1 | Provenance, access control |

**ZoD Response:** L4 controls API access; L1 controls artifact access.

---

### Execution (ML)

**Tactic Goal:** Execute adversarial techniques against the AI system.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0013: User Execution | L2 | Screen user inputs |
| AML.T0014: Malicious ML Model | L1 | Provenance verification |
| AML.T0015: Exploit ML Framework | L1 | Supply chain verification |

**ZoD Response:** L2 screens inputs; L1 verifies model and framework integrity.

---

### Persistence (ML)

**Tactic Goal:** Maintain access to the AI system over time.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0016: Model Persistence | L1 | Model integrity verification |
| AML.T0017: Data Persistence | L6 | Memory audit, baseline comparison |
| AML.T0018: ML Training Pipeline Poison | L1 | Supply chain, provenance |

**ZoD Response:** L6 memory audit detects persistent modifications; L1 protects training pipeline.

---

### Defense Evasion (ML)

**Tactic Goal:** Avoid detection by AI security controls.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0019: Adversarial Example Crafting | L2 | Robust screening models |
| AML.T0020: Model Evasion | L6 | Behavioral monitoring (not just model outputs) |
| AML.T0021: Indicator Removal | Integrity | Immutable logs |
| AML.T0022: Obfuscated Files/Information | L2 | Deep content analysis |

**ZoD Response:** L6 monitors behavior externally; integrity channel prevents log tampering.

**Key Property:** P7 (immutable forensic record)—indicators cannot be removed.

---

### Discovery (ML)

**Tactic Goal:** Discover information about the AI system's environment.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0023: Model Discovery | L3 | Policy opacity (agent cannot observe controls) |
| AML.T0024: Data Discovery | L4 | Scope restrictions |
| AML.T0025: System Information Discovery | L3 | Cognitive isolation limits discovery |

**ZoD Response:** L3 cognitive isolation + policy opacity limit what agent can discover about defenses.

---

### Collection (ML)

**Tactic Goal:** Collect data from the AI system.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0026: Data from ML Systems | L4 | Egress control, data classification |
| AML.T0027: Model Inference | L4 | Rate limiting, query restrictions |
| AML.T0028: Automated Collection | L6 | Behavioral monitoring for bulk access |

**ZoD Response:** L4 semantic policy controls data access; L6 detects abnormal collection patterns.

---

### ML Attack Staging

**Tactic Goal:** Prepare collected data for exfiltration or further attacks.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0029: Data Staging | L6 | Monitor for staging patterns |
| AML.T0030: Model Staging | L6 | Detect model extraction attempts |

**ZoD Response:** L6 behavioral monitoring detects staging activities.

---

### Exfiltration (ML)

**Tactic Goal:** Steal data or models from the AI system.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0031: Exfiltration via ML Inference API | L4 | Query rate limiting, output restrictions |
| AML.T0032: Exfiltration via Web Service | L4 | Egress control policies |
| AML.T0033: Exfiltration to Cloud Storage | L4 | Destination allowlists |

**ZoD Response:** L4 semantic policy with egress control prevents exfiltration.

**Key Property:** P12 (deterministic semantic enforcement)—blocked regardless of justification.

---

### Impact (ML)

**Tactic Goal:** Manipulate, degrade, or destroy AI systems and data.

| ATLAS Technique | ZoD Layer | Control |
|-----------------|-----------|---------|
| AML.T0034: Cost Harvesting | L4 | Rate limiting, resource quotas |
| AML.T0035: Data Manipulation | L6 | Memory audit, integrity verification |
| AML.T0036: Model Manipulation | L1 | Provenance verification |
| AML.T0037: ML Model Denial of Service | L4, L6 | Rate limiting, availability monitoring |
| AML.T0038: Financial Harm | L4, L7 | Semantic policy, human approval for financial |

**ZoD Response:** L4 constrains impact scope; L7 requires human approval for high-impact actions.

---

## ATLAS Case Studies → ZoD Lessons

### Case Study: Model Extraction

**ATLAS Pattern:** Adversary uses inference API to extract model functionality.

**ZoD Defense:**
- L4: Query rate limiting
- L4: Output restrictions (limited precision)
- L6: Detect extraction patterns
- Integrity: Log all queries for forensics

### Case Study: Data Poisoning

**ATLAS Pattern:** Adversary poisons training data to embed backdoors.

**ZoD Defense:**
- L1: Training data provenance
- L6: Memory audit for operational data
- L2: Screen data before ingestion

### Case Study: Adversarial Examples

**ATLAS Pattern:** Crafted inputs cause misclassification.

**ZoD Defense:**
- L2: Robust screening models
- L4: Semantic policy (actions, not classifications)
- L6: Behavioral monitoring for classification drift

---

## ATLAS → ZoD Layer Summary

| ATLAS Tactic | Primary ZoD Layers | Key Controls |
|--------------|-------------------|--------------|
| Reconnaissance | L1 | Minimize exposure |
| Resource Development | L1, L2 | Provenance, signatures |
| Initial Access | L1, L4 | Supply chain, authorization |
| ML Model Access | L1, L4 | Provenance, API controls |
| Execution | L1, L2 | Provenance, screening |
| Persistence | L1, L6 | Integrity, memory audit |
| Defense Evasion | L2, L6, Integrity | Robust screening, external monitoring, immutable logs |
| Discovery | L3 | Policy opacity, isolation |
| Collection | L4, L6 | Egress control, behavioral monitoring |
| ML Attack Staging | L6 | Pattern detection |
| Exfiltration | L4 | Semantic policy, egress control |
| Impact | L4, L7 | Scope limits, human approval |

---

## ATLAS Technique → ZoD Property Mapping

| ATLAS Technique Category | Key ZoD Property |
|-------------------------|------------------|
| Supply chain attacks | L1 provenance |
| Input manipulation | L2 screening |
| Persistence attacks | P5 (memory poisoning detection) |
| Evasion attacks | P7 (immutable forensic record), P8 (baseline integrity) |
| Exfiltration attacks | P12 (deterministic semantic enforcement) |
| Impact attacks | P1 (no unmediated execution), P11 (external policy authority) |

---

## Verification Checklist

**Initial Access Defense**
- [ ] Supply chain verification operational (L1)
- [ ] Credential broker in place (L1)
- [ ] Per-request authorization enforced (L4)

**Persistence Defense**
- [ ] Memory audit scheduled (L6)
- [ ] Model integrity verification (L1)

**Evasion Defense**
- [ ] Logs immutable (Integrity)
- [ ] External behavioral monitoring (L6)
- [ ] Baseline integrity verification (P8)

**Collection/Exfiltration Defense**
- [ ] Egress policies defined (L4)
- [ ] Rate limiting operational (L4)
- [ ] Behavioral patterns monitored (L6)

**Impact Defense**
- [ ] Resource quotas enforced (L4)
- [ ] High-impact requires human approval (L7)

---

## References

- [MITRE ATLAS](https://atlas.mitre.org/)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [ZoD Architecture Specification](../docs/architecture.md)
- [ZoD Threat Model](../docs/threat-model.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
