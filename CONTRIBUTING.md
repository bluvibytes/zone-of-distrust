# Contributing to Zones of Distrust (ZoD)

Thanks for your interest in contributing to Zones of Distrust (ZoD). This project is security-focused and designed to encourage rigorous critique, adversarial testing, and practical implementation work.

ZoD is intended to be complementary to existing security standards and frameworks, not a replacement.

Security is hard. We assume good faith, welcome disagreement, and focus on improving defensibility through evidence and reproducible work.

---

## Code of Conduct

Participation in this project is governed by the Code of Conduct:

- See: `CODE_OF_CONDUCT.md`

By participating, you agree to follow these guidelines.

---

## Security Issues and Vulnerability Reports

If you believe you have found a security vulnerability, **do not open a public issue**.

Please report it privately via:

**security@bluvi.ai**

See: `SECURITY.md`

---

## What Contributions Are Welcome

We welcome contributions across documentation, specifications, test harnesses, reference implementations, and adversarial evaluation.

High-value contribution areas include:

### Specification and Architecture Feedback
- improvements to `specs/` requirements
- tightening normative language ("MUST", "SHOULD", "MAY")
- threat model refinements
- clarifying trust boundaries and assumptions
- identifying gaps or ambiguous enforcement rules

### Schemas and Interchange Formats
- JSON schema improvements (tokens, integrity events, delegation chains)
- schema validation test cases
- versioning strategy improvements

### Implementation Examples
We welcome examples of how ZoD is being implemented in real systems, including:

- agent framework integrations
- deployment patterns (Kubernetes, service mesh, sidecars, PEP gateways)
- security operations playbooks
- logging and correlation pipelines

### Reference Implementation
- CA (certificate authority / validator) reference service
- executor reference service
- policy evaluation engine integration examples
- replay prevention and nonce tracking implementations

### Adversarial Testing
- prompt injection test cases
- memory poisoning test cases
- token replay and substitution attacks
- multi-agent chain manipulation attacks
- executor bypass attempts

### Documentation Improvements
Help make the architecture more accessible:

- clarify technical explanations
- add diagrams and visualizations
- improve examples
- improve onboarding documentation

### Mappings and Ecosystem Alignment
- mappings to OWASP, NIST, MITRE ATLAS, CSA, ISO, SOC 2, etc.
- corrections to mapping claims or scope assumptions
- new framework crosswalks

---

## Where to Contribute (Issues, PRs, Discussions)

- **Issues:** open an issue to report problems, propose improvements, or identify gaps
- **Pull Requests:** submit concrete changes to documentation, specs, schemas, or code
- **Discussions:** use GitHub Discussions for broader architectural questions and design debates

---

## Contribution Guidelines

### Be Specific and Reproducible
Security contributions should include:
- clear threat model assumptions
- expected vs actual behavior
- reproducible steps or test harness references
- evidence artifacts (logs, traces, payloads)

### Avoid Overclaiming
ZoD prioritizes falsifiable security properties. Please avoid statements such as "ZoD prevents X" unless the claim is tied to an enforceable mechanism and testable requirement.

### Keep Contributions Scoped
Small, high-quality PRs are preferred over large changes that are hard to review.

---

## How to Submit Changes

1. Fork the repository
2. Create a feature branch
3. Make changes with clear commit messages
4. Open a pull request (PR)
5. Reference related issues if applicable

---

## Style and Formatting

- Use clear Markdown formatting for documentation changes
- Use consistent terminology across docs (ZoD layers, CA, executor, integrity channel)
- Use normative RFC language where appropriate:
  - MUST / MUST NOT
  - SHOULD / SHOULD NOT
  - MAY

---

## Licensing

This repository is dual-licensed:

- **Specification and documentation**: CC BY 4.0  
- **Code and reference implementations**: Apache 2.0  

See:
- `LICENSE`
- `LICENSE-CC-BY`
- `LICENSES.md`

By submitting a contribution, you agree that your contribution will be licensed under the applicable license for the file(s) modified.

---

## Project Direction and Governance

ZoD is an active research and engineering effort. Some proposals may be rejected if they conflict with core design principles or security assumptions.

If you are proposing a major change, open an issue first to discuss it.

---

## Getting Help / Contact

For general questions, open an issue or discussion.

For private security concerns, email:

**security@bluvi.ai**
