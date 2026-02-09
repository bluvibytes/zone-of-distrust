# Zones of Distrust

**A Layered Security Architecture for Autonomous AI Agents**

*Version 0.9 RFC — February 2026*

---

Zero Trust gave us the philosophy: *never trust, always verify*. But Zero Trust was built for users and devices—entities that know when they've been compromised.

AI agents break that assumption. A prompt-injected agent doesn't know it's been compromised. The manipulation becomes its genuine reasoning.

**Zones of Distrust** extends Zero Trust principles for autonomous entities that can be compromised without knowing it.

---

## 🏗️ Architecture Overview

The architecture consists of seven interdependent layers:

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

## 📚 Documentation

| Document | Description |
|----------|-------------|
| **[Architecture Specification](/docs/ARCHITECTURE.md)** | Complete seven-layer technical specification |
| **[Executive Brief](/docs/executive-brief.md)** | High-level summary for leadership |
| **[Security Properties](/docs/security-properties.md)** | 12 measurable properties (P1-P12) |
| **[Threat Model](/docs/threat-model.md)** | Attacker positions and trust assumptions |
| **[Implementation Guide](/docs/implementation-guide.md)** | Practical deployment guidance |
| **[Full Documentation](/docs/README.md)** | Complete documentation index |

---

## 🗺️ Framework Mappings

ZoD maps to major AI security frameworks, demonstrating alignment and coverage:

| Category | Frameworks |
|----------|------------|
| **Threat Taxonomies** | [OWASP Agentic](/docs/mappings/owasp-agentic-mapping.md), [Cisco](/docs/mappings/cisco-mapping.md), [Microsoft AIRT](/docs/mappings/microsoft-airt-mapping.md), [MITRE ATLAS](/docs/mappings/mitre-atlas-mapping.md) |
| **Methodology** | [MAESTRO](/docs/mappings/maestro-mapping.md), [NIST AI RMF](/docs/mappings/nist-ai-rmf-mapping.md), [Google SAIF](/docs/mappings/google-saif-mapping.md), [AWS Scoping](/docs/mappings/aws-scoping-mapping.md) |
| **Compliance** | [EU AI Act, SOC 2, ISO 27001, ISO 42001](/docs/mappings/compliance-mapping.md) |

See the [complete mapping index](/docs/mappings/README.md) for all 18 framework mappings.

---

## 🚀 Reference Implementation

**Status:** Coming Q2 2026

We're building a vendor-neutral agent runtime that implements this architecture across Windows, macOS, Linux, and Android.

**Get notified:** Sign up at [bluvi.ai/zones-of-distrust](https://bluvi.ai/zones-of-distrust)

---

## 📋 Project Documents

| Document | Description |
|----------|-------------|
| [ROADMAP.md](/ROADMAP.md) | Implementation timeline and milestones |
| [CONTRIBUTING.md](/CONTRIBUTING.md) | How to contribute |
| [SECURITY.md](/SECURITY.md) | Security policy and reporting |
| [GOVERNANCE.md](/GOVERNANCE.md) | Project governance model |
| [DISCLAIMER.md](/DISCLAIMER.md) | Legal disclaimers |
| [TRADEMARKS.md](/TRADEMARKS.md) | Trademark notices |

---

## 🤝 Contributing

This architecture is published openly to become an industry standard. We welcome:

- Technical feedback and improvements
- Implementation examples
- Integration patterns for agent frameworks
- Real-world use case documentation

See [CONTRIBUTING.md](/CONTRIBUTING.md) for guidelines.

---

## 📜 License

| Content | License |
|---------|---------|
| Documentation & Specification | [Creative Commons CC-BY 4.0](/LICENSE-CC-BY) |
| Reference Implementation (when released) | [Apache 2.0](/LICENSE) |

See [LICENSES.md](/LICENSES.md) for complete licensing details.

---

## 🏢 About BluVi

BluVi is building the security infrastructure for autonomous AI agents. We're publishing the Zones of Distrust architecture openly while developing production-grade commercial implementations.

**Learn more:** [bluvi.ai](https://bluvi.ai)  
**Contact:** info@bluvi.ai  
**GitHub:** [@bluvibytes](https://github.com/bluvibytes)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
