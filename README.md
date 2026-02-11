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

## 🧭 Repository Map

- `docs/` — Whitepaper, architecture, threat model, implementation guide, security properties
- `specs/` — Schemas and normative specs (RFC; evolving)
- `docs/mappings/` — Crosswalks to OWASP/NIST/CSA, compliance mappings, and indexes
- `.github/` — Issue/PR templates (coming)
  
---

## 📚 Documentation

| Document | Description |
|----------|-------------|
| [Architecture Specification](docs/ARCHITECTURE.md) | Complete seven-layer technical specification |
| [Executive Brief](docs/executive-brief.md) | High-level summary for leadership |
| [Security Properties](docs/security-properties.md) | 12 measurable properties (P1-P12) |
| [Threat Model](docs/threat-model.md) | Attacker positions and trust assumptions |
| [Implementation Guide](docs/implementation-guide.md) | Practical deployment guidance |
| [Full Documentation Index](docs/README.md) | Complete documentation directory |


---

## 🗺️ Framework Mappings

ZoD maps to major AI security frameworks, demonstrating alignment and coverage:

| Category | Frameworks |
|----------|------------|
| **Threat Taxonomies** | [OWASP Agentic](docs/mappings/owasp-agentic-mapping.md), [Cisco](docs/mappings/cisco-mapping.md), [Microsoft AIRT](docs/mappings/microsoft-airt-mapping.md), [MITRE ATLAS](docs/mappings/mitre-atlas-mapping.md) |
| **Methodology** | [MAESTRO](docs/mappings/maestro-mapping.md), [NIST AI RMF](docs/mappings/nist-ai-rmf-mapping.md), [Google SAIF](docs/mappings/google-saif-mapping.md), [AWS Scoping](docs/mappings/aws-scoping-mapping.md) |
| **Compliance** | [EU AI Act](docs/mappings/compliance-mapping.md#eu-ai-act), [SOC 2](docs/mappings/compliance-mapping.md#soc-2-trust-service-criteria), [ISO 27001](docs/mappings/compliance-mapping.md#isoiec-270012022), [ISO 42001](docs/mappings/compliance-mapping.md#isoiec-420012023), [HIPAA](docs/mappings/industry-mapping.md#healthcare-hipaa-hitrust-fda), [PCI DSS](docs/mappings/industry-mapping.md#pci-dss-v401-for-ai-payment-agents) |

See the [complete mapping index](docs/mappings/README.md) for all framework mappings.

---

## 🚀 Reference Implementation

**Status:** Coming Q2 2026

We're building a vendor-neutral agent runtime that implements this architecture across Windows, macOS, Linux, and Android.

---

## 📋 Project Documents

| Document | Description |
|----------|-------------|
| [ROADMAP](ROADMAP.md) | Implementation timeline and milestones |
| [CONTRIBUTING](CONTRIBUTING.md) | How to contribute |
| [SECURITY](SECURITY.md) | Security policy and reporting |
| [GOVERNANCE](GOVERNANCE.md) | Project governance model |
| [DISCLAIMER](DISCLAIMER.md) | Legal disclaimers |
| [TRADEMARKS](TRADEMARKS.md) | Trademark notices |

---

## 🤝 Contributing

This architecture is published openly to become an industry standard. We welcome:

- Technical feedback and improvements
- Implementation examples
- Integration patterns for agent frameworks
- Real-world use case documentation

See [CONTRIBUTING](CONTRIBUTING.md) for guidelines.

---

## 📜 License

| Content | License |
|---------|---------|
| Documentation & Specification | [Creative Commons CC-BY 4.0](LICENSE-CC-BY) |
| Reference Implementation (when released) | [Apache 2.0](LICENSE) |

See [LICENSES](LICENSES.md) for complete licensing details.

---

## 🏢 About BluVi

Too many solutions are bandaids—security built for humans at the keyboard, retrofitted for AI. We've watched incident after incident unfold like a horror movie, everyone yelling "DON'T OPEN THE DOOR!" while shoving your mouth full of popcorn, unable to tear your eyes away from the actors doing just that.

Most recently example was OpenClaw. Did we really see 22K people fork OpenClaw to run on their own machines within 6 weeks? That's like watching 22K children with keys to the house and no parental supervision.

We decided to do something about it instead.

Zones of Distrust is our open contribution to help raise the bar for agentic AI security. 

We need new solutions. Let's build what works.

**Learn more:** [Zone of Distrust](https://bluvi.ai/zod)  

**GitHub:** [@bluvibytes](https://github.com/bluvibytes/zone-of-distrust)

**Contact:** security@bluvi.ai

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
