# AWS Generative AI Scoping Matrix Mapping

**Zones of Distrust ↔ AWS AI Scoping Matrix (2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

AWS's Generative AI Scoping Matrix provides a practical framework for categorizing AI deployments by risk and determining appropriate security controls. It integrates with the AWS Well-Architected Framework.

**Relationship:** AWS Scoping tells you *what risk category you're in*. ZoD tells you *what controls that category needs*.

**Key AWS Resources:**
- Risk-based categorization framework
- Control selection guidance
- Well-Architected Framework integration
- Shared responsibility model for AI

> **Note:** For agentic AI deployments, AWS released the **Agentic AI Security Scoping Matrix** (November 2025), which categorizes systems by agency and autonomy levels across 4 scopes. The Agentic matrix addresses tool orchestration, persistent memory, identity delegation, and human oversight patterns that align more directly with ZoD's layered control model. See References for links to both matrices.

---

## Scoping Categories → ZoD Requirements

### Category 1: Consumer/Prototype

**AWS Definition:** Internal experimentation, no production data, limited users.

**Risk Level:** Low

**ZoD Minimum Viable:**

| Control | ZoD Layer | Implementation |
|---------|-----------|----------------|
| Basic logging | Integrity | Append-only logs |
| Simple monitoring | L6 | Basic behavioral observation |
| Manual oversight | L7 | Human review of outputs |

**ZoD Configuration:**
```yaml
category: consumer_prototype
autonomy_level: 1  # Human approves each action
monitoring: basic
logging: enabled
```

---

### Category 2: Internal Business

**AWS Definition:** Internal use with business data, multiple users, no external exposure.

**Risk Level:** Medium

**ZoD Requirements:**

| Control | ZoD Layer | Implementation |
|---------|-----------|----------------|
| Input screening | L2 | Basic injection detection |
| Authorization | L4 | Scope validation |
| Behavioral monitoring | L6 | Baseline comparison |
| Policy governance | L7 | Defined risk thresholds |
| Audit logging | Integrity | Immutable trail |

**ZoD Configuration:**
```yaml
category: internal_business
autonomy_level: 2  # Auto low-risk, human for high-risk
screening:
  enabled: true
  model: standard
monitoring:
  behavioral_baseline: enabled
  drift_threshold: 2_sigma
```

---

### Category 3: External-Facing

**AWS Definition:** Customer-facing applications, external data exposure, brand risk.

**Risk Level:** High

**ZoD Requirements:**

| Control | ZoD Layer | Implementation |
|---------|-----------|----------------|
| Full input screening | L2 | Comprehensive screening |
| Cognitive isolation | L3 | Strict separation |
| Semantic policy | L4 | Full policy enforcement |
| Parameter binding | L4 | Content hash, nonce, TTL |
| Behavioral monitoring | L6 | Real-time analysis |
| Memory audit | L6 | Scheduled inspection |
| Human oversight | L7 | Risk-weighted escalation |

**ZoD Configuration:**
```yaml
category: external_facing
autonomy_level: 2-3  # Risk-based
screening:
  enabled: true
  model: comprehensive
  degraded_mode: quarantine_with_summary
validation:
  semantic_policy: strict
  parameter_binding: full
monitoring:
  behavioral_baseline: enabled
  memory_audit:
    enabled: true
    schedule: hourly
```

---

### Category 4: Regulated/Critical

**AWS Definition:** Financial services, healthcare, critical infrastructure, regulatory requirements.

**Risk Level:** Critical

**ZoD Requirements:**

| Control | ZoD Layer | Implementation |
|---------|-----------|----------------|
| All L1 controls | L1 | Full OS foundation |
| All L2 controls | L2 | Maximum screening |
| Full L3 isolation | L3 | Strict cognitive isolation |
| Complete L4 validation | L4 | Full CA implementation |
| L5 execution controls | L5 | Token-bound, isolated |
| L6 full monitoring | L6 | All monitoring functions |
| L7 governance | L7 | Full human oversight |
| Integrity channel | Integrity | Complete implementation |

**ZoD Configuration:**
```yaml
category: regulated_critical
autonomy_level: 1-2  # Conservative
layers:
  L1: full
  L2: maximum
  L3: strict
  L4: complete
  L5: full
  L6: comprehensive
  L7: active
integrity_channel: enabled
properties:
  P1_through_P12: all_enforced
compliance:
  eu_ai_act: high_risk
  sox: if_applicable
  hipaa: if_applicable
```

---

## AWS Scoping Questions → ZoD Implications

### Question 1: Data Sensitivity

| AWS Answer | ZoD Implication |
|------------|-----------------|
| Public data only | L4 basic policies |
| Internal data | L4 classification policies |
| PII/PHI | L4 strict egress control |
| Regulated data | Full L4 + compliance mapping |

### Question 2: User Exposure

| AWS Answer | ZoD Implication |
|------------|-----------------|
| Single user/developer | Minimal L7 |
| Internal team | L7 risk classification |
| Enterprise-wide | Full L6 + L7 |
| External customers | All layers maximum |

### Question 3: Autonomy Level

| AWS Answer | ZoD Implication |
|------------|-----------------|
| Information only | L5 disabled |
| Human approves all | L7 per-action |
| Human approves high-risk | L4 risk classification + L7 selective |
| Autonomous | Full L4 policy + L6 monitoring + L7 override |

### Question 4: Integration Scope

| AWS Answer | ZoD Implication |
|------------|-----------------|
| Standalone | Basic architecture |
| Internal systems | L4 tool constraints |
| External APIs | L4 egress control |
| Multi-agent | L2 inter-agent + L4 chain validation |

### Question 5: Regulatory Requirements

| AWS Answer | ZoD Implication |
|------------|-----------------|
| None | Standard implementation |
| SOC 2 | Integrity channel, compliance mapping |
| HIPAA/PCI | Full implementation + specific policies |
| Financial regulation | Maximum controls + audit |

---

## AWS Well-Architected Pillars → ZoD Alignment

### Security Pillar

| AWS Best Practice | ZoD Control |
|-------------------|-------------|
| Identity and access management | L1 agent identity, L4 authorization |
| Detection | L6 monitoring, integrity channel |
| Infrastructure protection | L1, L3 isolation |
| Data protection | L4 semantic policy |
| Incident response | Recovery protocols |

### Reliability Pillar

| AWS Best Practice | ZoD Control |
|-------------------|-------------|
| Foundations | L1 OS foundation |
| Workload architecture | L3, L4, L5 separation |
| Change management | L7 GitOps |
| Failure management | P9 graceful degradation |

### Operational Excellence Pillar

| AWS Best Practice | ZoD Control |
|-------------------|-------------|
| Organization | L7 governance |
| Prepare | Implementation guide |
| Operate | L6 monitoring |
| Evolve | L7 policy updates |

### Performance Efficiency Pillar

| AWS Best Practice | ZoD Control |
|-------------------|-------------|
| Selection | Tiered validation (fast/standard/elevated) |
| Review | L6 baseline review |
| Monitoring | L6 performance monitoring |
| Tradeoffs | Risk vs. latency (documented) |

---

## Scoping Matrix Summary

| AWS Category | Risk | ZoD Minimum | ZoD Layers |
|--------------|------|-------------|------------|
| Consumer/Prototype | Low | Logging + manual review | L7, Integrity |
| Internal Business | Medium | Core controls | L2, L4, L6, L7, Integrity |
| External-Facing | High | Full core + monitoring | L2, L3, L4, L5, L6, L7, Integrity |
| Regulated/Critical | Critical | All controls | L1, L2, L3, L4, L5, L6, L7, Integrity |

---

## Implementation Guidance by Category

### Consumer/Prototype → Week 1

```
[ ] Basic logging enabled
[ ] Manual review process
[ ] No production data
```

### Internal Business → Weeks 1-4

```
[ ] Week 1: L3+L4+L5 core (minimum viable)
[ ] Week 2: L2 input screening
[ ] Week 3: L6 basic monitoring
[ ] Week 4: L7 risk classification
```

### External-Facing → Weeks 1-8

```
[ ] Weeks 1-2: Core containment (L3, L4, L5)
[ ] Weeks 3-4: Input control (L2)
[ ] Weeks 5-6: Monitoring (L6)
[ ] Weeks 7-8: Full validation (L4 enhanced)
```

### Regulated/Critical → Weeks 1-12+

```
[ ] Weeks 1-2: Core containment
[ ] Weeks 3-4: Input control
[ ] Weeks 5-6: Monitoring foundation
[ ] Weeks 7-8: Enhanced validation
[ ] Weeks 9-10: Memory audit
[ ] Weeks 11-12: OS foundation
[ ] Ongoing: Compliance verification
```

---

## Verification Checklist by Category

**Consumer/Prototype**
- [ ] Logging operational
- [ ] Manual review documented

**Internal Business**
- [ ] Input screening active (L2)
- [ ] Authorization enforced (L4)
- [ ] Monitoring operational (L6)
- [ ] Audit trail complete (Integrity)

**External-Facing**
- [ ] All Internal Business controls +
- [ ] Cognitive isolation verified (L3)
- [ ] Full semantic policy (L4)
- [ ] Memory audit scheduled (L6)
- [ ] Escalation workflows (L7)

**Regulated/Critical**
- [ ] All External-Facing controls +
- [ ] Full L1 implementation
- [ ] All properties (P1-P12) verified
- [ ] Compliance mapping complete
- [ ] Recovery procedures tested

---

## References

**AWS Generative AI Security Scoping Matrix:**
- [Scoping Matrix Overview](https://aws.amazon.com/ai/security/generative-ai-scoping-matrix/)
- [Introduction Blog Post](https://aws.amazon.com/blogs/security/securing-generative-ai-an-introduction-to-the-generative-ai-security-scoping-matrix/)

**AWS Agentic AI Security Scoping Matrix (November 2025):**
- [Agentic AI Scoping Matrix](https://aws.amazon.com/ai/security/agentic-ai-scoping-matrix/)
- [Framework Blog Post](https://aws.amazon.com/blogs/security/the-agentic-ai-security-scoping-matrix-a-framework-for-securing-autonomous-ai-systems/)

**AWS Well-Architected Framework:**
- [Well-Architected Overview](https://aws.amazon.com/architecture/well-architected/)

**ZoD References:**
- [ZoD Implementation Guide](../implementation-guide.md)
- [ZoD Security Properties](../security-properties.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
