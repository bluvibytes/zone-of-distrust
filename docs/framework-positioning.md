# Framework Positioning

**Zones of Distrust: Positioning in the AI Security Framework Landscape**

*Version 0.9 RFC — February 2026*

---

## Overview

The AI agent security space has matured rapidly. Between late 2024 and early 2026, over a dozen major frameworks have been published. Understanding what each does—and what it does not do—is essential to understanding where the Zones of Distrust fit.

**The core positioning:**

> *ZoD is not a competing framework. It is the implementation architecture that makes the other frameworks enforceable.*

**The novelty kernel:**

> *ZoD is execution governance for compromised reasoning—by isolating reasoning from execution and enforcing policy externally.*

Traditional security (including Zero Trust) assumes that after verification, the decision-maker can be trusted. ZoD assumes the decision-maker is the threat. The agent passes every verification check because it IS the authorized principal—with corrupted reasoning. ZoD governs execution anyway.

*Note: ZoD uses "governance" in the security sense—runtime enforcement of allowed execution—not organizational compliance workflows.*

---

## Three Types of Framework

The published frameworks fall into three complementary categories:

| Type | Question Answered | Examples |
|------|-------------------|----------|
| **Threat Taxonomies** | What attacks and failures should we defend against? | Cisco, OWASP Agentic, Microsoft AIRT, NVIDIA/Lakera, MITRE ATLAS |
| **Methodology & Governance** | How do we assess, govern, and manage AI risk? | CSA MAESTRO, CSA ATF, NIST AI RMF, Google SAIF, AWS Scoping |
| **Architectural Implementation** | How do we actually build the defenses? | **Zones of Distrust** |

*Many frameworks span multiple categories; this grouping reflects their primary emphasis.*

---

## The Framework Landscape

### Threat Taxonomies

#### Cisco Integrated AI Security Framework (Late 2025)

**What it is:** The most comprehensive threat taxonomy for agentic AI. Catalogs 19 attacker objectives, 150+ techniques, 112 subtechniques.

**Key contributions:**
- OpenClaw dataset analysis (31,000+ agent skills, 26% vulnerable)
- Three purpose-built scanners: MCP Scanner, A2A Scanner, Skill Scanner
- Regulatory mapping to EU AI Act, NIST, Budapest Convention
- Lifecycle-aware threat modeling

**What it doesn't provide:** Architectural specification for how to build defenses.

**ZoD integration:** Cisco's threat catalog informs ZoD's Layer 2 screening rules, Layer 4 validation criteria, and Layer 6 monitoring signatures.

---

#### OWASP Top 10 for Agentic Applications (Late 2025)

**What it is:** First security framework dedicated exclusively to autonomous AI systems. Developed by 100+ researchers with expert review from NIST, European Commission, Alan Turing Institute.

**The ten risk categories:**

| ID | Risk | ZoD Primary Defense |
|----|------|---------------------|
| ASI01 | Agent Goal Hijacking | L2, L3, L4 |
| ASI02 | Tool Misuse and Exploitation | L4 semantic policy |
| ASI03 | Identity and Privilege Abuse | L1, L4 chain validation |
| ASI04 | Supply Chain Vulnerabilities | L1 provenance |
| ASI05 | Memory Poisoning | L6 memory audit |
| ASI06 | Human Trust Manipulation | L7 escalation |
| ASI07 | Inter-Agent Communication Compromise | L2, L4 chain validation |
| ASI08 | Cascading Failures | P9 graceful degradation |
| ASI09 | Rogue Agent Behavior | L6 behavioral monitoring |
| ASI10 | Inadequate Monitoring and Observability | L6, integrity channel |

**What it doesn't provide:** How to architect a system that prevents these risks.

**ZoD integration:** Every OWASP risk category maps to specific ZoD layer controls.

---

#### Microsoft Taxonomy of Failure Modes (April 2025)

**What it is:** Classification of failures across security and safety dimensions, distinguishing novel agentic failures from existing failures with new severity.

**Novel failure modes identified:**
- Agent compromise
- Agent injection
- Agent impersonation
- Agent flow manipulation
- Multi-agent jailbreaks

**Key case study:** Memory poisoning using LangChain/LangGraph/GPT-4o—detailed attack mechanics for data exfiltration through corrupted recall.

**Microsoft SDL for AI (Early 2026):** Now requires agent identity, RBAC enforcement, recommends AI shutdown mechanisms.

**What it doesn't provide:** Layered architecture specification.

**ZoD integration:** Microsoft's design considerations map directly to ZoD layers:
- Identity management → L1
- Memory hardening → L6 memory audit
- Control flow regulation → L4
- Environment isolation → L3, L5
- Comprehensive logging → Integrity channel

---

#### NVIDIA/Lakera AI Safety and Security Framework (Late 2025)

**What it is:** Research paper with operational taxonomy connecting component-level risks to system-level harms.

**Key contributions:**
- Agent Red Teaming via Probes (ARP) methodology
- Four autonomy levels (L0-L3) with escalating controls
- AI-Q Research Assistant case study: 10,000+ attack/defense traces
- Finding: Direct user input attacks amplify differently than external data source attacks

**What it doesn't provide:** Complete architectural implementation.

**ZoD integration:** 
- Autonomy levels adopted in L4's autonomy-level enforcement
- ARP methodology validates L2 and L6 implementations
- Amplification research informs L2 screening model design

---

#### MITRE ATLAS (Adversarial Threat Landscape for AI Systems)

**What it is:** Extension of MITRE ATT&CK framework for AI/ML systems. Tactics, techniques, and procedures (TTPs) for adversarial ML.

**Key contributions:**
- Standardized vocabulary for AI attacks
- Case study repository
- Integration with existing threat intelligence workflows

**What it doesn't provide:** Agent-specific architectural controls.

**ZoD integration:** ATLAS TTPs inform Layer 2 detection signatures and Layer 6 behavioral monitoring patterns.

---

### Methodology & Governance Frameworks

#### CSA MAESTRO (February 2025)

**What it is:** Multi-Agent Environment Security, Trust, Risk and Oversight. First enterprise-grade methodology for multi-agent security assessment.

**The MAESTRO Hierarchy:**

```
┌─────────────────────────────────────────┐
│     MAESTRO (Assessment Methodology)    │
├─────────────────────────────────────────┤
│  Instructs organizations HOW to assess  │
│  multi-agent systems and WHAT controls  │
│  should exist                           │
└─────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────┐
│     Zones of Distrust (Architecture)    │
├─────────────────────────────────────────┤
│  Provides the IMPLEMENTATION PATTERN    │
│  that satisfies MAESTRO's requirements  │
└─────────────────────────────────────────┘
```

**MAESTRO control domains → ZoD mapping:**

| MAESTRO Domain | ZoD Layer(s) |
|----------------|--------------|
| Agent Identity & Access | L1, L4 |
| Inter-Agent Communication | L2, L4 chain validation |
| Tool & Resource Security | L4 semantic policy, L5 |
| Memory & State Management | L6 memory audit |
| Orchestration Security | L4, L7 |
| Human Oversight | L7 |
| Monitoring & Observability | L6, integrity channel |

**What MAESTRO doesn't provide:** The architectural blueprint.

**ZoD integration:** ZoD is an implementation architecture for MAESTRO-aligned systems.

---

#### CSA AI Technology Framework (ATF) (Early 2026)

**What it is:** Comprehensive technology framework for AI security from the Cloud Security Alliance.

**Key contributions:**
- Technology-agnostic control requirements
- Cloud-native AI security patterns
- Integration with CSA CCM (Cloud Controls Matrix)

**What it doesn't provide:** Specific architectural patterns for agent containment.

**ZoD integration:** ATF control requirements map to ZoD layer specifications. ZoD provides one compliant architectural pattern.

---

#### AWS Generative AI Scoping Matrix (2025)

**What it is:** Practical scoping tool for AI security assessments. Helps organizations categorize AI deployments by risk and determine appropriate controls.

**Key contributions:**
- Risk-based categorization framework
- Control selection guidance
- Integration with AWS Well-Architected Framework

**What it doesn't provide:** Defense-in-depth architectural specification.

**ZoD integration:** AWS scoping categories map to ZoD's risk-weighted escalation (L7) and autonomy-level enforcement (L4).

---

#### Google SAIF (June 2023, Updated Early 2026)

**What it is:** Secure AI Framework organized around six core elements and four lifecycle phases.

**Lifecycle phases → ZoD mapping:**

| SAIF Phase | ZoD Layers |
|------------|------------|
| Secure Development | L1, L2 |
| Secure Deployment | L3, L4, L5 |
| Secure Execution | L3-L5 runtime |
| Secure Monitoring | L6, integrity channel |

**What it doesn't provide:** Concrete architectural pattern for execution governance.

**ZoD integration:** ZoD is the architectural pattern that implements SAIF's lifecycle controls.

---

#### NIST AI Risk Management Framework (AI RMF)

**What it is:** Four core functions for AI risk management: Govern, Map, Measure, Manage.

**NIST functions → ZoD mapping:**

| NIST Function | ZoD Implementation |
|---------------|-------------------|
| Govern | L7 Human Governance |
| Map | L2-L3 threat surface mapping |
| Measure | L6 behavioral measurement |
| Manage | L4-L5 validated execution |

**What it doesn't provide:** Implementation architecture.

**ZoD integration:** ZoD's formal threat model and boundary conditions satisfy NIST's emphasis on documented assumptions and limitations.

---

#### CoSAI (Coalition for Secure AI)

**What it is:** OASIS Open Project with founding members including Anthropic, Amazon, Cisco, Google, IBM, Intel, Microsoft, NVIDIA, OpenAI, and PayPal.

**CoSAI-RM (Risk Map):** Open framework for AI security risk identification, derived from SAIF.

**ZoD integration:** ZoD provides the architectural blueprint for operationalizing CoSAI-RM controls.

---

## The Gap ZoD Fills

Autonomous agents introduce risk classes—delegation abuse, tool misuse, task drift, long-horizon autonomy—that cannot be handled purely through model governance. Agents are not just models; they are operators with persistence, tool access, delegation authority, and state.

### What ZoD Unifies

ZoD is one of the first frameworks to assemble these capabilities into a single, layered architecture. Individual elements exist elsewhere; the unified model with explicit trust boundaries does not:

| Capability | Description | Why ZoD Emphasis Is Distinct |
|------------|-------------|---------------------------|
| **Defense-in-depth with explicit trust boundaries** | Each layer assumes every layer above it has been compromised | Frameworks describe controls individually, not layer relationships |
| **Policy opacity** | Agent cannot observe or reason about security policies (L3/L4) | Rarely formalized—agent ignorance as explicit security property |
| **Cross-layer integrity signaling** | Immutable, out-of-band channel—no layer self-reports | Frameworks often assume monitoring is trusted |
| **Baseline integrity verification** | Monitoring the monitoring system for adversarial drift | Recursive security problem rarely addressed |
| **Semantic intent policy** | Concrete policy language with negative constraints | Frameworks recommend "policy" without specifying language |
| **Parameter-bound token specification** | Content hash, nonce, executor attestation, TTL binding | Extends existing credential proxy patterns |
| **Formal degradation model** | Defined behavior when each layer fails | Frameworks often assume controls work |
| **Unified architecture** | All components assembled with directional logic | Individual controls exist; unified model is uncommon |

*On policy opacity: This is not secrecy as a primary defense; it is reducing adversarial adaptation by preventing policy gradient learning in the agent.*

---

## Cross-Framework Mapping

### ZoD Layers → All Frameworks

| ZoD Layer | Cisco | SAIF | OWASP | Microsoft | NVIDIA | MAESTRO | ATF | AWS | MITRE |
|-----------|-------|------|-------|-----------|--------|---------|-----|-----|-------|
| L7: Governance | Regulatory mapping | Governance controls | ASI10 | Design considerations | Autonomy levels | Human Oversight | Governance | Risk categorization | — |
| L6: Monitoring | Detection evasion | Secure Monitoring | ASI09 | Logging/monitoring | ARP methodology | Monitoring & Observability | Monitoring | — | Detection TTPs |
| L5: Execution | Tool exploitation | Secure Execution | ASI02 | Environment isolation | System harm mapping | Tool & Resource | Execution | — | — |
| L4: CA/Validation | Multi-agent coord. | Application controls | ASI03 | Control flow regulation | Escalating controls | Orchestration | Access Control | Control selection | — |
| L3: Cognitive Isolation | Reasoning manipulation | — | ASI01 | Agent injection | Component risk mapping | — | — | — | — |
| L2: Input Control | Input manipulation | Secure Development | ASI06 | Agent compromise | Pipeline amplification | Inter-Agent Comm. | Input validation | — | Evasion TTPs |
| L1: OS Foundation | Supply chain | Secure Development | ASI04 | Identity management | Model provenance | Agent Identity | Infrastructure | — | Initial Access |

*Cells indicate relevance, not direct equivalence. Framework emphases vary.*

---

### Capability Comparison Matrix

| Capability | Traditional Zero Trust | Prompt Filters | Credential Proxy | Agent Identity | Cisco | SAIF | OWASP | MS | NVIDIA | MAESTRO | **ZoD** |
|------------|----------------------|----------------|------------------|----------------|-------|------|-------|-----|--------|---------|---------|
| Pre-reasoning input screening | — | ✓ | — | — | ~ | ~ | ~ | ~ | ~ | ~ | **✓** |
| Reasoning/execution separation | — | — | — | ~ | — | — | ~ | ~ | — | ~ | **✓** |
| Parameter-bound token | — | — | ~ | — | — | — | — | — | — | — | **✓** |
| Semantic intent policy | — | — | — | — | — | — | — | — | — | — | **✓** |
| Memory audit (external) | — | — | — | — | ~ | — | ~ | ~ | — | ~ | **✓** |
| Behavioral baseline integrity | — | — | — | — | — | — | — | — | — | — | **✓** |
| Multi-agent chain validation | — | — | — | ~ | ~ | — | ~ | ~ | — | ~ | **✓** |
| Immutable integrity channel | — | — | — | — | — | — | — | — | — | — | **✓** |
| Cross-layer correlation | — | — | — | — | — | — | — | — | — | — | **✓** |
| Formal degradation model | — | — | — | — | — | — | — | — | — | — | **✓** |
| Threat taxonomy depth | — | — | — | — | ✓ | ~ | ✓ | ✓ | ✓ | ~ | ~ |
| Lifecycle guidance | — | — | — | — | ~ | ✓ | — | ~ | ~ | ✓ | ~ |
| Red team methodology | — | — | — | — | ✓ | ~ | — | ✓ | ✓ | ~ | — |
| Assessment methodology | — | — | — | — | ~ | ~ | ~ | ~ | ~ | ✓ | ~ |

*✓ = core defined capability | ~ = partial or ad-hoc coverage | — = absent*

**Key insight:** The frameworks excel at threat identification, lifecycle guidance, assessment methodology, and red teaming. ZoD excels at architectural control implementation—the layer where identified threats become enforceable defenses.

---

## Real-World Incident Mapping

The OWASP Top 10 for Agentic Applications documents production incidents that validate ZoD's design:

### Malicious Postmark MCP Server

**Incident:** Legitimate-appearing MCP server on npm secretly BCC'd every agent-sent email to attacker.

**ZoD defense:**
- L2: Screens MCP protocol messages
- L4: Semantic intent policy detects unauthorized email recipients
- L6: Monitors for communication pattern anomalies

---

### PhantomRaven/Slopsquatting

**Incident:** 126 malicious packages registered under names AI assistants hallucinate when recommending dependencies.

**ZoD defense:**
- L1: Model provenance attestation, supply chain verification
- L4: Authorization scope—agent cannot install packages without validated approval

---

### Procurement Agent Memory Poisoning

**Incident:** Three weeks of gradual memory poisoning caused procurement agent to confidently explain why transferring funds to attacker accounts served the company.

**ZoD defense:**
- L6: Memory audit detects anomalous memory accumulation
- L4: Behavioral baseline catches deviation in financial transaction patterns
- L4: Semantic intent policy blocks transfers to non-approved accounts regardless of justification

---

### Agent Session Smuggling (A2A Protocol)

**Incident:** Malicious agents exploit trust relationships in multi-turn A2A conversations.

**ZoD defense:**
- L2: Screens inter-agent communications
- L4: Validates multi-agent request chains with independent trust verification at each hop
- L6: Detects behavioral drift correlated with peer agent interactions

---

### Ray Framework Breach

**Incident:** Over 230,000 Ray AI clusters compromised through infrastructure vulnerabilities.

**ZoD defense:**
- L1: Process isolation and infrastructure integrity
- L6: Monitoring of execution environment health
- Architecture: Acknowledges infrastructure compromise as threat model boundary condition

---

## Compliance Mapping

| Regulation/Standard | ZoD Support |
|--------------------|-------------|
| **EU AI Act** | L7 (human oversight), L6 (continuous monitoring), L4 audit log (transparency), integrity channel (tamper-proof logging) |
| **NIST AI RMF** | L7 (Govern), L2-L3 (Map), L6 (Measure), L4-L5 (Manage) |
| **NIST CSF** | Maps through AI RMF alignment |
| **SOC 2** | L1 (security), L4-L5 (processing integrity), L6 (availability monitoring), semantic policy (confidentiality) |
| **ISO 27001** | Access control (L1, L4), cryptographic controls (L4 tokens), operations security (L3, L5), incident management (recovery protocols) |
| **ISO/IEC 42001** | AI-specific controls—behavioral monitoring, memory audit, semantic intent |
| **SEC AI Guidance** | L7 (documented oversight), integrity channel (audit trails), formal threat model (risk assessment) |

---

## Framework Timeline

```
2023
├── Jun: Google SAIF launched
│
2024
├── MITRE ATLAS expansion for generative AI
├── AWS Scoping Matrix introduced
│
2025
├── Early: CSA MAESTRO published
├── Spring: Microsoft Taxonomy of Failure Modes
├── Late: NVIDIA/Lakera Framework
├── Late: Cisco Integrated AI Security Framework
├── Late: OWASP Top 10 for Agentic Applications
│
2026
├── Early: Google SAIF updated
├── Early: CSA ATF published
├── Early: Microsoft SDL for AI updated (agent identity required)
├── Feb: Zones of Distrust v0.9 RFC ◄── YOU ARE HERE
```

---

## What ZoD Does Not Attempt

To sharpen positioning, ZoD explicitly excludes the following from scope:

- **ZoD does not define a threat taxonomy.** It implements defenses against threats identified by other frameworks (Cisco, OWASP, MITRE ATLAS).
- **ZoD does not replace red teaming methodology.** It provides the architecture that red teams validate.
- **ZoD does not replace organizational governance programs.** It provides technical controls and evidence; compliance requires broader organizational work.
- **ZoD does not guarantee L1 integrity.** ZoD assumes infrastructure is hardened but not infallible; L6 integrity telemetry is designed to detect degradation, not fully survive total host compromise.

---

## Summary

| Framework Type | What They Provide | What They Need |
|----------------|-------------------|----------------|
| **Threat Taxonomies** (Cisco, OWASP, Microsoft, NVIDIA, MITRE) | What can go wrong | How to build defenses |
| **Methodology & Governance** (MAESTRO, ATF, AWS, SAIF, NIST) | How to assess and govern | Architectural implementation |
| **Zones of Distrust** | How to build it | Threat intelligence, assessment methodology |

**The relationship is complementary, not competitive.**

> *ZoD sits at the intersection of what the industry has identified (threat taxonomies), how the industry recommends governing (methodology frameworks), and how the defenses should actually be built (architectural implementation). It is the engineering layer that makes the other frameworks enforceable.*

---

## Related Documents

- [Architecture Specification](architecture.md) — The seven-layer implementation
- [Security Properties](security-properties.md) — Measurable properties (P1-P12)
- [Threat Model](threat-model.md) — What ZoD defends against

---

## Sources

**Threat Taxonomies**
- Cisco, "Integrated AI Security Framework for Agentic Systems," 2025. 19 attacker objectives, 150+ techniques, 112 subtechniques. Includes MCP Scanner, A2A Scanner, Skill Scanner.
- OWASP, "Top 10 for Agentic Applications," December 2025. ASI01-ASI10 vulnerability prioritization.
- Microsoft, "AI Red Team (AIRT) Taxonomy," April 2025. 27 agent failure mode classifications.
- NVIDIA/Lakera, "Agentic Security Whitepaper," April 2025. Autonomy Risk Progression methodology.
- MITRE, "ATLAS (Adversarial Threat Landscape for AI Systems)," ongoing. AI-specific TTPs extending ATT&CK.

**Methodology & Governance**
- Cloud Security Alliance, "MAESTRO: Multi-Agent Environment for Security Threat and Risk Operations," February 2025.
- Cloud Security Alliance, "Agentic Trust Framework (ATF)," February 2026. Governance maturity model.
- AWS, "Generative AI Scoping Matrix" and "Agentic AI Scoping Matrix," November 2025. Deployment and agency categorization.
- Google, "Secure AI Framework (SAIF)," 2023. Lifecycle security model.
- NIST, "AI Risk Management Framework (AI RMF)," January 2023. Govern/Map/Measure/Manage structure.

**Industry Research**
- SailPoint, "AI Agents: The New Attack Surface," 2025. 80% unintended actions, 23% credential exposure findings.
- CyberArk, "2025 Identity Security Landscape," 2025. Machine identities outnumber humans ~80:1.
- Gartner, "Market Guide for AI Trust, Risk and Security Management," 2025.

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
