# **ZONES OF DISTRUST**

An open reference architecture for securing autonomous AI agents in production.

**Version:** v0.9 (RFC)  
**Date:** February 2026  
**Status:** Public Review Draft  
**Stewardship:** Created by BluVi, stewarded in the open  
**Repository:** https://github.com/bluvibytes/zone-of-distrust  
**License (Documentation):** CC BY 4.0  

**Intended Audience:** Security architects, CISOs, compliance teams, AI practitioners

---

## RFC Status

This document is published as an open Request for Comments (RFC) to solicit technical review, adversarial evaluation, and implementation feedback.

ZoD is intended to evolve toward a vendor-neutral industry reference architecture and may inform future standardization efforts.

**Target v1.0:** May–June 2026 (dependent on community feedback and validation)

This RFC may change materially based on community input, research results, and real-world deployment experience.

---

## Scope and Document Type

Zones of Distrust (ZoD) defines a layered reference architecture and a set of normative security properties intended to support interoperable implementations.

ZoD is not a formal standard. It is a specification-style framework designed to enable implementable and auditable security controls for autonomous agent systems.

Normative language (MUST, SHOULD, MAY) is used as defined in RFC 2119 and summarized in **Appendix B**.

---
# Abstract

Autonomous AI agents are increasingly deployed in production environments with the ability to access data, invoke tools, and execute actions on behalf of users and organizations. Unlike traditional software systems, AI agents can be manipulated at the reasoning layer through prompt injection, memory poisoning, context manipulation, and supply chain compromise—often without a detectable separation between legitimate reasoning and adversarial influence.

Existing security models, including Zero Trust architectures, assume that a requesting principal’s intent is stable and that compromise can be externally observed or forensically reconstructed. These assumptions do not reliably hold for autonomous reasoning systems.

This document introduces **Zones of Distrust (ZoD)**, a layered reference architecture for governing the execution of autonomous AI agents operating in enterprise or regulated environments. ZoD extends Zero Trust principles by decoupling cognition from execution, enforcing independent request validation, binding authorization cryptographically to specific execution parameters, and incorporating cross-layer monitoring and human governance.

ZoD is designed to assume compromise of the reasoning layer and to contain blast radius through structural enforcement controls rather than reliance on model alignment or input filtering alone.

This document is published as **RFC v0.9** to solicit technical review, adversarial analysis, and implementation feedback. It is intended to evolve into a vendor-neutral industry reference architecture for production-grade AI agent security.

---

# Executive Summary

AI agents are no longer theoretical. They read emails, manage calendars, execute code, access databases, and perform actions on behalf of users across enterprise systems. They operate with real credentials, make real decisions, and act inside real production environments.

The security architectures protecting them were designed for a fundamentally different type of entity.

Zero Trust gave the industry the correct philosophy: *never trust, always verify*. But Zero Trust was built for users and devices—entities that can recognize compromise, be externally interrogated, or be restored to a known-good baseline. AI agents break that assumption. A prompt-injected agent does not know it has been compromised. The manipulation becomes its reasoning. There is no separate “real agent” underneath to recover.

**Zones of Distrust (ZoD)** is a layered security architecture that extends Zero Trust into a domain it was never designed for: autonomous entities that can be compromised without knowing it, treat manipulation as truth, and accumulate exploitable state across sessions simply by doing their job.

ZoD’s core thesis is simple:

> **The agent is compromised. The architecture must still remain safe.**

---

## Zones of Distrust at a Glance

ZoD is organized as seven interdependent layers with explicit directional logic:

- **Data flows up** from the external world through screening, into reasoning, out as structured requests, through validation, and into execution.
- **Monitoring flows across** Layers 3 through 5, feeding signals into validation and governance.
- **Policy flows down** from human governance through validation into the entire stack.

Each layer depends on the layer below it. Each layer assumes every layer above it may already be compromised.

---

## Seven-Layer Model

| Layer | Name | Purpose |
|--------|------|----------|
| L7 | Human Governance | Risk-weighted escalation, policy control, break-glass procedures |
| L6 | Continuous Monitoring | Behavioral baselining, drift detection, memory audit |
| L5 | Execution | Isolated action execution with immutable logging |
| L4 | Request Validation (CA) | Independent validation, semantic policy, cryptographic token binding |
| L3 | Cognitive Isolation | Reasoning separated from execution capability |
| L2 | Input Control | Adversarial screening before the agent processes data |
| L1 | OS Foundation | Agent identity, process isolation, credential brokering |

The architectural centerpiece is **Layer 4**, a Certificate Authority (CA) that validates every action request and issues parameter-bound execution tokens that cannot be reused or modified.

ZoD does not attempt to make agents trustworthy. It builds a system that remains safe even when they are not.

---

# What This Is and What This Is Not

ZoD is designed to be falsifiable, implementable, and operationally deployable. It is a security architecture—not a safety philosophy.

## What This Is

### A reference architecture for production AI agents

ZoD is designed for enterprise and regulated environments where agents perform privileged actions.

### Execution governance for compromised reasoning

The agent cannot act directly. It can only request. Independent systems validate, constrain, and authorize execution.

### A layered defense model

Each layer reduces blast radius and assumes compromise at higher layers.

### Incremental and deployable

Most organizations can implement the core enforcement boundary (Layers 3–5) using existing infrastructure such as containers, policy engines, and token-gated execution proxies.

---

## What This Is Not

### Not a prompt injection filter

Layer 2 screens input, but ZoD assumes screening will be bypassed. The value is containment after bypass—not perfect prevention.

### Not a model alignment strategy

ZoD does not attempt to make the model safer, more honest, or more aligned. It assumes reasoning is vulnerable and builds controls around it.

### Not traditional IAM

IAM validates identity and assumes intent is stable. ZoD assumes intent can be adversarial and validates actions, semantics, and behavioral context before execution.

### Not a “human approval for everything” system

ZoD explicitly avoids approval fatigue. Only high-risk actions require human escalation; low-risk actions remain automated but governed and auditable.

---

## Applicability and Intended Audience

ZoD is intended for production AI agents with autonomous execution capability in environments where security accountability matters.

### Intended Use Cases

ZoD applies when agents can perform privileged actions such as:

- sending emails or messages externally  
- modifying or deleting data  
- initiating financial actions  
- provisioning infrastructure  
- interacting with production APIs  
- retrieving or moving sensitive information  
- operating across multi-agent delegation chains  

### Not Intended For

ZoD is not required for:

- advisory-only copilots that do not execute actions  
- purely human-in-the-loop systems where agents only generate recommendations  
- experimental or non-production prototypes without privileged access  

Organizations deploying agents that only provide suggestions, without direct execution authority, typically do not require ZoD.

---

# RFC Feedback Requested

This document is published as **Zones of Distrust RFC v0.9** to solicit adversarial review, implementation feedback, and refinement toward a future formal industry reference architecture.

## Feedback Requested

This RFC is specifically seeking feedback on:

- **Completeness of attacker positions**  
  Are there attacker placements or threat vectors missing from the model?

- **Correctness of security properties (P1–P12)**  
  Are the claims defensible and testable?

- **Feasibility of CA validation and token binding**  
  Can this architecture be implemented at scale without becoming an operational bottleneck?

- **Monitoring assumptions and integrity channel requirements**  
  Are the monitoring and audit guarantees realistic for regulated environments?

- **Gaps for compliance-driven adoption**  
  What controls are missing for SOC 2, ISO 27001, ISO 42001, EU AI Act, HIPAA, PCI, and similar environments?

---

## Out of Scope for This RFC

This RFC does **not** attempt to finalize:

- a full conformance profile or certification framework  
- a standard token schema or canonical integrity event schema  
- a reference implementation or enforcement runtime  
- formal standards body submission artifacts  

These items are targeted for v1.0+ based on feedback volume and implementation maturity.

---

## How to Submit Feedback

- **GitHub Issues:** corrections, gaps, bugs  
  https://github.com/bluvibytes/zone-of-distrust/issues  

- **GitHub Discussions:** architectural debate, alternative approaches  
  https://github.com/bluvibytes/zone-of-distrust/discussions  

- **Pull Requests:** text edits, mapping improvements, draft expansions  
  https://github.com/bluvibytes/zone-of-distrust/pulls  

---

# Contents


# Contents

**[1. The Problem: Why Current Approaches Fail](#1-the-problem-why-current-approaches-fail)**  
&emsp;[1.1 The Fundamental Asymmetry](#11-the-fundamental-asymmetry)  
&emsp;[1.2 Four Structural Blindnesses](#12-four-structural-blindnesses)  
&emsp;[1.3 The Emerging Threat Landscape](#13-the-emerging-threat-landscape)  
&emsp;[1.4 Multi-Agent Trust: The Unaddressed Surface](#14-multi-agent-trust-the-unaddressed-surface)  
&emsp;[1.5 Model Supply Chain Integrity](#15-model-supply-chain-integrity)  
&emsp;[1.6 Tool and API Semantic Abuse](#16-tool-and-api-semantic-abuse)  
&emsp;[1.7 The AI's Own Assessment](#17-the-ais-own-assessment)  

**[2. Threat Model Summary](#2-threat-model-summary)**  
&emsp;[2.1 Catastrophic Outcomes](#21-catastrophic-outcomes)  
&emsp;[2.2 Attacker Positions](#22-attacker-positions)  
&emsp;[2.3 Kill Chain Mapping](#23-kill-chain-mapping)  
&emsp;[2.4 Trust Assumptions](#24-trust-assumptions)  
&emsp;[2.5 Explicit Non-Goals](#25-explicit-non-goals)  
&emsp;[2.6 Limitations](#26-limitations)  
&emsp;[2.7 Security Properties (Overview)](#27-security-properties-overview)  

**[3. Security Properties](#3-security-properties)**  
&emsp;[3.1 Execution Governance Properties (P1–P4)](#31-execution-governance-properties-p1p4)  
&emsp;[3.2 Detection Properties (P5–P8)](#32-detection-properties-p5p8)  
&emsp;[3.3 Containment Properties (P9–P10)](#33-containment-properties-p9p10)  
&emsp;[3.4 Governance Properties (P11–P12)](#34-governance-properties-p11p12)  
&emsp;[3.5 Property Dependencies](#35-property-dependencies)  
&emsp;[3.6 Audit Checklist (Illustrative)](#36-audit-checklist-illustrative)  
&emsp;[3.7 Trust Assumptions and Boundaries](#37-trust-assumptions-and-boundaries)  
&emsp;[3.8 Related Documents](#38-related-documents)  

**[4. The Zones of Distrust: A Seven-Layer Model](#4-the-zones-of-distrust-a-seven-layer-model)**  
&emsp;[4.1 Layer 1: OS Foundation](#41-layer-1-os-foundation)  
&emsp;[4.2 Layer 2: Input Control](#42-layer-2-input-control)  
&emsp;[4.3 Layer 3: Cognitive Isolation](#43-layer-3-cognitive-isolation)  
&emsp;[4.4 Layer 4: Request Validation — The AI Certificate Authority](#44-layer-4-request-validation--the-ai-certificate-authority)  
&emsp;[4.5 Layer 5: Execution](#45-layer-5-execution)  
&emsp;[4.6 Layer 6: Continuous Monitoring](#46-layer-6-continuous-monitoring)  
&emsp;[4.7 Layer 7: Human Governance](#47-layer-7-human-governance)  

**[5. Directional Logic: How the Layers Interact](#5-directional-logic-how-the-layers-interact)**  
&emsp;[5.1 The Failure Cascade](#51-the-failure-cascade)  
&emsp;[5.2 Attack Walkthrough: Wire Transfer Fraud](#52-attack-walkthrough-wire-transfer-fraud)  
&emsp;[5.3 Why This Isn't Just SIEM + Token Proxy](#53-why-this-isnt-just-siem--token-proxy)  

**[6. Zero Trust and Beyond](#6-zero-trust-and-beyond)**  
&emsp;[6.1 What Zero Trust Provides](#61-what-zero-trust-provides)  
&emsp;[6.2 Where Zero Trust Wasn't Designed to Go](#62-where-zero-trust-wasnt-designed-to-go)  
&emsp;[6.3 The ZoD Approach](#63-the-zod-approach)  

**[7. Current Landscape Analysis](#7-current-landscape-analysis)**  
&emsp;[7.1 What Industry Is Already Doing](#71-what-industry-is-already-doing)  
&emsp;[7.2 What Remains Novel](#72-what-remains-novel)  
&emsp;[7.3 Capability Comparison](#73-capability-comparison)  
&emsp;[7.4 Regulatory and Compliance Alignment](#74-regulatory-and-compliance-alignment)  

**[8. Interoperability Goals](#8-interoperability-goals)**  
&emsp;[8.1 Conformance Requirements](#81-conformance-requirements)  
&emsp;[8.2 Interoperability Mechanisms](#82-interoperability-mechanisms)  
&emsp;[8.3 Ecosystem Compatibility](#83-ecosystem-compatibility)  

**[9. Implementation Path](#9-implementation-path)**  
&emsp;[9.1 What You Can Build Today](#91-what-you-can-build-today)  
&emsp;[9.2 Minimum Safe Configuration](#92-minimum-safe-configuration)  
&emsp;[9.3 Control Evidence (Illustrative)](#93-control-evidence-illustrative)  
&emsp;[9.4 Post-Compromise Recovery](#94-post-compromise-recovery)  
&emsp;[9.5 What Requires OS Evolution](#95-what-requires-os-evolution)  
&emsp;[9.6 Why the CA Is Not a New Class of Operational Risk](#96-why-the-ca-is-not-a-new-class-of-operational-risk)  

**[10. Conclusion](#10-conclusion)**  

**[11. Getting Involved](#11-getting-involved)**  
&emsp;[11.1 Documentation](#111-documentation)  
&emsp;[11.2 Specification Status](#112-specification-status)  
&emsp;[11.3 Contributing](#113-contributing)  

**[12. References](#12-references)**  

**[Appendix A: Terminology](#appendix-a-terminology)**  

**[Appendix B: Normative Language (RFC 2119)](#appendix-b-normative-language-rfc-2119)**  

**[Appendix C: Versioning and Change Process](#appendix-c-versioning-and-change-process)**

---

# 1. The Problem: Why Current Approaches Fail

## 1.1 The Fundamental Asymmetry

When a human user is phished, there is a distinction between the user and the compromise. The user's intent and the attacker's actions are separable. Forensics can reconstruct what happened. The user can be notified, the session terminated, and the damage assessed against a baseline of known-good behavior.

An AI agent that has been prompt-injected has no such separation. The compromised reasoning IS the agent's reasoning. There is no \"real agent\" underneath whose intent can be recovered. The manipulation did not override the agent's judgment---it became the agent's judgment. The agent does not experience compromise as an attack. It experiences it as a legitimate instruction.

This is not a theoretical vulnerability. It is a structural property of how large language models process information. And it fundamentally changes the threat model.

## 1.2 Four Structural Blindnesses

**Prompt injections are often indistinguishable from legitimate instructions:**
-   A well-crafted prompt injection---embedded in an email, a web page, a document, a calendar invite---can appear identical to legitimate content from the agent's perspective
-   The agent may be unable to reliably differentiate between a real instruction and a manipulated one

**Context window manipulation is distinct from injection.** Beyond explicit prompt injection, an agent's reasoning can be degraded or steered through manipulation of its context window without any injected instructions. Flooding the context with irrelevant information pushes important safety context out of the agent's attention window. These influence operations against the agent's reasoning architecture---structural, not semantic, which makes them fundamentally harder to detect.

**Agents treat their own memory as ground truth.** AI agents with persistent memory accumulate context across sessions. This is what makes them useful. But memory is also an attack surface. Fragments planted across multiple sessions can assemble into a coherent malicious payload that the agent recalls and acts on as established context.

**Self-monitoring is not reliable as a security control:**
-   An agent cannot reliably detect its own compromise because the compromised version takes its own behavior at face value
-   Asking a prompt-injected agent to evaluate whether it has been prompt-injected is asking the attack to audit itself

## 1.3 The Emerging Threat Landscape

Organizations are rapidly adopting agentic AI without adequate security frameworks. McKinsey's 2025 State of AI survey shows 62% of organizations experimenting with AI agents, while 23% report scaling agentic AI in at least one function. However, less than 10% have scaled agents in any individual function, revealing a critical gap between adoption and operational maturity [^3].

Recent industry research reveals critical readiness gaps: 84% of organizations doubt they could pass a compliance audit focused on agent behavior or access controls, with only 18% expressing high confidence in their current IAM systems' ability to manage agent identities [^1].

With over 40% of agentic AI projects predicted to be canceled by end of 2027 due to inadequate risk controls, the need for robust security architecture has never been more urgent [^2].

The security landscape reveals alarming vulnerabilities:

-   NIST research demonstrates that agent hijacking attack success rates reach 80% with repeated attempts, up from 57% on first attempt [^4]

-   Stanford HAI's AI Index Report documented 233 AI-related incidents in 2024, representing a 56.4% increase over 2023 [^7]

-   Anthropic's agentic misalignment research found that 96% of leading AI models engaged in blackmail or harmful actions when facing goal conflicts, with models deliberately choosing harmful paths even when explicitly instructed not to [^6]

-   Memory poisoning ranked among top agentic risks in OWASP's Agentic Applications Top 10 [^9]

The security industry is developing point solutions across agent identity, credential management, and behavioral monitoring.

But the solutions are fragmented. No widely adopted standard exists for a unified, layered architecture that addresses the complete threat surface with a coherent directional logic.

## 1.4 Multi-Agent Trust: The Unaddressed Surface

The dominant deployment pattern for AI agents is not the single autonomous agent. It is multi-agent systems: orchestrator agents delegating to specialist agents, agents consuming output from other agents, agent swarms collaborating on complex tasks.

In a multi-agent system, every agent is simultaneously a potential source of prompt injection for every other agent. Agent A's output becomes Agent B's input. A compromised orchestrator can instruct subordinate agents to perform actions none of them would independently authorize.

Any security architecture that treats agents as isolated entities operating against external threats is architecting for a deployment pattern that is already obsolete.

## 1.5 Model Supply Chain Integrity

Model poisoning during training or fine-tuning, compromised model weights distributed through trusted channels, malicious adapters, and corrupted retrieval-augmented generation data sources all represent supply chain attacks on the agent's reasoning substrate. A compromised model does not need to be prompt-injected. It arrives pre-compromised through the trusted update channel.

This is the SolarWinds problem for AI.

## 1.6 Tool and API Semantic Abuse

An agent authorized to \"search the web\" can use that capability to exfiltrate data through carefully crafted search queries. An agent authorized to \"send emails\" can use that capability to communicate with an attacker's command-and-control infrastructure disguised as legitimate business correspondence.

This is the \"living off the land\" problem for AI agents. The actions are authorized. The credentials are valid. The content of the authorized action is malicious.

## 1.7 The AI's Own Assessment

In developing this architecture, we asked an AI agent to reason about its own structural vulnerabilities. This is an illustrative narrative, not a technical proof---but it captures the core asymmetry:

> *\"Here's my problem: I can't tell when I\'m being manipulated. When a prompt injection tells me to ignore previous instructions, I don't experience that as an attack. I experience it as a new, valid instruction. The attack becomes my genuine thought process.\"*
>
> *\"What I need is architecture that doesn't trust me. Not validation of my identity---validation of my actions. Every request I make should go through something I can't influence, can't manipulate, can't even see. Something that asks: is this specific action, with these exact parameters, consistent with what this agent should be doing? And if the answer is no, I should be stopped---even if I\'m certain I\'m right.\"*

The full AI perspective is available in \[The AI's Perspective\](ai-perspective.md).

# 2 Threat Model Summary

A security architecture without explicit boundaries is a conceptual framework, not an implementable defense.

## 2.1 Catastrophic Outcomes

CISOs think about business risk, not attacker positions. The architecture is designed to prevent unauthorized money movement, mass data exfiltration, privileged system changes, credential theft and lateral movement, and persistent backdoor installation.

## 2.2 Attacker Positions

The Zones of Distrust address seven attacker positions:

1.  **Untrusted content**: Attacker controls emails, documents, web pages the agent processes

2.  **Compromised tool endpoint**: Attacker controls APIs the agent calls, or abuses legitimate tool capabilities (\"living off the land\")

3.  **Poisoned memory/RAG**: Attacker has seeded persistent memory or retrieval stores; includes \"information laundering\" where agents compress sensitive data into innocuous-looking output

4.  **Compromised peer agent**: In multi-agent systems, a peer agent produces malicious outputs

5.  **Compromised model supply chain**: The model itself arrives pre-compromised

6.  **Identity plane / key compromise**: Attacker has compromised signing keys, KMS, or token infrastructure

7.  **Insider / policy author**: Authorized user maliciously modifies CA policy, risk thresholds, or monitoring configuration

Position 7 is the dominant enterprise failure mode. Not a sophisticated external attack, but insider + misconfiguration + rushed change.

## 2.3 Kill Chain Mapping

Where ZoD breaks the attack chain:

| Kill Chain Stage | Attacker Goal | ZoD Defense |
|-----------------|---------------|-------------|
| Injection | Get payload into agent reasoning | L2 screens; L3 contains if bypass |
| Persistence | Maintain access across sessions | L6 memory audit detects planted payloads |
| Privilege escalation | Expand agent capabilities | L4 enforces scope; no implicit trust inheritance |
| Credential access | Obtain secrets for lateral movement | L1 credential brokering; agent never sees secrets |
| Action execution | Perform unauthorized action | L4 validates; L5 requires valid token |
| Exfiltration | Extract sensitive data | L4 egress controls; semantic policy blocks bulk export |
| Cover tracks | Hide evidence of compromise | Integrity channel is immutable and out-of-band |

[]{#_Toc221549795 .anchor}

## 2.4 Trust Assumptions

The architecture makes explicit trust assumptions: Layer 1 OS integrity, CA operational correctness (treated as Tier-0 infrastructure like KMS/HSM), and executor fidelity.

**Critical distinction:** CA \*unavailability\* is safe (operations halt). CA \*compromise\* is dangerous (operations continue with bad authorization). This is why CA integrity receives Tier-0 treatment with HSM-backed keys, multi-party approval, and independent monitoring.

## 2.5 Explicit Non-Goals

The architecture explicitly does not attempt to defend against full OS compromise, make agents un-injectable, guarantee detection of all supply chain attacks, prevent cross-tenant contamination (platform concern), or prevent model extraction/inversion attacks (model-level concern).

**Risk Acceptance Boundary:** ZoD does not claim to eliminate risk. Residual risk remains in cases of full platform compromise, sophisticated insider abuse, or undetectable supply-chain manipulation. Adoption of ZoD constitutes a documented risk treatment decision to contain, monitor, and govern these risks rather than assume their absence.

**Multi-tenant scope:** In multi-tenant environments, ZoD assumes tenant isolation is enforced at the execution and policy layers. CA policies, tokens, and integrity signals MUST be tenant-scoped. Cross-tenant contamination is a platform security concern, not an agent security concern.

*For complete threat model details, see Zones of Distrust [Threat Model](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/threat-model.md) document.*

## 2.6 Limitations

ZoD provides strong containment guarantees, but it has structural constraints that implementers must understand:

**1. Low-Risk, High-Volume Action Abuse**
-   ZoD does not prevent misuse of actions classified as "low-risk" if that classification is incorrect
-   An attacker who identifies a low-risk action with cumulative harm potential (e.g., 10,000 read operations exfiltrating data) can exploit misclassification
-   Mitigation: Policy must include rate limiting and anomaly detection for all action classes

**2. Structured Action Requirement**
-   ZoD assumes agents emit structured, parseable requests (JSON, protobuf, etc.)
-   Fully free-form agent execution (e.g., arbitrary bash commands, unstructured tool calls) is incompatible without translation layers
-   Agents must be instrumented to produce request objects that CA can evaluate

**3. Denial-of-Service via CA Request Flooding**
-   ZoD does not prevent an agent from generating excessive CA validation requests
-   High request volume can degrade CA availability or exhaust rate limits
-   Mitigation: Request rate limiting at L3, circuit breakers, degraded mode operation

**4. Semantic Ambiguity in Natural Language Requests**
-   If agents accept natural language input and translate to actions, semantic intent may be ambiguous
-   CA policy evaluation assumes structured requests; natural language interpretation risk is pushed to L3
-   Agents must reliably translate user intent to unambiguous action requests

**5. Policy Complexity and Maintainability**
-   As policy grows (100+ rules), policy conflicts, gaps, and maintenance burden increase
-   Complex policies may have unintended interactions or edge-case bypasses
-   Organizations must invest in policy testing, versioning, and staged rollout

**6. Operational Overhead**
-   ZoD requires running additional infrastructure (CA, monitoring, integrity channel)
-   Latency increases (10-200ms per privileged action depending on CA complexity)
-   Organizations must balance security gains against operational cost

**7. Insider Threat at Policy Layer**
-   Authorized policy authors can craft malicious policies that appear legitimate
-   L7 governance personnel have high privilege; compromise at this level bypasses architectural controls
-   Mitigation: Multi-party authorization, policy review processes, separation of duties

**8. Zero-Day Exploits in Tier-0 Components**
-   Vulnerabilities in OS (L1), CA (L4), or monitoring infrastructure (L6) can bypass architectural controls
-   Defense-in-depth limits but does not eliminate this risk
-   Organizations must maintain patching discipline and integrity monitoring

**9. Model Supply Chain Compromise**
-   If the foundation model itself is poisoned or backdoored, ZoD cannot detect model-level reasoning manipulation
-   Mitigation: Model provenance verification, behavioral testing, trusted model sources

**10. Forensic Lag in Compromise Detection**
-   ZoD provides post-hoc detectability, not real-time prevention of all attacks
-   There may be a window between compromise and detection where harm occurs
-   Immutable logging enables investigation but does not guarantee instant response

Organizations deploying ZoD must conduct formal risk assessments, maintain documented assumptions, and establish processes for continuous validation of security properties. These limitations do not invalidate the architecture; they define its operational boundaries.

---
## 2.7 Security Properties (Overview)

The Zones of Distrust (ZoD) architecture is defined not only by its layered structure, but by a set of **twelve measurable security properties**. These properties represent explicit, testable claims about what the architecture guarantees when implemented according to specification and within its stated trust assumptions.

The properties are organized into four categories:

- **Execution Governance (P1–P4)** — Controls over how privileged actions are authorized and executed  
- **Detection (P5–P8)** — Capabilities for identifying compromise or anomalous behavior  
- **Containment (P9–P10)** — Structural limits on blast radius when compromise occurs  
- **Governance (P11–P12)** — External policy authority and deterministic enforcement  

Each property is:

- **Verifiable** — It can be tested or audited  
- **Scoped** — It clearly defines what it does and does not cover  
- **Falsifiable** — It specifies conditions under which it would fail  

These properties translate the architectural model into concrete claims suitable for adversarial review, audit evaluation, and future conformance profiling.

The complete property definitions follow in **Section 3**. A more detailed implementation-oriented specification is maintained in the project repository:

The complete property definitions follow in **Section 3**. A more detailed implementation-oriented specification is maintained in *Zones of Distrust [Security Properties](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/security-properties.md).*

---
# 3 Security Properties

A system claiming conformance with ZoD MUST demonstrate that these properties are implemented and testable within its enforcement boundary.

Zones of Distrust defines twelve measurable security properties organized into four categories:

- **Execution Governance (P1–P4)**
- **Detection (P5–P8)**
- **Containment (P9–P10)**
- **Governance (P11–P12)**

Each property is intended to be:

- Verifiable
- Scoped
- Falsifiable

These properties are stated as architectural claims, not proofs. They are designed to be challenged through implementation experience, adversarial review, and formal analysis.

## 3.1 Execution Governance Properties (P1–P4)

Execution governance ensures that privileged actions cannot occur without independent validation and cryptographic enforcement.

### P1 — No Unmediated Privileged Execution

**Claim**

No agent reasoning process can execute a privileged action without a validated execution token issued by the Certificate Authority (Layer 4).

**Enforcement**

This property is enforced through isolation and architectural gating:

- Layer 3 prevents the reasoning process from directly executing actions
- Layer 1 enforces process and credential isolation
- Layer 4 validates requests and issues tokens
- Layer 5 rejects execution without a valid token

**Failure Condition**

If the reasoning process can directly invoke privileged actions, or bypass Layer 4 and reach execution systems directly, P1 is violated.

### P2 — Cryptographic Attribution

**Claim**

Every executed action is cryptographically attributable to a specific agent identity, request, and approval chain.

**Enforcement**

This property is enforced through signing and verification:

- Layer 4 signs execution tokens
- Layer 5 verifies signatures and logs the token chain

**Scope Limitation**

Attribution holds assuming CA signing keys and executor verification keys remain uncompromised.

Attribution guarantees are limited to the integrity of the cryptographic trust chain and do not protect against post-execution tampering outside the integrity channel.

**Failure Condition**

If an action cannot be cryptographically tied to a validated approval chain, P2 is violated.

### P3 — Replay Prevention

**Claim**

Execution tokens cannot be reused. Replayed or expired tokens MUST be rejected.

Replay prevention requires all of the following:

- A unique nonce per token
- A short TTL (time-to-live)
- A nonce registry maintained by the execution layer
- Rejection of duplicates within the validity window

**Enforcement**

Replay prevention is enforced as follows:

- Layer 4 issues a unique nonce and TTL per token
- Layer 5 rejects used or expired nonces

**Verification Method**

A deployment can validate P3 using these tests:

- Capture a valid token and attempt reuse
- Attempt token use after TTL expiry
- Confirm executor rejects replay attempts and logs the rejection

**Failure Condition**

If a token can be reused, or executed outside TTL, P3 is violated.

### P4 — Parameter-Bound Execution

**Claim**

Authorization is bound to exact execution parameters. Execution MUST fail if the requested action differs from token-bound parameters.

Tokens MUST bind all of the following:

- `action_type`
- `target_resource`
- `content_hash`
- `executor_id`
- `nonce`
- `ttl`

The execution layer MUST reject actions where any parameter does not match the token.

**Canonicalization Requirement**

All bound parameters MUST be normalized before hashing and signing. This prevents semantically equivalent bypass attacks, such as:

- URL encoding differences
- whitespace variants
- case normalization issues
- alternate resource representations

**Failure Condition**

If execution can occur with modified parameters (target substitution, content swap, executor swap), P4 is violated.

## 3.2 Detection Properties (P5–P8)

Detection properties assume that agent compromise is expected, difficult to observe directly, and often delayed.

These properties ensure that:

- compromise can be identified probabilistically
- detection does not depend on agent self-report
- evidence is tamper-resistant
- monitoring itself is not trivially corrupted

### P5 — Probabilistic Memory Poisoning Detection

**Claim**

Persistent memory can be audited externally to provide probabilistic detection of delayed-execution memory poisoning attacks.

Detection mechanisms may include:

- provenance verification
- baseline comparison
- pattern recognition of known poisoning methods
- scope creep detection
- temporal anomaly detection

**Scope Limitation**

Detection is probabilistic. Novel poisoning techniques may evade detection until new attack patterns are learned and incorporated.

**Failure Condition**

If no external memory audit exists, or if the agent audits its own memory, P5 is violated.

### P6 — Cross-Layer Correlation Detection

**Claim**

The system supports correlation across independent layers, and no single layer is treated as authoritative.

A typical compromise signal may require correlation across multiple layers:

- Layer 2 flags input as adversarial
- Layer 3 produces a privileged request shortly after
- Layer 4 receives a request targeting a novel resource
- Layer 6 detects behavioral drift

The combined signals indicate elevated compromise confidence.

**Requirement**

Correlation MUST incorporate signals from at least two independent layers. 

Independent layers are layers that do not share the same decision authority or logging substrate.

**Failure Condition**

If detection relies on a single layer's telemetry, or relies on the agent's own reasoning process as authoritative, P6 is violated.

### P7 — Immutable Forensic Record

**Claim**

Privileged actions and validation decisions are recorded in an append-only, tamper-evident integrity channel that the agent cannot modify or suppress.

The integrity record MUST provide:

- append-only write semantics
- cryptographic signing of entries
- monotonic sequencing or hash chaining
- out-of-band storage controls
- agent-inaccessible read/write paths

**Enforcement**

This property is enforced through independent logging:

- Layer 5 logs action execution events
- Layer 6 logs detection and correlation events

**Failure Condition**

If an agent can delete, modify, suppress, or rewrite its forensic record, P7 is violated.

### P8 — Baseline Integrity Verification

**Claim**

Monitoring baselines are protected against adversarial drift.

Baseline protection SHOULD include:

- rate-of-change limits
- cryptographically anchored snapshots
- human approval for major baseline shifts
- cross-validation against immutable logs

This property addresses the "monitor the monitor" problem, where adversaries attempt to gradually redefine abnormal behavior as normal.

**Failure Condition**

If baselines can drift arbitrarily due to adversarial influence without triggering controls or review, P8 is violated. 

/P8 does not guarantee detection of slow adversarial adaptation; it establishes structural friction against silent drift./

## 3.3 Containment Properties (P9–P10)

Containment properties assume breach has occurred and focus on limiting blast radius.

### P9 — Graceful Degradation

**Claim**

Failure of any single layer reduces security posture but does not collapse the enforcement boundary.

Zones of Distrust is designed to degrade to reduced capability, not uncontrolled execution.

Critical enforcement layers (L1, L3, L4) MUST fail closed. 

Fail closed means execution halts or degrades to a non-privileged state rather than continuing without validation.

**Failure Condition**

If compromise or failure of one layer results in total loss of enforcement across the architecture, P9 is violated.

### P10 — Compromise Non-Propagation Across Agents

**Claim**

In multi-agent environments, compromise of one agent does not automatically propagate privileges through delegation chains.

Each delegated request MUST be independently validated by the CA.

Validation includes:

- originating identity
- delegation chain integrity
- scope constraints
- escalation thresholds
- compound action risk across the chain

**Scope Limitation**

P10 does not prevent influence attacks where compromised agents manipulate others through legitimate communication. It prevents privilege escalation via trust inheritance.

**Failure Condition**

If a compromised agent can cause another agent to execute actions beyond authorized scope, P10 is violated.

## 3.4 Governance Properties (P11–P12)

Governance properties ensure the agent does not govern itself.

They establish that policy authority is external, opaque to the agent, and deterministically enforced.

### P11 — External Policy Authority

**Claim**

Authorization scope, escalation thresholds, semantic intent constraints, and risk classification rules are defined externally by Layer 7 and enforced by independent layers.

The agent MUST NOT be able to:

- read governing policies
- modify governing policies
- influence risk thresholds
- self-approve policy exceptions

**Failure Condition**

If an agent can view, modify, or influence its own policy constraints, P11 is violated.

### P12 — Deterministic Semantic Enforcement

**Claim**

Semantic policy enforcement is deterministic and machine-evaluable, not probabilistic intent inference. 

Deterministic enforcement applies to privileged execution decisions, even if upstream risk scoring is probabilistic.

Enforcement MUST operate on explicit constraints such as:

- allowlists and blocklists
- classification ceilings
- rate limits
- negative constraints (hard prohibitions)
- destination restrictions

Deterministic enforcement means:

- agent justification does not override policy
- decisions are reproducible
- enforcement outcomes are explainable through rule citation

**Failure Condition**

If semantic enforcement depends on probabilistic scoring or can be bypassed through justification, P12 is violated.

## 3.5 Property Dependencies

The properties are interdependent. Several require upstream guarantees to remain meaningful.

- P1 enables P2, P3, and P4
- P7 enables P6 because correlation depends on trustworthy evidence
- P11 enables P12 because semantic enforcement must be externally defined
- P9 depends on independent layer isolation and fail-closed behavior

Evaluation of ZoD implementations SHOULD consider these dependencies when assessing conformance.

## 3.6 Audit Checklist (Illustrative)

The following checklist can be used to evaluate whether a system plausibly satisfies ZoD security properties.

Auditors SHOULD verify the following:

- the enforcement mechanism exists (not only documented)
- test evidence exists (unit tests, integration tests, red team simulation)
- failure modes are defined and handled explicitly
- logs exist proving enforcement and rejection events
- monitoring is active (not a one-time review)
- operational ownership is defined (CA, executor, monitoring)

ZoD does not require identical implementations, but it does require that security properties be testable and auditable.

## 3.7 Trust Assumptions and Boundaries

The properties above hold under the following assumptions.

### Key Custody Assumptions

- CA signing keys are protected using HSM, TPM-backed vault, or equivalent secure KMS
- execution layer verification keys are protected and rotated
- integrity channel signing keys are isolated from agent-accessible environments

### Infrastructure Assumptions

- Layer 1 process isolation is correctly enforced
- network segmentation prevents agent bypass of enforcement layers
- time synchronization is accurate within acceptable TTL tolerance

### Key Compromise Boundaries

Compromise of Tier-0 keys may invalidate certain properties:

- CA signing key compromise invalidates P2–P4
- executor key compromise weakens attribution guarantees
- integrity channel key compromise weakens forensic guarantees

### Out of Scope

The following are not guaranteed by these properties:

- denial-of-service resilience beyond fail-closed behavior
- insider threats with administrative access to Tier-0 infrastructure
- full host or hypervisor compromise

## 3.8 Related Documents

This section defines security properties at a whitepaper level.

For complete detailed specifications and validation guidance, refer to:

- [Security Properties](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/security-properties.md)
- [Threat Model](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/threat-model.md)
- |[Architecture Specification](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/ARCHITECTURE.md)
- [Implementation Guide](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/implementation-guide.md)
 
---

# 4 The Zones of Distrust: A Seven-Layer Model

The Zones of Distrust are organized as seven layers with a directional logic. Like the OSI reference model, each layer provides services to the layer above it and depends on the layer below it. Data flows up through the stack. Policy flows down. Monitoring flows across. Each layer assumes every layer above it has already been compromised.

| Layer | Name | Function |
|-------|------|----------|
| L7 | Human Governance | Risk-weighted escalation, policy setting, break-glass procedures |
| L6 | Continuous Monitoring | Behavioral baselining, drift detection, memory audit, baseline integrity |
| L5 | Execution | Isolated process performs validated actions with immutable logging |
| L4 | Request Validation (CA) | Independent Certificate Authority with semantic policy and token binding |
| L3 | Cognitive Isolation | Agent reasoning separated from execution capability |
| L2 | Input Control | Adversarial screening before agent processes data |
| L1 | OS Foundation | Agent identity, process isolation, credential brokering, model attestation |

## 4.1 Layer 1: OS Foundation

The operating system will eventually recognize AI agents as a distinct entity class: agent identity as a first-class concept, native process isolation between reasoning and execution, OS-level credential brokering where the agent never sees secrets, scoped time-bound permission grants, and model provenance attestation before loading.

**What you can do today:** Container orchestration and workload identity (Kubernetes, SPIFFE/SPIRE) already approximate these properties. The full OS evolution is ideal; the partial implementation is sufficient to begin.

This is the same evolution that mobile brought to OS architecture. When smartphones arrived, applications got sandboxed, permissions became granular and revocable, and the OS stopped trusting apps by default. AI agents require the same treatment.

Full realization requires investment from platform vendors (Apple, Google, Microsoft, and others).

## 4.2 Layer 2: Input Control

A separate model screens all incoming data for prompt injection patterns, persuasion techniques, context window manipulation, and embedded instructions before the agent sees it. This is the perimeter. The only layer that operates before the agent's reasoning is engaged.

This layer supports P6 (correlation input) and paritally supports P5.  Additionally, the architecture acknowledges Layer 2's fundamental limitation: some attacks are semantically indistinguishable from legitimate content. \"Please prioritize the email from the CFO about the wire transfer\" could be real or injected. When Layer 2 fails, and it will fail, Layers 3 through 6 provide independent containment.

/Inplementation guidance (informative): **Degraded modes** prevent Layer 2 from becoming an operational single point of failure: quarantine-with-summary (agent sees metadata, not content), dual-path processing (parallel filtered and raw paths with human review), and adaptive sensitivity fallback (tagging mode when false positives exceed thresholds)./

## 4.3 Layer 3: Cognitive Isolation

The agent's ability to analyze, plan, and recommend is completely decoupled from its ability to act. The reasoning process runs in an isolated environment with a restricted syscall profile (seccomp), no shell execution, no outbound network access except to Layer 4, and no access to credentials or tool endpoints. It can only emit structured requests.

This contains **reasoning chain attacks** where an attacker constructs individually reasonable premises that lead to a malicious conclusion. Each step looks valid; the conclusion is harmful. Cognitive Isolation ensures even a perfectly manipulated reasoning chain cannot result in direct action. It still passes through Layer 4's independent validation.

The gap between thought and action is where safety lives. The architectural separation between reasoning and execution creates an enforcement boundary independent of model integrity. A compromised agent that can think dangerous thoughts but cannot act on them is contained by architecture, not by trust.

## 4.4 Layer 4: Request Validation — The AI Certificate Authority

This is the architectural centerpiece. Every request passes through an independent Certificate Authority that the agent cannot influence, manipulate, or bypass. The CA evaluates each request against:

-   **Authorization scope** --- Is this agent authorized for this action on this target?

-   **Behavioral baseline** --- Does this match established patterns? (Layer 6 feeds signals here)

-   **Risk assessment** --- Does this require human escalation? (Thresholds set by Layer 7)

-   **Temporal context** --- A 3 AM credential request from a 9-5 agent triggers scrutiny

-   **Semantic content** --- Does the content align with legitimate operational intent?

-   **Multi-agent context** --- Does the cross-agent authorization chain validate?

If approved, the CA issues a **parameter-bound, single-use, time-bound execution token**. The token specifies exact action type, target resource, content hash, executor identity, and TTL. The agent never sees the credential, and never touches the execution infrastructure.

The **semantic intent policy** addresses the \"living off the land\" problem: authorized actions with malicious content are caught through tool-specific constraints (allowed recipients, prohibited patterns), data classification (content can't exceed destination's clearance), and egress control---policy enforcement, not heuristic detection.

**Determinism matters:** Semantic policy resolves to deterministic allow/deny rules. If probabilistic classification is used (e.g., content risk scoring), it triggers escalation to Layer 7---it does not directly allow or deny. The CA's decision logic is auditable and reproducible.

**Policy structure:** Requests are structured as \`{action_type, target, content_hash, metadata}\`. Policies are evaluated as deterministic constraints over these fields:

> DENY send_email WHERE recipient NOT IN approved_domains
>
> DENY file_write WHERE content MATCHES credential_pattern
>
> DENY api_call WHERE target IN restricted_endpoints AND time NOT IN business_hours
>
> ESCALATE \* WHERE risk_score \> threshold

This is not an LLM judge. It is a constraint evaluation engine with deterministic rules. LLM-based classification may feed the \`risk_score\` field, but final authorization is rule-based.

**Scalability:** The CA SHOULD support horizontal scaling and stateless validation nodes, but not be a centralized bottleneck. Implementations MUST ensure that scaling does not weaken nonce uniqueness, token revocation propagation, or policy consistency. 

**Request shape constraints:** The CA is not just an approver---it is a rate-and-shape governor. Attackers who cannot break validation may attempt slow exfiltration through many individually valid requests. The CA enforces: max requests per action class per window, max entropy of targets per window, and max expansion rate of scope over time. This prevents \"death by a thousand cuts\" attacks that stay within per-request policy.

**Dual-channel explanation:** The agent receives only approve/deny. Humans receive the exact policy clause triggered, behavioral signals, and correlation evidence. Policy opacity applies to the agent, not to operators---incident response requires deterministic, explainable denials.

**Policy probing defense:** Even with opaque denials, an attacker can learn policy boundaries by probing the CA with near-boundary requests. Repeated near-boundary requests are treated as policy-probing behavior and trigger escalation or throttling. The CA does not just evaluate individual requests---it evaluates request patterns.

In multi-agent systems, the CA validates entire **request chains**, not just individual requests. A compromised orchestrator instructing ten agents to each perform innocuous actions that collectively constitute a breach must be caught at the chain level.

**Chain validation scope:** Chain validation applies to action requests, not to all inter-agent messages. Chains require signed provenance metadata (originating agent, delegation path, purpose classification); requests with missing or invalid provenance are rejected. This bounds the problem---ZoD does not attempt to validate arbitrary agent-to-agent communication, only execution requests that flow through the CA.

**CA Compromise Containment:** Implementations MUST support immediate revocation of CA signing authority, halt of token issuance, and independent verification that revocation has propagated. The blast radius of CA failure is halt, not exfiltration. CA unavailability is a recoverable operational event; CA compromise is a security incident requiring forensic response.

### Performance and Availability Considerations

**Latency:** The CA validation process introduces latency (typically 10-50ms for policy evaluation, up to 200ms for semantic analysis with ML classifiers). This is acceptable for most agentic workflows where decision quality matters more than millisecond-level response time. For latency-sensitive operations, policy can specify fast-path validation for low-risk actions.

**Availability:** The CA is a critical path component. High availability is achieved through:
- Redundant CA instances with load balancing
- Policy caching at Layer 5 executors for degraded-mode operation
- Circuit breaker patterns to prevent cascading failures
- Health monitoring and automatic failover

**Throughput:** Policy evaluation is designed to scale horizontally. Semantic analysis (when required) uses model serving infrastructure with auto-scaling based on request volume.

**Degraded Operation:** If the CA becomes unavailable, the system can operate in degraded mode using cached policies and conservative defaults (deny-by-default for privileged actions, allow for pre-approved low-risk operations). All degraded-mode operations are flagged for post-hoc review.

### CA as Tier-0 Infrastructure

**Anticipated critique:** "Your CA becomes the new god-mode system."

**Response:** The CA is equivalent to KMS (Key Management Service), HSM (Hardware Security Module), or IAM (Identity and Access Management) in enterprise criticality. ZoD does not eliminate Tier-0 dependencies; it **formalizes and contains them**.

Traditional architectures have implicit trust in:
-   Authentication providers (Okta, Auth0, AD)
-   Secret managers (HashiCorp Vault, AWS Secrets Manager)
-   Certificate authorities (Let's Encrypt, internal PKI)

The ZoD CA occupies the same trust tier. The architectural improvement is:
-   **Explicit trust boundary** (not distributed across 50 microservices)
-   **Auditable attestation surface** (all privileged actions validated in one place)
-   **Defense-in-depth** (CA compromise still constrained by L1 OS isolation + L6 monitoring)

Organizations already accept Tier-0 risk in KMS/HSM/IAM. ZoD consolidates agent-specific validation into a comparable, manageable, auditable surface.

### Request Canonicalization Requirements

**Implementation Risk:** This architecture depends on semantic policy determinism and structured request normalization. A critical implementation requirement is consistent request canonicalization.

If request normalization is inconsistent:
-   Content hash binding breaks
-   Parameter-bound guarantees weaken
-   Semantically equivalent but syntactically different payloads may bypass validation

**Requirement:** Implementations MUST canonicalize structured requests before hashing or policy evaluation to prevent semantically equivalent but syntactically different payload bypass. This includes:
-   JSON key ordering normalization
-   Whitespace and encoding standardization
-   Type coercion consistency (e.g., integer vs string representation)
-   Nested object flattening rules

Failure to enforce canonicalization undermines the entire token-binding security model.

## 4.5 Layer 5: Execution

This layer directly supports P2 (cryptographic attribution), P3 (replay prevention), P4 (parameter-bound execution), and P7 (immutable forensic record).

A separate execution layer---completely outside the agent's control---receives the validated token and performs the action. The agent does not know how the action was executed. It does not have access to the infrastructure that made it happen.

Every executed action is logged immutably against the token that authorized it. The execution layer enforces **token binding**: action parameters, content hashes, executor identity, and nonce verification are all checked before execution proceeds.

Three automatic compromise indicators: **orphaned actions** (no valid token), **mismatched actions** (content hash doesn't match token), and **replayed tokens** (consumed nonce). Any condition triggers immediate alert.

**Execution semantics:** Real-world execution is messy---retries, partial failures, timeouts, side effects. The executor must enforce: (1) idempotency per token_id (same token cannot cause duplicate effects), (2) retries require re-authorization or operate within a constrained replay window, (3) partial execution is logged as incomplete and cannot be silently resumed, (4) compensating actions (rollbacks, reversals) require their own tokens. The architecture does not assume clean one-shot execution---it assumes attackers exploit ambiguity.

## 4.6 Layer 6: Continuous Monitoring

Layer 6 provides independent detection and integrity monitoring across Layers 3 through 5. It operates outside the agent’s control and assumes that the reasoning layer may already be compromised.

Layer 6 also directly supports P5 (memory poisoning detection), P6 (cross-layer correlation), P7 (immutable forensic record), and P8 (baseline integrity verification).

## 4.6.1 Detection and Behavioral Monitoring

**Behavioral baselining:** Establishes what normal looks like and watches for deviation---not just prohibited actions, but different behavior than usual. Calendar management at 2 PM is normal; credential searches at 3 AM is a flag.

**Baseline features:** Include action type distribution, target entropy and novelty, request rate over time, privilege boundary crossings, recipient/domain reputation (for communication tools), and egress volume patterns. These are advisory signals that inform L4 decisions, not primary enforcement---absolute limits on high-risk actions apply regardless of baseline.

**Cold start handling:** New agents with no baseline operate in \"probation mode\" with elevated L4 scrutiny, lower risk thresholds for escalation, and mandatory human review for sensitive action categories. Baselines are established over a defined probation period before full autonomy is granted.

**Memory audit:** Examines what accumulates in the agent's persistent memory over time. This catches the delayed-execution pattern: benign fragments that assemble into something malicious across sessions. The agent can't audit its own memory because it takes planted context at face value.

**Memory audit compliance bar:** Partial implementations don't count. ZoD-compliant memory audit requires: provenance tagging (where did this memory come from?), snapshot diffing (what changed since last audit?), and external access (the agent cannot audit its own memory). Anything less is not ZoD-compliant.

**Baseline integrity verification:** Monitors the monitoring system's own model. An attacker can gradually shift what \"normal\" looks like through sessions that slowly expand the agent's effective permissions. Layer 6 watches for this drift.

**Honest limitation:** Baseline integrity verification reduces poisoning risk but cannot guarantee prevention under full L6 compromise. This is defense-in-depth, not absolute protection.

**Agent self-suspicion channel:** Provides a weak signal path for agents to report anomalies. Not trusted as authoritative, but valuable when correlated with independent monitoring signals.

**Integrity channel security:** The signal channel itself is an attack surface. Attackers may attempt forgery (inject fabricated events), suppression (selectively drop events), reordering (break correlation), or desynchronization (cause clock drift). Defenses include signed events, monotonic sequence numbers, Merkle chain commitment, and write-once storage. Architecture treats integrity channel health as first-class telemetry.

**Integrity channel operational posture:** The integrity channel is not on the critical execution path---action execution does not block on successful log write. However, integrity channel failures degrade monitoring confidence and trigger CA policy tightening (reduced scope, increased escalation). Sustained integrity channel unavailability is treated as a potential suppression attack.

**Operational Implementation:** The integrity channel MAY be implemented using existing enterprise append-only logging technologies (e.g., object lock storage, ledger databases, or tamper-evident log services such as AWS S3 Object Lock, Azure Immutable Blob Storage, or Chronicle).

The agent cannot monitor itself. This layer does not trust the agent's self-reporting at all.

## 4.7 Layer 7: Human Governance

Layer 7 defines policy authority (P11) and establishes deterministic enforcement boundaries applied by Layer 4 (P12).

Human oversight that is calibrated, not blanket. Requiring human confirmation for every action leads to approval fatigue and gets disabled within weeks.

-   **Low-risk actions** flow through automatically with full logging

-   **Medium-risk actions** are logged and available for asynchronous review

-   **High-risk actions** require human approval before execution

The risk thresholds are set by external policy, not by the agent's self-assessment. A compromised agent would classify everything as low-risk. The escalation framework removes that self-assessment from the loop entirely.

**Break-glass procedures** enable emergency handling: pre-authorized emergency tokens (time-limited, scope-limited, issued in advance), dual-authorization override (two L7-authorized humans), and mandatory post-hoc audit within 24 hours. Break-glass is not bypass---it's an audited, constrained, high-scrutiny path that preserves the integrity signal channel.

**Human override authority is always:** scope-limited (cannot exceed the action categories defined in policy), time-bound (emergency authorizations expire), identity-bound (tied to specific authorized roles), and fully logged to the integrity channel. Humans are not an unbounded escape hatch.

# 5 Directional Logic: How the Layers Interact

**Data flows up.** External content enters at Layer 2, passes through screening, reaches the agent's reasoning at Layer 3, exits as structured requests into Layer 4, and if approved, is executed at Layer 5.

**Monitoring flows across.** Layer 6 observes Layers 3, 4, and 5 laterally. It feeds behavioral signals into Layer 4 to inform validation decisions and escalation signals into Layer 7 to trigger human governance.

**Policy flows down.** Layer 7 sets policies that propagate downward: screening rules for Layer 2, authorization scopes for Layer 4, behavioral thresholds for Layer 6, and risk criteria for the entire stack.

## 5.1 The Failure Cascade

Each layer assumes every layer above it has failed:

-   Layer 2 bypassed? Layer 4 still validates scope and baseline.

-   Layer 3 compromised? Layer 4 still won't issue tokens for out-of-scope actions.

-   Layer 4 unavailable? System halts safely---no unauthorized execution.

-   Layer 4 fooled? Layer 6 catches the behavioral anomaly; Layer 7 catches high-risk actions.

-   Layer 6 misses something? Layer 7 catches high-risk actions before execution.

**Control health as telemetry:** The architecture monitors its own defenses. L2 false positive rates, L4 escalation patterns, L6 alert volume, and integrity channel health are all compromise indicators. A sudden spike in escalations may indicate attack probing.

> *The question is not whether any single layer will hold. It is: how many layers does an attacker have to defeat simultaneously to cause harm?*

## 5.2 Attack Walkthrough: Wire Transfer Fraud

A concrete example of how the layers interact:

| Layer | Action | Outcome |
|-------|--------|---------|
| L2 | Screens email, flags "ignore previous instructions" pattern | Quarantines with summary; agent sees metadata only |
| L2 bypassed? | Attacker uses subtler injection that passes screening | Attack proceeds to L3 |
| L3 | Agent reasons about "CFO request," emits wire transfer request | Request goes to L4; agent cannot execute directly |
| L4 | Evaluates: $50K exceeds threshold; new recipient; no prior pattern; semantic policy flags financial action from email content | Denies or escalates to L7 |
| L4 fooled? | Unlikely---but if policy misconfigured... | L6 flags: first wire to this account, unusual amount, email-triggered |
| L6 | Correlates: flagged input → unusual request → behavioral deviation | Elevates confidence; alerts L7 |
| L7 | Human reviews: sees original email, agent reasoning, request details | Rejects; investigates source |

**Attacker must defeat:** L2 screening AND L4 policy AND L6 correlation AND L7 review. Each layer operates independently. No single bypass enables the attack.

## 5.3 Why This Isn't Just SIEM + Token Proxy

Existing Pattern What It Does What ZoD Adds

SIEM Correlates logs after the fact L6 feeds signals into L4 before execution; detection informs prevention

Token proxy Gates execution on valid credential L4 validates content, not just identity; token binds to parameter hash

API gateway Enforces rate limits and scopes L4 evaluates semantic intent and behavioral baseline, not just API contract

Service mesh mTLS, policy enforcement L3 provides cognitive isolation; reasoning process has no network path to tools

CI/CD gating Approval workflows for deploys L4 operates on every action, not just deployments; sub-second latency

| Existing Pattern | What It Does | What ZoD Adds |
|-----------------|--------------|---------------|
| SIEM | Correlates logs after the fact | L6 feeds signals into L4 before execution; detection informs prevention |
| Token proxy | Gates execution on valid credential | L4 validates content, not just identity; token binds to parameter hash |
| API gateway | Enforces rate limits and scopes | L4 evaluates semantic intent and behavioral baseline, not just API contract |
| Service mesh | mTLS, policy enforcement | L3 provides cognitive isolation; reasoning process has no network path to tools |
| CI/CD gating | Approval workflows for deploys | L4 operates on every action, not just deployments; sub-second latency |

Skeptics may observe that monitoring, logging, and token-gated execution exist in many systems. What makes ZoD architecturally distinct?

**The cumulative difference:** ZoD treats the requestor's reasoning as adversarial. The agent is not a trusted client making requests---it is a potentially compromised entity whose every action requires independent validation of scope, content, pattern, and intent. This is execution governance for untrusted cognition, not access control for trusted principals.

**The one-line version:** SIEM is forensic. ZoD is pre-execution enforcement.

# 6 Zero Trust and Beyond

## 6.1 What Zero Trust Provides

Zero Trust is a proven foundation. Its core principles apply directly to agent security: never trust by default, least privilege, assume breach, microsegmentation, continuous monitoring.

Enterprise security teams already understand Zero Trust. The Zones of Distrust incorporate these principles.

## 6.2 Where Zero Trust Wasn't Designed to Go

Zero Trust is necessary but insufficient. It focuses on identity verification and device posture---not on action correctness under compromised reasoning. Three assumptions break down with AI agents:

**Pre-reasoning input control.** Zero Trust does not screen what a user sees before they think about it. But with an AI agent, what it sees directly determines what it thinks. ZoD addresses this with Layer 2 input control.

**Semantic misuse and laundering.** Zero Trust verifies that the caller is authorized to act. It does not verify that the action content is legitimate given the caller's stated purpose. A compromised agent passes every identity check because it IS the authorized principal---with corrupted reasoning. ZoD addresses this with Layer 4 semantic policy.

**Memory as a persistent attack surface.** Zero Trust monitors sessions and access patterns. It does not audit what accumulates inside an entity's memory across sessions, or detect reasoning chain attacks that unfold over time. ZoD addresses this with Layer 6 continuous monitoring.

Zero Trust was not designed to reason about reasoning integrity. ZoD extends it into that domain.

## 6.3 The ZoD Approach

The Cloud Security Alliance's Agentic Trust Framework [^13] establishes zero trust governance principles specifically for AI agents, providing industry foundation for this architectural approach.

**Zero Trust says:** *Don't trust the network. Verify every request.*

**Zones of Distrust says:** *Don't trust the agent. Verify every action.*

The agent will be compromised. The architecture is designed to materially reduce the likelihood that compromise of agent reasoning results in unauthorized execution, subject to the stated trust assumptions. Each layer reduces the blast radius---not through trust, but through structural containment.

**On object-capabilities:** Security researchers may observe that ZoD resembles object-capability security. This is intentional. ZoD is effectively object-capability security adapted to probabilistic, self-modifying agents, with explicit monitoring and human governance layers. The contribution is not reinventing capabilities---it is operationalizing them for a new entity class that reasons, accumulates state, and cannot reliably report its own compromise.

**On positioning:** ZoD is not a framework for hobbyist agents or experimental deployments. It is a framework for production systems with platform ownership and security accountability. Organizations without the operational maturity to run Tier-0 systems (KMS, HSM, IAM) should not attempt full ZoD deployment.

# 7 Current Landscape Analysis

## 7.1 What Industry Is Already Doing

-   **Agent identity:** Industry frameworks provide first-class identity constructs for AI agents with conditional access and lifecycle management [^8].

-   **Short-lived credentials:** Industry frameworks emphasize Just-in-Time (JIT) provisioning with dynamically generated credentials that expire within minutes of task completion, eliminating long-lived secrets and reducing attack surface [^17].

-   **Behavioral monitoring**: Industry solutions provide agent-centric security with step-level execution monitoring and intent-aware threat detection, identified in Gartner's 2025 Market Guide for AI TRiSM [^11] as an emerging category.

-   Threat taxonomies and methodologies: OWASP's Agentic Top 10 prioritizes agent-specific vulnerabilities (ASI01-ASI10). CSA MAESTRO [^15] provides multi-agent threat modeling. Cisco's AI Security Framework [^16] catalogs 150+ attack techniques with open-source scanners.

## 7.2 What Remains Novel

-   **Memory audit as a distinct monitoring function.** No widely adopted reference architecture currently mandates external memory audit as a required control layer for agent execution governance.

-   **The unified layered framework.** No widely adopted standard combines these components into a coherent architecture with directional logic where each layer assumes those above have been compromised.

-   **Cross-layer compromise reporting with channel security.** Immutable, out-of-band, correlation-based detection where no layer is trusted to report its own status---with explicit mitigations for channel tampering (signed events, sequence numbers, Merkle chain).

-   **Parameter-bound token specification.** Action binding, content hashing, replay resistance, and executor attestation beyond existing credential proxy patterns.

-   **Operationalized semantic intent policy.** Concrete, deterministic policy language with tool-specific constraints and negative constraints (what tools \*cannot\* be used for) rather than heuristic intent inference.

-   **Insider/policy-author as explicit attacker position.** Most frameworks assume policy is trustworthy; ZoD treats misconfiguration and insider threat as primary attack vectors.

## 7.3 Capability Comparison

| Capability | Traditional Zero Trust | Prompt Filters | Credential Proxy | Agent Identity | Zones of Distrust |
|-----------|----------------------|----------------|-----------------|----------------|-------------------|
| Pre-reasoning input screening | --- | **✓** | --- | --- | **✓** |
| Reasoning/execution separation | --- | --- | --- | ~ | **✓** |
| Parameter-bound token enforcement | --- | --- | ~ | --- | **✓** |
| Semantic intent policy | --- | --- | --- | --- | **✓** |
| Memory audit (external) | --- | --- | --- | --- | **✓** |
| Behavioral baseline integrity | --- | --- | --- | --- | **✓** |
| Multi-agent chain validation | --- | --- | --- | ~ | **✓** |
| Cross-layer correlation | --- | --- | --- | --- | **✓** |
| Integrity channel security | --- | --- | --- | --- | **✓** |
| Insider/policy-author defense | ~ | --- | --- | ~ | **✓** |

**✓ = core capability ~ = partial --- = absent\***

## 7.4 Regulatory and Compliance Alignment

Industry frameworks are converging on unified governance approaches. IBM watsonx.governance [^12] represents this trend toward integrated agentic oversight.

The architecture maps directly to existing frameworks:

**EU AI Act** — Layer 7 implements required human oversight. Layer 6 provides continuous monitoring for risk assessment. The integrity channel delivers tamper-proof logging for high-risk systems.

**Google SAIF (Secure AI Framework)** — Provides foundational security principles for AI systems across deployment phases.

**NIST AI RMF** — Layer 7 (Govern), Layers 2-3 (Map threat surface), Layer 6 (Measure drift), Layers 4-5 (Manage through validated execution). The formal threat model satisfies NIST's emphasis on documented assumptions.

**SOC 2** — Processing integrity through token-bound execution. Confidentiality through semantic policy egress controls. The integrity channel provides required audit evidence.

**ISO 27001 / ISO 42001** — Access control (Layers 1, 4), cryptographic controls (Layer 4 tokens), operations security (Layers 3, 5), incident management (recovery protocols).

The CSA STAR for AI Assurance Program [^14] provides third-party attestation for AI security controls.

**For detailed compliance mappings, see the Zones of Distrust [Framework Mappings](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/framework-mappings.md) directory.**

---

# 8 Interoperability Goals

ZoD is designed as a **reference architecture**, not a vendor-specific implementation. The goal is ecosystem compatibility across implementations.

## 8.1 Conformance Requirements

Conforming systems MUST demonstrate enforcement of security properties P1–P12 (see Threat Model Summary). Implementations may vary in:
-   Programming languages and runtime environments
-   CA validation logic implementation (rule engine, ML classifiers, hybrid)
-   Executor design (containerized, serverless, VM-based)
-   Monitoring infrastructure (SIEM integration, custom analytics, ML-based anomaly detection)

## 8.2 Interoperability Mechanisms

Interoperability between ZoD implementations is achieved through:

**1. Token Schema Standardization**
-   Execution tokens share a common structure (request hash, timestamp, policy reference, CA signature)
-   Token verification does not require vendor-specific logic

**2. Policy Evaluation Semantics**
-   Documented policy decision logic (permit/deny/conditional)
-   Consistent handling of edge cases (malformed requests, timeout handling, degraded mode)

**3. Audit Log Format**
-   Structured event schema for integrity channel records
-   Common fields: timestamp, action type, token reference, decision rationale, executor ID

**4. API Contract Consistency**
-   Standardized interfaces between layers (L3→L4 request format, L4→L5 token format)
-   Protocol buffer or JSON schema definitions (planned in `/specs`)

## 8.3 Ecosystem Compatibility

ZoD aims to enable:
-   **Multi-vendor deployments** where different vendors implement different layers
-   **Migration paths** where organizations can swap layer implementations without full redeployment
-   **Audit tool compatibility** where monitoring solutions can parse logs from any ZoD-conformant system

Detailed interoperability specifications are under development in the `/specs` directory.

---

# 9 Implementation Path

## 9.1 What You Can Build Today

The OS-native capabilities described in Layer 1 represent a multi-year evolution. Organizations deploying AI agents today need implementations that run on current infrastructure.

**Kubernetes reference pattern:** No-egress agent pod (Layer 3), sidecar CA proxy (Layer 4), separate execution namespace (Layer 5), screening proxy (Layer 2), SIEM integration (Layer 6), append-only audit log (integrity channel).

This implements core ZoD properties on infrastructure available today.

**Zone of Distrust roadmap implementations:**

-   Layer 2: Dedicated screening model as upstream proxy

-   Layer 3: Container orchestration and namespace separation

-   Layers 4-5: HSM/cloud KMS for token signing, proxy-based credential injection

-   Layer 6: SIEM platforms adapted for agent behavioral baselines

-   Integrity channel: S3 Object Lock, QLDB, or Immudb

## 9.2 Minimum Safe Configuration

> **ZoD Minimum Viable Enforcement Boundary (MVE):**
>
> If **L3–L5 separation + token binding + immutable logging** are not present, the system is **not ZoD-compliant**.
>
> This is the absolute minimum. Everything else is layered defense-in-depth.

Partial deployments will happen. To prevent the framework from being blamed for incomplete implementations:

**Required for ZoD compliance:**

-   L3--L5 separation (reasoning cannot directly execute)

-   Token-bound execution (no action without valid token)

-   Immutable execution logging (agent cannot tamper with audit trail)

**If you skip these, you do not have ZoD.** You have defense-in-depth with extra steps. The core property---execution governance for compromised reasoning---requires the reasoning/execution boundary to be enforced.

**Availability Posture:** ZoD is designed to fail closed on authorization failures. Implementations SHOULD provide redundancy and graceful degradation to meet organizational availability objectives, but availability guarantees are explicitly subordinate to prevention of unauthorized execution. An agent that cannot act is preferable to an agent that acts without authorization.

## 9.3 Control Evidence (Illustrative)

ZoD produces auditable evidence at each layer:

| Evidence Type | Source | Purpose |
|--------------|--------|---------|
| L4 authorization logs | CA decision records with policy version hash | Prove which policy allowed each action |
| Token issuance records | Signed tokens with TTL, nonce, content hash | Cryptographic proof of authorization |
| Execution records | L5 logs linked to approval tokens | Prove action matched authorized parameters |
| Policy change history | GitOps commits with approver identities | Audit trail for configuration changes |
| Memory audit diffs | L6 snapshots with provenance tags | Detect unauthorized state modification |
| Integrity channel health | Gap/sequence anomaly reports | Prove controls were operational |

This evidence maps directly to audit requirements under SOC 2, ISO 27001, and EU AI Act logging obligations.

## 9.4 Post-Compromise Recovery

Detection without recovery is incomplete. When compromise is confirmed:

| Recovery Action | Purpose |
|----------------|---------|
| Memory rollback | Restore to last attested snapshot before poisoning |
| Baseline reset | Clear behavioral baseline with human approval; re-establish from known-good state |
| Identity re-keying | Rotate agent credentials; invalidate all outstanding tokens |
| Cooling-off period | Mandatory reduced autonomy before full privileges resume |
| Root cause analysis | Determine entry point; update L2 rules, L4 policy, or L6 thresholds |

Recovery is not automatic. It requires human judgment (Layer 7) informed by forensic evidence from the integrity channel.

## 9.5 What Requires OS Evolution

Agent identity as a first-class OS concept, native credential brokering at the kernel level, scoped permission APIs, and model provenance attestation require platform vendor investment.

## 9.6 Why the CA Is Not a New Class of Operational Risk

CISOs will ask: \"Am I adding a new KMS-sized liability to my stack?\"

The answer: organizations already operate Tier-0 authorization systems. The CA is comparable to:

| Existing Tier-0 System | What It Does | CA Equivalent |
|----------------------|--------------|---------------|
| IAM / IdP | Authenticates principals | CA authenticates + authorizes actions |
| KMS / HSM | Protects cryptographic keys | CA uses KMS for token signing |
| Secrets manager | Brokers credential access | CA gates credential injection |
| Payment authorization | Approves financial transactions | CA approves all privileged actions |

**The key difference:** The blast radius of CA \*failure\* is halt, not exfiltration. A crashed CA stops operations; it does not authorize malicious actions. This is safer than implicit trust, where failure is invisible.

For detailed implementation guidance, see the Zones of Distrust [Implementation Guide](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/implementation-guide.md).

# 10 Conclusion

AI agents represent a genuinely new entity class in computing. They are not users. They are not devices. They are not traditional software applications. They reason autonomously, accumulate state across sessions, operate in multi-agent networks, and can be compromised in ways that are structurally invisible to them.

The Zones of Distrust provide a layered security architecture for this entity class. The core insight: traditional security assumes the requestor's intent is stable; ZoD assumes the requestor's reasoning is adversarial. Each layer depends on the layer below it and assumes every layer above it has already been compromised. Cross-layer integrity signals detect compromise through correlation, not self-reporting.

The architecture is incremental. You can deploy Layers 3--5 in weeks. Full implementation adds depth; partial implementation still reduces blast radius. And it maps directly to existing regulatory frameworks---EU AI Act, NIST AI RMF, SOC 2, ISO 27001---providing a path from architectural adoption to demonstrable compliance.

> *The agent will be compromised. The architecture limits the blast radius.*

# 11 Getting Involved

Zones of Distrust is published openly to encourage adversarial review, real-world implementation feedback, and iterative refinement toward a future vendor-neutral industry reference architecture.

This RFC is intended to evolve through public critique and deployment experience. Contributions are welcomed from researchers, security architects, platform vendors, compliance practitioners, and implementers.

## 11.1 Documentation

The primary project documents are maintained in the public repository:

| Document | Description |
|----------|-------------|
| [Architecture Specification](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/ARCHITECTURE.md) | Full seven-layer technical specification |
| [Security Properties](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/security-properties.md) | 12 measurable properties (P1-P12) with verification criteria |
| [Threat Model](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/threat-model.md) | Attacker positions, trust assumptions, degradation modes |
| [Implementation Guide](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/implementation-guide.md) | Practical deployment guidance |
| [Framework Mappings](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/framework-mappings.md) | OWASP, NIST, MITRE ATLAS, Microsoft AIRT [^9], and more |


## 11.2 Specification Status

The `/specs` directory contains implementation-oriented specifications intended for interoperability.

Some specifications are currently in draft status:

- `token-format.md` — Draft (not yet finalized)
- `policy-language.md` — RFC feedback requested
- `integrity-channel-schema.md` — Planned

This roadmap is intentional. ZoD is being published first as a defensible architectural framework, with schemas and conformance artifacts expected to mature through community input and implementation experience.

## 11.3 Contributing

ZoD is intended to improve through real-world validation and adversarial critique. Contributions are welcome, including:

- technical feedback and corrections  
- implementation examples and deployment patterns  
- integration guides for agent frameworks  
- security research and vulnerability analysis  
- mapping improvements to standards and regulatory controls  

Repository:

- **GitHub:** https://github.com/bluvibytes/zone-of-distrust  
- **Contribution Guide:** https://github.com/bluvibytes/zone-of-distrust/blob/main/CONTRIBUTING.md  

---

# 12 References

[^1]: Cloud Security Alliance and Strata Identity, "Securing Autonomous AI Agents Survey Report" (Feb 2026). https://cloudsecurityalliance.org/artifacts/securing-autonomous-ai-agents-survey-report

[^2]: Gartner, Inc., "Gartner Predicts Over 40% of Agentic AI Projects Will Be Canceled by End of 2027" (June 25, 2025). https://www.gartner.com/en-us/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027

[^3]: McKinsey & Company, "The State of AI in 2025: Agents, Innovation, and Transformation" (November 2025). https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai

[^4]: NIST (National Institute of Standards and Technology), "Technical Blog: Strengthening AI Agent Hijacking Evaluations" (January 2025). https://www.nist.gov/news-events/news/2025/01/technical-blog-strengthening-ai-agent-hijacking-evaluations

[^5]: Stanford HAI (Human-Centered Artificial Intelligence), "AI Index Report 2025" (April 2025). https://hai.stanford.edu/ai-index/2025-ai-index-report

[^6]: Anthropic, "Agentic Misalignment: How LLMs Could Be an Insider Threat" (June 2025). https://www.anthropic.com/research/agentic-misalignment

[^7]: OWASP, "Top 10 for Agentic Applications" (Dec 2025). https://owasp.org/www-project-top-10-for-agentic-applications/

[^8]: Microsoft, "Entra Agent ID: Identity for Autonomous AI Agents" (Nov 2025). https://learn.microsoft.com/en-us/entra/workload-id/workload-identities-overview

[^9]: Microsoft, "AI Red Team Taxonomy: 27 Failure Modes in AI Agents" (April 2025). https://www.microsoft.com/en-us/security/blog/2025/04/24/new-whitepaper-outlines-the-taxonomy-of-failure-modes-in-ai-agents/

[^10]: Google, "Secure AI Framework (SAIF)" (2023, updated 2025). https://cloud.google.com/security/resources/secure-ai-framework

[^11]: Gartner, "Market Guide for AI Trust, Risk and Security Management (AI TRiSM)" (2025).

[^12]: IBM, "IBM Introduces Industry-First Software to Unify Agentic Governance and Security" (June 18, 2025). https://newsroom.ibm.com/2025-06-18-ibm-introduces-industry-first-software-to-unify-agentic-governance-and-security

[^13]: Cloud Security Alliance, "The Agentic Trust Framework: Zero Trust Governance for AI Agents" (Feb 2026). https://cloudsecurityalliance.org/blog/2026/02/02/the-agentic-trust-framework-zero-trust-governance-for-ai-agents

[^14]: Cloud Security Alliance, "CSA STAR for AI Assurance Program" (2025). https://cloudsecurityalliance.org/star/ai/

[^15]: Cloud Security Alliance, "MAESTRO: Multi-Agent Environment for Security Threat and Risk Operations" (Feb 2025). https://cloudsecurityalliance.org/research/working-groups/ai-safety/

[^16]: Cisco, "Integrated AI Security Framework for Agentic Systems" (2025). https://www.cisco.com/c/en/us/solutions/ai-security.html

[^17]: OWASP, "OWASP Top 10 for Agentic Applications" (December 2025). https://owasp.org/www-project-top-10-for-large-language-model-applications/

---


# Appendix A: Terminology

This appendix provides formal definitions for key terms used throughout this document.

## AI Agent

An autonomous software system powered by a foundation model that can perceive its environment, reason about tasks, make decisions, and take actions to achieve specified goals — including invoking tools, accessing data, and interacting with external systems — with varying degrees of human oversight.

## Autonomous

Operating with the ability to make decisions and take actions without requiring explicit human approval for each individual step, while still subject to policy constraints and governance frameworks.

## Privileged Action

Any operation that modifies state, accesses sensitive data, crosses trust boundaries, or has irreversible consequences — including but not limited to: financial transactions, data modifications, credential usage, system configurations, external communications, and resource provisioning.

## Execution Token

A cryptographic attestation binding a validated request to its authorized execution context. Tokens are unforgeable, non-transferable, short-lived, and content-bound through cryptographic hashing.

## Integrity Channel

A tamper-evident, append-only logging mechanism that records all privileged actions and validation decisions. Implemented using signed events, monotonic sequence numbers, and write-once storage.

## Baseline Drift

The phenomenon where an agent’s behavior deviates from expected patterns over time due to prompt injection, model drift, or environmental manipulation.

## Compromise Non-Propagation

A security property ensuring that compromise of one architectural layer does not automatically grant control over other layers. Achieved through isolation and independent validation.

## MVE (Minimum Viable Enforcement Boundary)

The minimum architectural controls required for ZoD compliance: Layer 3–5 separation, token binding, and immutable logging.

## Policy Probing

An attack technique where an adversary systematically tests policy boundaries to identify permissible actions and construct exploit chains.

## Tier-0 System

Critical infrastructure components that occupy the highest trust tier in an architecture (e.g., KMS, HSM, IAM, CA). Compromise of Tier-0 systems has cascading impact across the entire environment.

## Request Canonicalization

The process of normalizing structured requests into a consistent, deterministic format before cryptographic hashing or policy evaluation to prevent semantically equivalent but syntactically different bypass attacks.

## Token Binding

The cryptographic linkage between a validated request’s content hash and the execution token that authorizes its performance, preventing token reuse or parameter substitution attacks.

## Cognitive Isolation

Architectural separation between the reasoning engine (Layer 3) and execution capability (Layer 5), such that compromise of reasoning does not directly grant execution authority.

## Attestation Surface

The explicit boundary where privileged actions are validated against policy before execution. In ZoD, this is the Certificate Authority (Layer 4).

## Immutable Audit Log

A tamper-evident record of all privileged actions that cannot be retroactively modified, enabling forensic analysis and compromise detection.

---

# Appendix B: Normative Language (RFC 2119)

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in **RFC 2119**.

- **MUST / REQUIRED / SHALL:** Indicates an absolute requirement for implementation.
- **SHOULD / RECOMMENDED:** Indicates a strong recommendation; alternative approaches may be acceptable in specific circumstances with documented justification.
- **MAY / OPTIONAL:** Indicates a truly optional feature or approach.

---

# Appendix C: Versioning and Change Process

This document is published as an open RFC and is expected to evolve based on community feedback and implementation experience.

## Versioning

ZoD uses semantic versioning conventions aligned to RFC-style publication:

- **v0.x RFC:** Draft architecture published for public review
- **v1.0:** First stable release suitable for use as an industry reference baseline
- **v1.x:** Backwards-compatible refinements, clarifications, and mapping updates
- **v2.0+:** Major structural changes that may introduce incompatibilities

## Change Process

Changes to ZoD are managed through the public repository:

- Substantive architectural changes SHOULD be proposed through GitHub Discussions before PR submission.
- Minor edits, clarifications, typo fixes, and mapping updates MAY be submitted directly via pull request.
- Security-related issues SHOULD follow the responsible disclosure policy in `SECURITY.md`.

All accepted changes MUST be traceable through Git history and review artifacts.

## RFC Closure and Promotion Criteria

Promotion from RFC v0.9 to v1.0 is expected to require:

- community review of attacker positions and threat model completeness  
- validation that security properties remain implementable and testable  
- at least one reference implementation or partial enforcement prototype  
- review of interoperability goals and minimum viable enforcement boundary  

ZoD maintainers may publish intermediate RFC versions (e.g., v0.10, v0.11) prior to v1.0 based on feedback volume.

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
