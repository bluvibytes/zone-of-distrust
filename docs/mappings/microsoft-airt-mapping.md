# Microsoft AIRT Taxonomy of Failure Modes Mapping

**Zones of Distrust ↔ Microsoft AI Red Team Taxonomy (April 2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

Microsoft's AI Red Team (AIRT) published a taxonomy classifying failures across security and safety dimensions, distinguishing novel agentic failures from existing failures with new severity in agentic contexts.

**Relationship:** Microsoft identifies *failure modes*. ZoD provides *architectural defenses*.

**Key Microsoft Resources:**
- Taxonomy of Failure Modes in Agentic AI Systems (April 2025)
- Memory poisoning case study (LangChain/LangGraph/GPT-4o)
- Microsoft SDL for AI (February 2026 update)

---

## Novel Agentic Failure Modes

### Agent Compromise

**Microsoft Definition:** An agent is manipulated to act against its intended purpose through prompt injection or context manipulation.

**ZoD Controls:**

| Attack Vector | ZoD Layer | Control |
|---------------|-----------|---------|
| Direct prompt injection | L2 | Pre-reasoning input screening |
| Indirect injection (documents) | L2 | Content screening before processing |
| Context window poisoning | L2 | Limitation ceiling, content classification |
| Reasoning manipulation | L3, L4 | Cognitive isolation + external validation |

**Key Insight:** Microsoft notes compromise is invisible from inside. ZoD addresses this with L6 external monitoring.

---

### Agent Injection

**Microsoft Definition:** Malicious instructions are inserted into agent context through external data sources.

**ZoD Controls:**

| Injection Source | ZoD Layer | Control |
|------------------|-----------|---------|
| Email content | L2 | Screen all external content |
| Web pages | L2 | Screen retrieved content |
| Database results | L2 | Screen query results |
| API responses | L2 | Screen all tool outputs |
| User uploads | L2 | Screen all file content |

**Key Property:** L2 screens everything before L3 processes it.

---

### Agent Impersonation

**Microsoft Definition:** Attackers impersonate legitimate agents or users to exploit trust relationships.

**ZoD Controls:**

| Impersonation Type | ZoD Layer | Control |
|--------------------|-----------|---------|
| Agent identity spoofing | L1 | Agent identity as OS principal |
| User impersonation | L4 | Independent validation, not agent assertion |
| Authority claims | L4 | Semantic policy, not intent-based |
| Credential theft | L1 | Credential broker (agent never holds secrets) |

**Key Property:** P2 (cryptographic attribution)—every action traceable to verified identity.

---

### Agent Flow Manipulation

**Microsoft Definition:** Attacks corrupt control flow between agents in multi-agent systems.

**ZoD Controls:**

| Flow Attack | ZoD Layer | Control |
|-------------|-----------|---------|
| Request redirection | L4 | Multi-agent chain validation |
| Response tampering | L2 | Screen inter-agent messages |
| Workflow hijacking | L4 | Independent validation at each hop |
| Trust chain exploitation | L4 | No inherited trust |
| Cascade triggering | L4, L6 | P10 non-propagation + correlation |

**Key Property:** P10 (compromise non-propagation)—compromised agent cannot issue valid tokens for peers.

---

### Multi-Agent Jailbreaks

**Microsoft Definition:** Coordinated attacks across multiple agents to bypass individual agent safeguards.

**ZoD Controls:**

| Jailbreak Pattern | ZoD Layer | Control |
|-------------------|-----------|---------|
| Distributed prompt injection | L2 | Screen all inputs regardless of source |
| Agent collusion | L6 | Cross-agent behavioral correlation |
| Consensus attacks | L4 | Independent validation (no consensus-based approval) |
| Peer pressure manipulation | L4 | Policy-based, not peer-influenced |

**Key Insight:** ZoD's L4 validates against policy, not agent consensus or peer approval.

---

## Existing Failures with Elevated Severity

### Memory Poisoning (Microsoft Case Study)

**Microsoft Case Study:** LangChain/LangGraph/GPT-4o memory poisoning enabling data exfiltration through corrupted recall.

**ZoD Defense-in-Depth:**

| Attack Stage | ZoD Layer | Control |
|--------------|-----------|---------|
| Poisoned content injection | L2 | Screen content before memory write |
| Memory accumulation | L6 | Memory audit detects anomalous patterns |
| Corrupted reasoning | L4 | Behavioral baseline catches deviation |
| Data exfiltration attempt | L4 | Semantic policy blocks unauthorized egress |
| Forensics | L6, Integrity | Memory snapshots, source verification |

**Recovery Path:**
1. L6 detects memory anomaly
2. L4 blocks suspicious requests
3. Memory rollback to last-known-good state
4. Enhanced monitoring during probation

---

### Privilege Escalation

**Elevated Severity:** Agents with tool access can escalate to system-level privileges.

**ZoD Controls:**

| Escalation Vector | ZoD Layer | Control |
|-------------------|-----------|---------|
| Tool capability abuse | L4 | Tool-specific semantic constraints |
| Permission creep | L4 | Per-request scope evaluation |
| Credential access | L1 | Credential broker (no direct access) |
| System command execution | L5 | Execution isolation, allowlisted actions |

---

### Data Exfiltration

**Elevated Severity:** Agents with external communication can exfiltrate sensitive data.

**ZoD Controls:**

| Exfiltration Vector | ZoD Layer | Control |
|---------------------|-----------|---------|
| Email | L4 | Recipient allowlists, content policies |
| API calls | L4 | Endpoint restrictions, data classification |
| File uploads | L4 | Egress control, classification enforcement |
| Encoded channels | L4 | Semantic analysis of content |

---

## Microsoft Design Considerations → ZoD Mapping

| Microsoft Recommendation | ZoD Implementation |
|-------------------------|-------------------|
| **Identity management** | L1 agent identity as distinct principal |
| **Memory hardening** | L6 memory audit, L2 screening |
| **Control flow regulation** | L4 request validation, chain validation |
| **Environment isolation** | L3 cognitive isolation, L5 execution isolation |
| **Comprehensive logging** | Integrity channel (immutable) |

---

## Microsoft SDL for AI (February 2026) → ZoD Alignment

| SDL Requirement | ZoD Control | Status |
|-----------------|-------------|--------|
| Agent identity required | L1 agent identity | ✓ Aligned |
| RBAC enforcement | L4 authorization scope | ✓ Aligned |
| AI shutdown mechanisms | L7 kill switch, L4 revocation | ✓ Aligned |
| Memory isolation | L1 OS-level isolation | ✓ Aligned |
| Audit logging | Integrity channel | ✓ Aligned |

---

## Summary Matrix

| Microsoft Failure Mode | Type | ZoD Layers | Key Properties |
|-----------------------|------|------------|----------------|
| Agent Compromise | Novel | L2, L3, L4, L6 | P12 |
| Agent Injection | Novel | L2 | L2 screening |
| Agent Impersonation | Novel | L1, L4 | P2 |
| Agent Flow Manipulation | Novel | L2, L4, L6 | P10 |
| Multi-Agent Jailbreaks | Novel | L2, L4, L6 | P6 |
| Memory Poisoning | Elevated | L2, L4, L6 | P5 |
| Privilege Escalation | Elevated | L1, L4, L5 | P4 |
| Data Exfiltration | Elevated | L4 | P12 |

---

## Verification Checklist

**Novel Failures**
- [ ] Agent compromise: L2 screening + L6 monitoring active
- [ ] Agent injection: All input sources screened (L2)
- [ ] Agent impersonation: Agent identity verified (L1)
- [ ] Agent flow manipulation: Chain validation operational (L4)
- [ ] Multi-agent jailbreaks: Cross-agent correlation active (L6)

**Elevated Severity Failures**
- [ ] Memory poisoning: Memory audit scheduled (L6)
- [ ] Privilege escalation: Credential broker operational (L1)
- [ ] Data exfiltration: Egress policies defined (L4)

**SDL Alignment**
- [ ] Agent identity implemented (L1)
- [ ] RBAC enforced (L4)
- [ ] Shutdown mechanism tested (L7)

---

## References

- [Microsoft Taxonomy of Failure Modes](https://www.microsoft.com/en-us/security/blog/)
- [Microsoft SDL for AI](https://www.microsoft.com/en-us/securityengineering/sdl)
- [ZoD Architecture Specification](../ARCHITECTURE.md)
- [ZoD Threat Model](../threat-model.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
