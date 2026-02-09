# Zero Trust Architecture Mapping

**Zones of Distrust ↔ NIST SP 800-207 Zero Trust Architecture**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

NIST SP 800-207 defines Zero Trust Architecture (ZTA) principles for enterprise networks. ZoD extends these principles specifically for AI agents, addressing gaps where traditional ZTA falls short for agentic systems.

**Relationship:** ZTA defines *network/identity* trust principles. ZoD extends these to *cognitive/behavioral* trust for AI agents.

---

## Zero Trust Tenets → ZoD Implementation

### Tenet 1: All Data Sources and Computing Services Are Considered Resources

**NIST Definition:** Every data source and service is a resource to be protected.

| ZTA Application | ZoD Extension |
|-----------------|---------------|
| Network resources | Agent reasoning as a resource |
| Data stores | Agent memory as a resource |
| Services | Agent capabilities as resources |

**ZoD Implementation:**
- L1: Agent identity as distinct principal (resource)
- L3: Agent reasoning environment as isolated resource
- L6: Agent memory as auditable resource

---

### Tenet 2: All Communication Is Secured Regardless of Network Location

**NIST Definition:** Network location alone does not imply trust.

| ZTA Application | ZoD Extension |
|-----------------|---------------|
| Encrypted channels | All inter-layer communication validated |
| No implicit trust | Agent-to-agent treated as untrusted |
| Perimeter-less | Trust boundaries at every layer |

**ZoD Implementation:**
- L2: All inputs screened regardless of source
- L4: All actions validated regardless of origin
- Inter-layer protocols enforce trust boundaries

---

### Tenet 3: Access to Individual Resources Is Granted on a Per-Session Basis

**NIST Definition:** Access granted per session with minimum privileges.

| ZTA Application | ZoD Extension |
|-----------------|---------------|
| Session-based access | Per-request authorization |
| No standing privileges | Scoped, short-lived tokens |
| Just-in-time access | Token TTL and single-use nonces |

**ZoD Implementation:**
- L4: Every action requires fresh CA validation
- L4: Tokens are time-bound and nonce-protected (P3)
- L1: No standing credentials—credential broker

---

### Tenet 4: Access Is Determined by Dynamic Policy

**NIST Definition:** Policy considers client identity, application, and behavioral attributes.

| ZTA Application | ZoD Extension |
|-----------------|---------------|
| Identity attributes | Agent identity + behavioral baseline |
| Application context | Action context + semantic intent |
| Environmental factors | Risk level + system state |

**ZoD Implementation:**
- L4: Policy includes identity, action, target, behavioral context
- L4: Semantic intent policy evaluates action meaning
- L6: Behavioral baseline informs risk scoring
- L7: Risk classification determines required controls

---

### Tenet 5: Enterprise Monitors and Measures Security Posture

**NIST Definition:** Continuous monitoring of asset security state.

| ZTA Application | ZoD Extension |
|-----------------|---------------|
| Asset monitoring | Agent behavioral monitoring |
| Security metrics | Drift detection, baseline comparison |
| Continuous assessment | Memory audit, cross-layer correlation |

**ZoD Implementation:**
- L6: Continuous behavioral monitoring
- L6: Memory audit (agent state inspection)
- P8: Baseline integrity verification (monitor the monitor)
- Integrity channel: Cross-layer signal correlation

---

### Tenet 6: Authentication and Authorization Are Dynamic and Strictly Enforced

**NIST Definition:** Constant re-evaluation of trust.

| ZTA Application | ZoD Extension |
|-----------------|---------------|
| Re-authentication | Every action re-validated |
| Dynamic authorization | Behavioral context affects authorization |
| Strict enforcement | No bypass paths |

**ZoD Implementation:**
- P1: No unmediated execution—every action through CA
- L4: Authorization considers current behavioral state
- L3: No path from reasoning to execution without L4/L5

---

### Tenet 7: Enterprise Collects Information About Assets and Uses It to Improve Security

**NIST Definition:** Data collection drives security improvement.

| ZTA Application | ZoD Extension |
|-----------------|---------------|
| Network telemetry | Behavioral telemetry |
| Traffic analysis | Action pattern analysis |
| Security intelligence | Drift detection, anomaly correlation |

**ZoD Implementation:**
- L6: Behavioral baseline from historical data
- Integrity channel: Immutable audit trail for analysis
- L7: Post-incident review improves policy

---

## Where ZTA Falls Short for Agents

Traditional Zero Trust addresses network and identity. For AI agents, additional concerns exist:

| Gap | Why ZTA Doesn't Address It | ZoD Solution |
|-----|---------------------------|--------------|
| Pre-reasoning input control | ZTA validates requests, not content | L2 screens before agent reasons |
| Cognitive compromise | ZTA assumes entity knows if compromised | L6 external monitoring (agent can't detect own compromise) |
| Memory poisoning | ZTA doesn't address persistent state attacks | L6 memory audit |
| Behavioral drift | ZTA focuses on point-in-time access | L6 continuous baseline comparison |
| Policy opacity | ZTA policies visible to clients | L3/L4 agent cannot see policy |
| Semantic validation | ZTA validates identity/permissions | L4 validates action meaning |

**Note on Policy Opacity:** Policy opacity does not mean lack of auditability. Policies are:
- **Opaque to the agent** — the agent cannot query or probe for policy boundaries
- **Transparent to auditors/humans** — L7 governance has full visibility; all policy decisions are logged to the integrity channel

This prevents the agent from gaming visible policy edges while maintaining full audit compliance.

---

## ZTA Components → ZoD Mapping

### Policy Engine (PE)

**ZTA Definition:** Makes access decisions based on policy.

**ZoD Equivalent:** L4 Contextual Authority (CA)

| PE Function | CA Implementation |
|-------------|-------------------|
| Policy evaluation | Semantic intent policy + behavioral baseline |
| Trust scoring | Risk classification from multiple signals |
| Access decision | Token issuance or denial |

---

### Policy Administrator (PA)

**ZTA Definition:** Executes PE decisions, establishes/terminates sessions.

**ZoD Equivalent:** L4 Token Issuance + L7 Policy Management

| PA Function | ZoD Implementation |
|-------------|-------------------|
| Session establishment | Token issuance with scope/TTL |
| Session termination | Token expiration, revocation |
| Policy distribution | L7 GitOps policy management |

---

### Policy Enforcement Point (PEP)

**ZTA Definition:** Enables, monitors, and terminates connections.

**ZoD Equivalent:** L5 Constrained Executor

| PEP Function | L5 Implementation |
|--------------|-------------------|
| Access enforcement | Token validation before execution |
| Connection monitoring | Execution logging |
| Session termination | Token rejection, quarantine |

---

## ZTA Deployment Models → ZoD Patterns

### Device Agent/Gateway Model

**ZTA:** Agent on device communicates with gateway.

**ZoD Adaptation:**
```
AI Agent (L3) → Validation Gateway (L4) → Execution Gateway (L5)
```

---

### Enclave Gateway Model

**ZTA:** Gateway protects resource enclave.

**ZoD Adaptation:**
```
Agent Enclave (L3) ─┐
                    ├─→ CA Gateway (L4) → Executor Enclave (L5)
Tool Enclave (L1)  ─┘
```

---

### Resource Portal Model

**ZTA:** Portal acts as PEP for single resource.

**ZoD Adaptation:**
```
Agent → L4 CA (portal) → Specific Tool/Resource
         │
         └── Per-resource semantic policy
```

---

### Device Application Sandboxing

**ZTA:** Applications sandboxed on device.

**ZoD Adaptation:**
```
Agent Process (sandboxed, no network, no exec) ← L3
Validator Process (bridges networks) ← L4
Executor Process (separate sandbox) ← L5
```

---

## Extended Zero Trust for Agents

ZoD adds these principles beyond traditional ZTA:

### Principle: Assume Cognitive Compromise

**Traditional ZTA:** Assume breach of network/credentials.

**ZoD Extension:** Assume the agent's reasoning may be compromised.

**Implementation:**
- L2: Screen inputs before reasoning
- L4: Validate actions regardless of reasoning quality
- L6: Monitor behavior, not self-reports

---

### Principle: Separate Thought from Action

**Traditional ZTA:** Separate authentication from authorization.

**ZoD Extension:** Separate reasoning from execution.

**Implementation:**
- L3: Cognitive isolation
- L5: Execution isolation
- No direct path between them

---

### Principle: Policy Opacity

**Traditional ZTA:** Policies may be visible to aid compliance.

**ZoD Extension:** Policy invisibility is a security property.

**Implementation:**
- Agent cannot see L4 policy
- Agent cannot see L7 risk thresholds
- Knowing rules enables gaming them

---

### Principle: External State Verification

**Traditional ZTA:** Trust but verify credentials.

**ZoD Extension:** Never trust agent's self-reported state.

**Implementation:**
- L6: External memory audit
- L6: External behavioral monitoring
- Agent cannot verify its own integrity

---

## Verification Checklist

**ZTA Tenet Alignment**
- [ ] All resources protected (tenets 1-2)
- [ ] Per-request authorization (tenet 3)
- [ ] Dynamic policy with behavioral context (tenet 4)
- [ ] Continuous monitoring (tenet 5)
- [ ] Strict enforcement (tenet 6)
- [ ] Data-driven improvement (tenet 7)

**ZoD Extensions**
- [ ] Pre-reasoning input control (L2)
- [ ] Cognitive isolation (L3)
- [ ] Semantic validation (L4)
- [ ] External monitoring (L6)
- [ ] Memory audit (L6)
- [ ] Policy opacity (L3/L4)

---

## References

- [NIST SP 800-207: Zero Trust Architecture](https://csrc.nist.gov/publications/detail/sp/800-207/final)
- [ZoD Architecture Specification](../ARCHITECTURE.md)
- [ZoD Implementation Guide](../implementation-guide.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
