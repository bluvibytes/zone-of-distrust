# FedRAMP 20x Mapping

**Zones of Distrust ↔ FedRAMP 20x**

*Version 0.9 RFC — May 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

> **Document Currency:** FedRAMP 20x is in active development. KSI identifiers, theme structure, and specific requirements may evolve. The authoritative source is the FedRAMP Machine-Readable (FRMR) documentation at [github.com/FedRAMP/docs](https://github.com/FedRAMP/docs). This mapping was verified against FRMR v0.9.43-beta (2026-04-08). Readers should consult the authoritative FRMR for current state.

---

## Overview

This document maps ZoD architectural controls to the FedRAMP 20x continuous-monitoring authorization model. FedRAMP 20x is the federal authorization framework for cloud service providers (CSPs), reframed under the 2022 FedRAMP Authorization Act and OMB Memorandum M-24-15 from a control-narrative model to an outcome-based, continuously-validated capability assessment. Phase 2 of the 20x pilot is active through March 2026; Phase 3 widescale adoption is expected Q3-Q4 2026.

**Note on legacy FedRAMP (Rev5):** Rev5 control families are referenced at a high level in [industry-mapping.md](industry-mapping.md) under Federal Sector. Rev5 remains operationally active during the transition period; FedRAMP plans to stop accepting new Rev5-based authorizations in FY27 Q4. The detailed mapping below focuses on FedRAMP 20x KSIs as the forward-looking authorization model.

---

## Relationship

ZoD is **strongly complementary** to FedRAMP 20x. Where 20x specifies what CSPs must continuously validate to receive federal authorization, ZoD provides architectural mechanisms that produce those validation artifacts for autonomous agent capabilities — an adjacent capability category outside current KSI scope.

This mapping uses three classification tiers:

- **Direct** — KSI requirement and ZoD layer share the same conceptual boundary
- **Extends** — ZoD layer extends a KSI concept into agent-specific territory
- **Adjacent** — ZoD layer addresses a gap that sits next to KSI scope but is not directly named

---

## FedRAMP 20x Structure

FedRAMP 20x organizes Key Security Indicators (KSIs) into 11 themes:

- **AFR** — Authorization by FedRAMP
- **CED** — Cybersecurity Education
- **CMT** — Change Management
- **CNA** — Cloud Native Architecture
- **IAM** — Identity and Access Management
- **INR** — Incident Response
- **MLA** — Monitoring, Logging, and Auditing
- **PIY** — Policy and Inventory
- **RPL** — Recovery Planning
- **SCR** — Supply Chain Risk
- **SVC** — Service Configuration

KSIs use descriptive 3-letter suffixes (e.g., KSI-CNA-MAT, KSI-IAM-JIT). Earlier numeric forms (KSI-CNA-02, KSI-IAM-04) are listed as "formerly known as" in the FRMR.

KSI-CSX-SUM (formerly FRR-KSI-02) requires implementation summaries with: goals and pass/fail criteria with traceability, consolidated information resources, machine-based validation processes with persistent cycles, non-machine-based validation processes, current implementation status, and clarifications.

**Scope boundary.** None of the 11 KSI themes is autonomous agent containment. KSI-CNA-MAT (formerly KSI-CNA-02) acknowledges compromise as a possibility — "lateral movement is minimized if compromised" — and provides lateral-movement minimization at the network and service layer. The case where the compromised entity is the reasoning of a machine-based information resource sits at the edge of current 20x scope.

---

## ZoD Layer Mapping to FedRAMP 20x KSIs

### Authorization by FedRAMP (AFR)

| KSI | ZoD Layer(s) | Tier | Note |
|-----|--------------|------|------|
| KSI-AFR-VDR Vulnerability Detection and Response *(formerly KSI-AFR-04)* | L1, L6 | Direct | Model provenance + cross-layer correlation |
| KSI-AFR-SCN Significant Change Notifications *(formerly KSI-AFR-05)* | L1, L7 | Direct | Model swap detection + governance approval logs |
| KSI-AFR-PVA Persistent Validation and Assessment *(formerly KSI-AFR-09)* | L4, L6, ISC | Direct | CA validates every action; baseline monitor produces continuous posture; ISC maintains immutable state |

### Cloud Native Architecture (CNA)

| KSI | ZoD Layer(s) | Tier | Note |
|-----|--------------|------|------|
| KSI-CNA-MAT Minimizing Attack Surface *(formerly KSI-CNA-02)* | L3, L4 | Extends | Cognitive isolation extends compromise containment to within-resource boundary |
| KSI-CNA-ULN Using Logical Networking *(formerly KSI-CNA-03)* | L4 | Extends | Parameter-bound tokens enforce action-level (not just network-level) traffic flow |
| KSI-CNA-DFP Defining Functionality and Privileges *(formerly KSI-CNA-04)* | L1 | Extends | Cryptographic attestation of model and policy artifacts at load extends infrastructure framing to model artifacts |
| KSI-CNA-RVP Reviewing Protections *(formerly KSI-CNA-05)* | L6 | Extends | Behavioral drift detection extends DOS protection to agentic anomalies |
| KSI-CNA-EIS Enforcing Intended State *(formerly KSI-CNA-08, Moderate)* | L4, L6, L7 | Direct | CA auto-denies; baseline auto-alerts; risk-weighted auto-escalation |

### Identity and Access Management (IAM)

| KSI | ZoD Layer(s) | Tier | Note |
|-----|--------------|------|------|
| KSI-IAM-SNU Securing Non-User Authentication *(formerly KSI-IAM-03)* | L4 | Extends | Action-first CA — verified caller identity is necessary but not sufficient for authorization |
| KSI-IAM-JIT Authorizing Just-in-Time *(formerly KSI-IAM-04)* | L4 | Extends | Per-action token issuance with parameter binding extends time-bounded JIT to action-bounded JIT |
| KSI-IAM-ELP Ensuring Least Privilege *(formerly KSI-IAM-05)* | L4, L5 | Direct | Narrowest-possible scope per action; constrained executor enforces token bounds |
| KSI-IAM-SUS Responding to Suspicious Activity *(formerly KSI-IAM-06)* | L6, L7 | Direct | Baseline deviation triggers elevated CA scrutiny or human escalation |
| KSI-IAM-AAM Automating Account Management *(formerly KSI-IAM-07)* | L1 | Direct | Model and agent lifecycle attestation |

### Monitoring, Logging, and Auditing (MLA)

| KSI | ZoD Layer(s) | Tier | Note |
|-----|--------------|------|------|
| KSI-MLA-OSM Operating SIEM Capability *(formerly KSI-MLA-01)* | ISC | Direct | Integrity Signal Channel feeds SIEM with cross-layer correlated events |
| KSI-MLA-RVL Reviewing Logs *(formerly KSI-MLA-02)* | ISC | Direct | All ZoD layers emit to immutable Integrity Channel |
| KSI-MLA-EVC Evaluating Configurations *(formerly KSI-MLA-05)* | L6 | Extends | Continuous baseline configuration evaluation extends to behavioral baselines |
| KSI-MLA-LET Logging Event Types *(formerly KSI-MLA-07)* | All layers | Direct | Event types specified per layer (see ZOD-SPEC Section 13) |
| KSI-MLA-ALA Authorizing Log Access *(formerly KSI-MLA-08, Moderate)* | L4 | Direct | CA governs access to log data the same as any other resource |

### Service Configuration (SVC)

| KSI | ZoD Layer(s) | Tier | Note |
|-----|--------------|------|------|
| KSI-SVC-ACM Automating Configuration Management *(formerly KSI-SVC-04)* | L4, L7 | Direct | Policy-as-code via GitOps; dynamic policy load with attestation |
| KSI-SVC-VRI Validating Resource Integrity *(formerly KSI-SVC-05)* | L1, L4 | Direct | Cryptographic resource validation; content hash binding in tokens |
| KSI-SVC-ASM Automating Secret Management *(formerly KSI-SVC-06)* | L4, L6 | Direct | Token-bound secret access; anomaly detection on secret usage |
| KSI-SVC-VCM Validating Communications *(formerly KSI-SVC-09, Moderate)* | ISC | Direct | Independent, tamper-evident, cross-layer integrity signal channel |

### Change Management (CMT)

| KSI | ZoD Layer(s) | Tier | Note |
|-----|--------------|------|------|
| KSI-CMT-LMC Logging Changes *(formerly KSI-CMT-01)* | ISC | Direct | Policy/model/configuration changes logged with cryptographic attribution |
| KSI-CMT-RMV Redeploying vs Modifying *(formerly KSI-CMT-02)* | L1 | Direct | Immutable resource validation at load |
| KSI-CMT-VTD Validating Throughout Deployment *(formerly KSI-CMT-03)* | L4, L5, L6 | Direct | Enforcement layers run continuously; baseline validates post-change |

### Supply Chain Risk (SCR)

| KSI | ZoD Layer(s) | Tier | Note |
|-----|--------------|------|------|
| KSI-SCR-MIT Mitigating Supply Chain Risk *(formerly KSI-TPR-03)* | L1, L4 | Direct | Model provenance; third-party tool constraints |
| KSI-SCR-MON Monitoring Supply Chain Risk *(formerly KSI-TPR-04)* | L6 | Extends | Behavioral monitoring extends to third-party agent integrations |

---

## Where FedRAMP 20x Goes Beyond Current ZoD Scope

ZoD does not cover the full breadth of FedRAMP 20x. The following 20x themes and KSIs address concerns that ZoD intentionally leaves to organizational, operational, or other architectural layers:

| 20x Coverage | ZoD Position |
|--------------|--------------|
| KSI-CED (Cybersecurity Education) — all four KSIs | Out of scope — ZoD is an architecture, not a training program |
| KSI-RPL (Recovery Planning) — Recovery Objectives, Recovery Plans, Backup Alignment, Recovery Testing | Partial — ZoD references recovery protocols but does not formally specify recovery objectives or testing |
| KSI-INR (Incident Response) — Reviewing Procedures, Reviewing Past Incidents, After Action Reports | Partial — ZoD's L7 governance touches incident response but does not formalize procedures, reviews, or after-action reports to 20x granularity |
| KSI-PIY (Policy and Inventory) including KSI-PIY-RES Reviewing Executive Support | Partial — ZoD assumes governance participation but does not specify executive support artifacts |
| KSI-CED Persistent training and testing of staff | Out of scope — ZoD does not address human knowledge validation |
| KSI-RPL-RRO Recovery Time and Recovery Point Objectives | Pending — recovery formalization is a v1.0 ZoD development item |

**A FedRAMP 20x authorization requires the full breadth of KSI coverage.** ZoD provides architectural mechanisms for the technical controls in CNA, IAM, MLA, SVC, CMT, SCR, and parts of AFR. The procedural and organizational themes (CED, INR, RPL, PIY) require additional CSP-side processes that ZoD does not provide.

---

## Capabilities at the Scope Boundary

ZoD addresses capability outcomes that fall outside the current KSI themes. The following are ZoD capabilities that do not have direct KSI equivalents:

| ZoD Capability | Where It Sits Relative to Current KSIs |
|----------------|----------------------------------------|
| Action-first authorization (CA) | Adjacent to KSI-IAM-SNU (identity-bound authorization) |
| Memory audit as architectural primitive | Outside current scope; agent memory is treated as application concern, despite creating cross-session attack surface that bypasses per-request validation |
| Cross-request sequence tracking | Adjacent to KSI-IAM-JIT (per-call authorization, not action chains) |
| Behavioral baseline integrity | Adjacent to KSI-IAM-SUS (identity-anchored anomalies, not behavior-anchored) |
| Cognitive isolation (reasoning vs. execution) | Adjacent to KSI-CNA-MAT (lateral movement minimization at network layer) |
| Deterministic semantic enforcement | Outside current scope; meaning of agent actions is not addressed |

These map to ZoD-novel properties P8 (Baseline Integrity Verification) and P12 (Deterministic Semantic Enforcement), among others.

---

## Compliance Level Coverage

| ZoD Compliance Level | Architectural Alignment with FedRAMP 20x Baseline |
|----------------------|---------------------------------------------------|
| ZoD-L1 Tokenized Execution | Aligns with elements of CNA, IAM core KSIs (Low) |
| ZoD-L2 Semantic Intent Policy | Aligns with elements of KSI-CNA-ULN, KSI-IAM-JIT (Low) |
| ZoD-L3 Monitoring & Memory Audit | Aligns with KSI-CNA-EIS, KSI-MLA-ALA (Moderate) |
| ZoD-L4 Integrity Signal Channel | Aligns with KSI-SVC-VCM (Moderate) |
| ZoD-L5 Multi-Agent Chain | Beyond current 20x scope |

ZoD-L4 and L5 deliver capabilities that exceed current 20x Moderate baseline coverage. **ZoD compliance levels are not equivalent to 20x authorization.** Architectural alignment supports KSI evidence generation but does not constitute authorization on its own.

---

## Evidence Artifacts for KSI-CSX-SUM (Implementation Summaries)

KSI-CSX-SUM (formerly FRR-KSI-02) requires providers to maintain simple high-level summaries for each Key Security Indicator including: goals with pass/fail criteria and traceability, consolidated information resources to be validated, machine-based validation processes and persistent cycles, non-machine-based validation processes and persistent cycles, current implementation status, and clarifications or responses to assessment summaries.

ZoD produces machine-checkable evidence directly:

| KSI-CSX-SUM Element | ZoD Evidence Artifact |
|--------------------|------------------------|
| Goals with pass/fail criteria and traceability | Bounded assurance specification per agent (max damage, detection window, what stops it) |
| Consolidated information resources | L4 token issuance log; L1 attested model/policy manifest |
| Machine-based validation processes and persistent cycles | L4 CA enforcement decisions; L6 baseline deviation reports; ISC event stream |
| Non-machine-based validation processes | L7 governance review records; policy-as-code commit history |
| Current implementation status | ZoD compliance level (L1–L5) with attested verification |
| Clarifications | ISC event metadata; governance decision logs |

---

## References

- FedRAMP 20x Overview: https://www.fedramp.gov/20x/
- FedRAMP 20x Documentation (human-readable): https://www.fedramp.gov/docs/20x/
- Key Security Indicators: https://www.fedramp.gov/docs/20x/key-security-indicators/
- FedRAMP Machine-Readable Documentation (FRMR): https://github.com/FedRAMP/docs
- FedRAMP Authorization Act: 44 USC § 3609
- OMB Memorandum M-24-15: https://www.whitehouse.gov/wp-content/uploads/2024/07/M-24-15-Modernizing-the-Federal-Risk-and-Authorization-Management-Program-FedRAMP.pdf
- NIST SP 800-53 Rev. 5: https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final
- RFC-0024 Machine-Readable Packages: https://www.fedramp.gov/rfcs/0024/

## Cross-References Within ZoD Repo

- [zero-trust-mapping.md](zero-trust-mapping.md) — KSI-CNA-MAT lateral movement language aligns with ZTA
- [compliance-mapping.md](compliance-mapping.md) — Federal authorization in broader compliance context
- [industry-mapping.md](industry-mapping.md) — Legacy FedRAMP Rev5 sector-specific coverage

---

*Mapping last updated: May 2026*
*ZoD Version: v0.9 RFC*
*FedRAMP 20x Version: Phase 2 Pilot, FRMR v0.9.43-beta (2026-04-08)*
