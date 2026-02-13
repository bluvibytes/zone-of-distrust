# Zones of Distrust

**A Layered Security Architecture for Autonomous AI Agents**

*Version 0.9 RFC — February 2026*

---

Zero Trust gave us the philosophy: *never trust, always verify*. But Zero Trust was built for users and devices—entities that know when they've been compromised.

Autonomous AI agents break that assumption. A prompt-injected agent doesn't know it's been compromised. The manipulation becomes its genuine reasoning.

**Zones of Distrust** extends Zero Trust principles for autonomous entities that can be compromised without knowing it.

**Core Thesis:** *Security is not about making the agent trustworthy. It's about building a system that remains safe even when the agent is not.*

---

## 🔎 Start Here

If you're new to ZoD, start with:

1. **[Architecture Specification](/docs/ARCHITECTURE.md)**
2. **[Threat Model](/docs/threat-model.md)**
3. **[Security Properties (P1–P12)](/docs/security-properties.md)**
4. **[Implementation Guide](/docs/implementation-guide.md)**

Full documentation index: **[docs/README.md](/docs/README.md)**

---

## 🏗️ Architecture Overview

ZoD defines seven interdependent layers:

| Layer | Name | Function |
|-------|------|----------|
| L7 | Human Governance | Risk-weighted escalation, policy setting, break-glass procedures |
| L6 | Continuous Monitoring | Behavioral baselining, drift detection, memory audit |
| L5 | Execution | Isolated process for validated actions, immutable logging |
| L4 | Request Validation (CA) | Independent Certificate Authority, semantic policy, token binding |
| L3 | Cognitive Isolation | Agent reasoning separated from execution capability |
| L2 | Input Control | Adversarial screening before agent processes data |
| L1 | OS Foundation | Agent identity, process isolation, credential brokering |

**Core Thesis:** *Security is not about making the agent trustworthy. It's about building a system that works even when the agent is not.*

---

## 🎯 What We Want Reviewed (Attack This)

ZoD is published as an RFC to invite adversarial critique. We are actively seeking review on:

- Cross-layer bypass scenarios (L2→L5, L3→L5, etc.)
- Prompt injection containment limits and failure modes
- Token binding flaws and replay/exfiltration risks
- Multi-agent boundary failures and delegated-agent abuse
- Drift detection evasion techniques
- Break-glass / human override abuse paths
- Logging integrity assumptions and tamper resistance

If you find a weakness, eevn if it's outside these areas, open an issue describing the attack path and impacted layers.

---

## 🧩 Security Properties

ZoD defines **12 measurable security properties (P1–P12)** intended to serve as a baseline for agentic system security evaluation.

See: **[Security Properties](/docs/security-properties.md)**

---

## 🗺️ Framework Mappings

ZoD includes crosswalks to major AI security and governance frameworks:

- OWASP Agentic
- MITRE ATLAS
- NIST AI RMF
- Google SAIF
- MAESTRO
- EU AI Act / ISO / SOC 2 mappings

See: **[Mapping Index](/docs/mappings/README.md)**

---

## 🧩 Road to a Community Standard

ZoD is currently **v0.9 RFC**. Breaking changes are expected.

Target milestones:
- v0.9 → community critique + issue intake
- v0.95 → bypass catalog + red-team test corpus
- v1.0 → stable reference architecture + security property baseline

---

## 🚀 Reference Implementation

**Status:** Planned (Q2 2026)

A vendor-neutral agent runtime implementing ZoD across Windows, macOS, Linux, and Android is in development.

---

## 🤝 Contributing

ZoD is published openly to evolve into an industry reference architecture.

Contributions are welcome, including:
- threat model critique and bypass analysis
- implementation patterns for agent frameworks
- security property validation and measurable metrics
- real-world deployment constraints and lessons learned

See: **[CONTRIBUTING](/CONTRIBUTING.md)**

---

## 📋 Project Documents

| Document | Description |
|----------|-------------|
| [ROADMAP](/ROADMAP.md) | Implementation timeline and milestones |
| [GOVERNANCE](/GOVERNANCE.md) | Project governance model |
| [SECURITY](/SECURITY.md) | Security policy and reporting |
| [DISCLAIMER](/DISCLAIMER.md) | Legal disclaimers |
| [TRADEMARKS](/TRADEMARKS.md) | Trademark notices |

---

## 📜 License

| Content | License |
|---------|---------|
| Documentation & Specification | [Creative Commons CC-BY 4.0](/LICENSE-CC-BY) |
| Reference Implementation (when released) | [Apache 2.0](/LICENSE) |

---

## 🏢 About

Too many solutions are bandaids—security built for humans at the keyboard, retrofitted for AI. We've watched incident after incident unfold like a horror movie, everyone yelling "DON'T OPEN THE DOOR!" while shoving your mouth full of popcorn, unable to tear your eyes away from the actors doing just that.

We decided to do something about it instead.

Zones of Distrust is our open contribution to help raise the bar for agentic AI security as an open reference architecture for securing autonomous AI agents. 

We (humans) need new solutions. Let's build what works.

**GitHub:** [@bluvibytes](https://github.com/bluvibytes/zone-of-distrust)

**Contact:** security@bluvi.ai

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
