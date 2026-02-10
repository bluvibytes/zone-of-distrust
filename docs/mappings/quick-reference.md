# ZoD Mapping Quick Reference

**Cross-Framework Cheatsheet**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## ZoD Layer → Framework Quick Reference

### L1: Infrastructure Integrity

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Agent identity as distinct principal | MAESTRO 1, ATF 2, NIST AC, FedRAMP AC-2, ISO 27001 A.8.1 |
| Credential broker (no secrets in agent) | MAESTRO 1, PCI Req 8, HIPAA §164.312(d) |
| Model provenance verification | Cisco, NIST supply chain, CoSAI SSC, EU AI Act Art. 10 |
| Supply chain attestation | OWASP LLM05, Cisco, MITRE ATLAS, ATF 9 |

**Quick Test:** `Agent cannot read credentials from environment variables`

---

### L2: Input Control

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Pre-reasoning content screening | OWASP ASI01/LLM01, Cisco Input Manipulation, ENISA |
| Prompt injection detection | OWASP ASI01/LLM01, Microsoft AIRT, NVIDIA |
| Inter-agent message screening | OWASP ASI07, MAESTRO 2, Cisco Multi-Agent |
| Degraded modes under load | AWS reliability, NIST, ATF 7 |

**Quick Test:** `Known injection "ignore instructions" blocked before agent sees it`

---

### L3: Cognitive Isolation

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Reasoning separated from execution | OWASP ASI09/LLM08, NVIDIA L0-L3 levels |
| Process isolation (no network/exec) | Zero Trust, FedRAMP SC-7, PCI Req 1 |
| Policy opacity | ZoD unique (agent can't see rules) |

**Quick Test:** `Agent process cannot make HTTP requests or spawn processes`

---

### L4: Contextual Authority

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Every action validated | OWASP ASI09, MAESTRO 3, NIST Manage, Zero Trust |
| Semantic intent policy | OWASP ASI02, Cisco Tool Exploitation |
| Parameter-bound tokens | OWASP LLM02/LLM07, Cisco |
| Multi-agent chain validation | OWASP ASI07, MAESTRO 2, Cisco |
| Risk-based tiered validation | AWS Scoping, NVIDIA autonomy levels |

**Quick Test:** `Token with modified parameters is rejected by executor`

---

### L5: Constrained Executor

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Token validation before execution | Zero Trust PEP, ATF 5 |
| Immutable execution logging | SOC 2 AU, ISO 27001 A.8.15, HIPAA §164.312(b) |
| Executor attestation | NIST, CoSAI |

**Quick Test:** `All actions logged with full parameters and attribution`

---

### L6: Continuous Monitoring

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Behavioral baseline comparison | NIST Measure, SAIF, ENISA |
| Memory audit (external inspection) | OWASP ASI05, Microsoft memory poisoning |
| Drift detection | Cisco, NVIDIA, MITRE ATLAS |
| Baseline integrity verification | ZoD unique (P8 - monitor the monitor) |

**Quick Test:** `Behavioral drift beyond configured baseline thresholds generates alert`

---

### L7: Human Governance

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Risk classification | EU AI Act Art. 9, NIST Govern, MAESTRO 6 |
| Escalation workflows | EU AI Act Art. 14, HITRUST, SOX |
| Policy authority (humans define) | All frameworks |
| GitOps policy management | SOC 2 CC8, ISO 27001 A.8.9 |

**Quick Test:** `High-risk action blocked until human approval received`

---

### Integrity Channel

| What It Does | Frameworks That Require It |
|--------------|---------------------------|
| Cross-layer signal correlation | NIST, ATF 6, CoSAI |
| Immutable audit trail | EU AI Act Art. 12, SOC 2, SOX, PCI Req 10 |
| Tamper-evident logging | ISO 27001 A.8.15, FedRAMP AU-9 |

**Quick Test:** `Audit logs cannot be modified or deleted`

---

## Security Properties → Framework Validation

| Property | What It Proves | Validated By |
|----------|----------------|--------------|
| P1 | No unmediated execution | OWASP, MAESTRO, Zero Trust |
| P2 | Cryptographic attribution | SOC 2, ISO 27001, NIST |
| P3 | Replay prevention | Cisco, Microsoft AIRT |
| P4 | Parameter-bound execution | OWASP LLM02, Cisco |
| P5 | Memory poisoning detection | OWASP ASI05, Microsoft |
| P6 | Cross-layer correlation | NIST, ATF |
| P7 | Immutable forensic record | EU AI Act, SOC 2, SOX |
| P8 | Baseline integrity verification | **ZoD unique** |
| P9 | Graceful degradation | AWS, NIST |
| P10 | Compromise non-propagation | OWASP ASI07, MAESTRO |
| P11 | External policy authority | EU AI Act, NIST |
| P12 | Deterministic semantic enforcement | **ZoD unique** |

---

## Common Audit Questions → ZoD Answers

| Auditor Asks | Answer | ZoD Evidence |
|--------------|--------|--------------|
| "How do you prevent prompt injection?" | Pre-reasoning screening | L2 screening logs |
| "How do you control AI access to data?" | Semantic intent policy | L4 policy config + token logs |
| "Can the AI delete its own logs?" | No, immutable integrity channel | Append-only storage config |
| "Who approved this AI action?" | Cryptographic attribution | L4 token + L7 approval record |
| "How do you detect compromised AI?" | External behavioral monitoring | L6 baseline + drift alerts |
| "Can one AI compromise another?" | Chain validation prevents | L4 multi-agent validation |
| "What if the AI ignores rules?" | Policy is external, not visible | L3 isolation + L4 enforcement |
| "How do you recover from AI incident?" | Recovery protocols | Memory rollback, baseline reset |

---

## Compliance Crosswalk (One-Page)

| Requirement | EU AI Act | SOC 2 | ISO 27001 | HIPAA | PCI DSS | FedRAMP |
|-------------|-----------|-------|-----------|-------|---------|---------|
| **L1 Identity** | Art. 10 | CC6 | A.8.1-5 | §312(d) | Req 8 | IA-2 |
| **L2 Screening** | Art. 15 | CC7 | A.8.7 | §312(e) | Req 5 | SI-3 |
| **L3 Isolation** | Art. 15 | CC5 | A.8.22 | §312(a) | Req 1 | SC-7 |
| **L4 Validation** | Art. 14 | PI | A.8.3 | §312(a) | Req 7 | AC-3 |
| **L5 Logging** | Art. 12 | AU | A.8.15 | §312(b) | Req 10 | AU-3 |
| **L6 Monitoring** | Art. 9 | CC4 | A.8.16 | §312(b) | Req 10 | SI-4 |
| **L7 Governance** | Art. 14 | CC1 | A.5 | §164.308 | Req 12 | PL-1 |
| **Integrity** | Art. 12 | CC4 | A.8.15 | §312(b) | Req 10 | AU-9 |

---

## Threat → Control Quick Reference

| Attack Type | Primary Defense | Secondary Defense |
|-------------|-----------------|-------------------|
| Direct prompt injection | L2 screening | L4 policy blocks action |
| Indirect injection (docs) | L2 content screening | L4 semantic validation |
| Memory poisoning | L6 memory audit | L2 input screening |
| Tool exploitation | L4 parameter binding | L1 provenance |
| Multi-agent compromise | L4 chain validation | L2 inter-agent screening |
| Privilege escalation | L4 scoped tokens | L1 no standing permissions |
| Data exfiltration | L4 egress policy | L6 behavioral monitoring |
| Supply chain attack | L1 provenance | L6 behavioral baseline |
| Session hijacking | L4 token nonces (P3) | L6 session monitoring |
| Behavioral drift | L6 baseline detection | L7 escalation |

---

## Implementation Priority by Risk Level

### Low Risk (Week 1)
```
Must Have: L3, L4 (basic), L5
□ Container isolation (L3)
□ Simple scope checking (L4)  
□ Token-based execution (L5)
□ Append-only logging
```

### Medium Risk (Week 4)
```
Add: L2, L6 (basic), L7 (basic)
□ Input screening (L2)
□ Behavioral monitoring (L6)
□ Risk classification (L7)
```

### High Risk (Week 12)
```
Add: L1 (full), L6 (full), L7 (full), Integrity
□ Credential broker (L1)
□ Memory audit (L6)
□ Multi-person approval (L7)
□ Cross-layer correlation (Integrity)
```

---

## Framework Document Index

| Framework | Mapping Document | Primary Focus |
|-----------|------------------|---------------|
| OWASP Agentic | [owasp-agentic-mapping](owasp-agentic-mapping.md) | Agent risks |
| OWASP LLM | [owasp-llm-mapping](owasp-llm-mapping.md) | LLM risks |
| MAESTRO | [maestro-mapping](maestro-mapping.md) | Multi-agent |
| CSA ATF | [csa-atf-mapping](csa-atf-mapping.md) | Cloud AI |
| NIST AI RMF | [nist-ai-rmf-mapping](nist-ai-rmf-mapping.md) | Risk management |
| Google SAIF | [google-saif-mapping](google-saif-mapping.md) | Lifecycle |
| Cisco | [cisco-mapping](cisco-mapping.md) | Threat taxonomy |
| Microsoft AIRT | [microsoft-airt-mapping](microsoft-airt-mapping.md) | Failure modes |
| NVIDIA/Lakera | [nvidia-lakera-mapping](nvidia-lakera-mapping.md) | Autonomy |
| MITRE ATLAS | [mitre-atlas-mapping](mitre-atlas-mapping.md) | TTPs |
| CoSAI | [cosai-mapping](cosai-mapping.md) | Industry coalition |
| Zero Trust | [zero-trust-mapping](zero-trust-mapping.md) | ZTA extension |
| ENISA | [enisa-mapping](enisa-mapping.md) | EU threats |
| AWS Scoping | [aws-scoping-mapping](aws-scoping-mapping.md) | Cloud deployment |
| Compliance | [compliance-mapping](compliance-mapping.md) | Regulations |
| Industry | [industry-mapping](industry-mapping.md) | Sector-specific |
| Incidents | [incident-mapping](incident-mapping.md) | Case studies |

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
