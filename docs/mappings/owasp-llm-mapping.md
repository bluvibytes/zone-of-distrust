# OWASP Top 10 for LLM Applications Mapping

**Zones of Distrust ↔ OWASP LLM Top 10 (2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

The OWASP Top 10 for LLM Applications identifies vulnerabilities in Large Language Model applications. While the [Agentic Top 10](owasp-agentic-mapping.md) focuses on autonomous agents, this framework addresses foundational LLM risks that also apply to agentic systems.

**Relationship:** LLM Top 10 covers the *model* vulnerabilities. Agentic Top 10 covers *autonomous system* vulnerabilities. ZoD defends against both.

---

## Risk Mapping

### LLM01: Prompt Injection

**OWASP Definition:** Malicious inputs manipulate LLM behavior through crafted prompts, either directly or indirectly through external content.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Direct prompt injection | Input screening before reasoning | L2 |
| Indirect injection (documents, web) | Content screening, source classification | L2 |
| Jailbreak attempts | Screening + semantic policy blocks unauthorized actions | L2, L4 |
| Instruction override | Policy opacity—agent can't see what's blocked | L3, L4 |

**Key ZoD Controls:**
- L2 screens inputs *before* the agent processes them
- L4 validates actions regardless of how agent was manipulated
- Policy opacity (L3/L4) means agent can't reason around controls

**Why Traditional Defenses Fail:** System prompts and output filters operate *after* injection has already affected reasoning. ZoD screens *before*.

---

### LLM02: Insecure Output Handling

**OWASP Definition:** Insufficient validation of LLM outputs before passing to downstream systems, enabling injection attacks.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Code injection via output | Execution requires validated token | L4, L5 |
| SQL injection | Parameter binding—exact query approved | L4 |
| Command injection | Semantic policy constrains commands | L4 |
| XSS via generated content | Output validation at execution | L5 |

**Key ZoD Controls:**
- P1 (No Unmediated Execution)—all outputs must be validated
- P4 (Parameter-Bound Execution)—approved parameters cannot change
- L5 executor validates token before any action

**Key Insight:** ZoD doesn't trust LLM output. Every action requires independent validation.

---

### LLM03: Training Data Poisoning

**OWASP Definition:** Manipulation of training data to introduce vulnerabilities, backdoors, or biases into the model.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Backdoor triggers | Behavioral monitoring *may detect* anomalous response patterns | L6 |
| Bias injection | Semantic policy blocks discriminatory actions | L4 |
| Capability manipulation | Model provenance verification | L1 |

**Key ZoD Controls:**
- L1 model provenance—verify model hasn't been tampered
- L6 behavioral baseline—detect if model behaves anomalously
- L4 semantic policy—block harmful actions regardless of model behavior

**Limitation:** ZoD cannot fix a poisoned model, but can contain its actions.

---

### LLM04: Model Denial of Service

**OWASP Definition:** Attacks that consume excessive resources or degrade model performance.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Resource exhaustion | L2 input limits (token ceiling) | L2 |
| Recursive prompt loops | Behavioral monitoring detects patterns | L6 |
| Context window flooding | Input screening with size limits | L2 |

**Key ZoD Controls:**
- L2 limitation ceiling—maximum input complexity
- L2 degraded modes—graceful handling under load
- P9 (Graceful Degradation)—maintain security during resource constraints

---

### LLM05: Supply Chain Vulnerabilities

**OWASP Definition:** Risks from third-party components including models, datasets, plugins, and dependencies.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Malicious model weights | Model provenance attestation | L1 |
| Compromised plugins/tools | Supply chain verification | L1 |
| Vulnerable dependencies | Provenance checking before load | L1 |
| Typosquatting (Slopsquatting) | Allowlist-based tool loading | L1, L4 |

**Key ZoD Controls:**
- L1 model provenance—cryptographic verification
- L1 supply chain verification—component attestation
- L4 tool constraints—restrict what tools can do even if compromised

**Real-World Example:** PhantomRaven/Slopsquatting—126 malicious packages. L1 provenance would catch.

---

### LLM06: Sensitive Information Disclosure

**OWASP Definition:** LLM reveals confidential information through responses, including training data, PII, or system details.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Training data extraction | Semantic policy blocks data egress | L4 |
| PII leakage | Data classification rules | L4 |
| System prompt disclosure | Policy opacity—agent can't access policies | L3, L4 |
| Credential exposure | Credential broker—agent never holds secrets | L1 |

**Key ZoD Controls:**
- L4 semantic intent policy with data classification
- L4 egress control—restrict what data can leave
- L1 credential broker—secrets never in agent's reach
- Policy opacity—agent can't disclose what it can't see

---

### LLM07: Insecure Plugin Design

**OWASP Definition:** Plugins with inadequate access controls, input validation, or excessive permissions.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Excessive plugin permissions | Scoped authorization per request | L4 |
| Plugin input injection | Parameter binding | L4 |
| Plugin privilege escalation | No standing permissions | L1, L4 |
| Malicious plugin behavior | Behavioral monitoring | L6 |

**Key ZoD Controls:**
- L4 semantic policy—tool-specific constraints
- P4 (Parameter-Bound Execution)—exact parameters approved
- L6 monitors plugin usage patterns

**Real-World Example:** Postmark MCP BCC attack—L4 semantic policy would detect unauthorized recipients.

---

### LLM08: Excessive Agency

**OWASP Definition:** LLM granted too much autonomy, capability, or permission without adequate controls.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Unconstrained actions | Every action requires CA validation | L4 |
| Automatic execution | Cognitive isolation from execution | L3 |
| Scope creep | Scoped, short-lived tokens | L4 |
| Missing human oversight | Risk-weighted escalation | L7 |

**Key ZoD Controls:**
- P1 (No Unmediated Execution)—agent cannot bypass CA
- L3 cognitive isolation—thinking separated from doing
- L7 human governance—escalation for high-risk actions
- P11 (External Policy Authority)—humans define boundaries

**Core ZoD Principle:** "The agent that thinks is never the process that acts."

---

### LLM09: Overreliance

**OWASP Definition:** Excessive trust in LLM outputs without verification, leading to misinformation or harmful actions.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Unverified factual claims | Human review for high-impact | L7 |
| Hallucinated actions | CA validates actions exist and are authorized | L4 |
| False confidence | Behavioral baseline detects anomalies | L6 |

**Key ZoD Controls:**
- L4 validates *actions*, not just *reasoning*
- L7 escalation for consequential decisions
- L6 monitoring detects pattern changes

**Note:** ZoD doesn't prevent hallucination—it prevents hallucinated actions from executing.

---

### LLM10: Model Theft

**OWASP Definition:** Unauthorized access, copying, or extraction of proprietary LLM models.

| Attack Vector | ZoD Defense | Layer |
|---------------|-------------|-------|
| Model weight extraction | Out of ZoD scope (infrastructure security) | — |
| API abuse for distillation | Rate limiting, behavioral monitoring | L6 |
| Insider threat | Agent identity, audit logging | L1, Integrity |

**Key ZoD Controls:**
- L1 agent identity—track who/what accesses model
- Integrity channel—immutable audit trail
- L6 detects unusual query patterns

**Scope Note:** Model theft prevention is primarily infrastructure security. ZoD focuses on model *usage* security.

---

## LLM Top 10 → ZoD Layer Matrix

| OWASP LLM Risk | L1 | L2 | L3 | L4 | L5 | L6 | L7 | Integrity |
|----------------|----|----|----|----|----|----|----|----|
| LLM01: Prompt Injection | | ✓ | ✓ | ✓ | | | | |
| LLM02: Insecure Output | | | | ✓ | ✓ | | | |
| LLM03: Training Data Poisoning | ✓ | | | ✓ | | ✓ | | |
| LLM04: Model DoS | | ✓ | | | | ✓ | | |
| LLM05: Supply Chain | ✓ | | | ✓ | | | | |
| LLM06: Sensitive Info Disclosure | ✓ | | ✓ | ✓ | | | | |
| LLM07: Insecure Plugin | | | | ✓ | | ✓ | | |
| LLM08: Excessive Agency | | | ✓ | ✓ | | | ✓ | |
| LLM09: Overreliance | | | | ✓ | | ✓ | ✓ | |
| LLM10: Model Theft | ✓ | | | | | ✓ | | ✓ |

---

## LLM Top 10 vs Agentic Top 10

| LLM Top 10 | Related Agentic Top 10 | Key Difference |
|------------|------------------------|----------------|
| LLM01: Prompt Injection | ASI01: Goal Hijacking | LLM: input attack; Agentic: objective manipulation |
| LLM02: Insecure Output | ASI02: Tool Misuse | LLM: output validation; Agentic: tool exploitation |
| LLM03: Training Poisoning | ASI05: Memory Poisoning | LLM: model training; Agentic: runtime memory |
| LLM05: Supply Chain | ASI04: Supply Chain | Same concern, different scope |
| LLM07: Insecure Plugin | ASI02: Tool Misuse | Overlapping concerns |
| LLM08: Excessive Agency | ASI03: Privilege Abuse | LLM: capability grants; Agentic: privilege escalation |
| — | ASI07: Inter-Agent Compromise | Agentic-specific |
| — | ASI08: Cascading Failures | Agentic-specific |

**Recommendation:** Use both mappings for comprehensive coverage.

---

## Verification Checklist

**LLM01: Prompt Injection**
- [ ] L2 input screening active
- [ ] Injection detection model deployed
- [ ] L4 validates actions regardless of reasoning

**LLM02: Insecure Output**
- [ ] P1 enforced—no unmediated execution
- [ ] P4 enforced—parameter binding
- [ ] L5 validates tokens before execution

**LLM03-04: Training/DoS**
- [ ] L1 model provenance verified
- [ ] L2 input limits configured
- [ ] L6 baseline established

**LLM05: Supply Chain**
- [ ] L1 supply chain verification active
- [ ] Component allowlists defined

**LLM06: Sensitive Information**
- [ ] L4 data classification rules defined
- [ ] L4 egress controls active
- [ ] L1 credential broker operational

**LLM07-08: Plugin/Agency**
- [ ] L4 tool-specific constraints defined
- [ ] L3 cognitive isolation enforced
- [ ] L7 escalation thresholds set

**LLM09-10: Overreliance/Theft**
- [ ] L7 human review for high-impact
- [ ] L6 behavioral monitoring active
- [ ] Integrity channel logging

---

## References

- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [OWASP Agentic Mapping](owasp-agentic-mapping.md)
- [ZoD Architecture Specification](../ARCHITECTURE.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
