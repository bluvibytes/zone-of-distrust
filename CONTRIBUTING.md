# Contributing to Zones of Distrust (ZoD)

Thank you for your interest in contributing to **Zones of Distrust (ZoD)**.

ZoD is an open reference architecture for securing autonomous AI agents in production. It is intentionally security-first, adversarially informed, and grounded in enforceable architectural boundaries rather than trust in agent behavior.

ZoD is designed to be **complementary** to existing security standards and frameworks—not a replacement.

Security is hard. We assume good faith, welcome disagreement, and prioritize defensibility through **evidence, reproducibility, and clarity**.

---

## Code of Conduct

Participation in this project is governed by the Code of Conduct.

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

We welcome contributions across documentation, specifications, schemas, reference implementations, and adversarial testing.

High-value contribution areas include:

---

### Specification and Architecture Feedback

- Improvements to requirements in `specs/`
- Tightening normative language (`MUST`, `SHOULD`, `MAY`)
- Threat model refinements
- Clarifying trust boundaries and assumptions
- Identifying ambiguous enforcement behavior or edge cases

---

### Schemas and Interchange Formats

- JSON schema improvements (execution tokens, integrity events, delegation chains)
- Schema validation test cases
- Backward-compatibility and versioning strategies

---

### Reference and Example Implementations

We welcome examples of how ZoD is implemented in real systems, including:

- Agent framework integrations
- Deployment patterns (Kubernetes, service mesh, sidecars, PEP gateways)
- Certificate Authority (validator) services
- Executor services
- Policy evaluation integrations
- Replay prevention and nonce-tracking mechanisms
- Logging and correlation pipelines

---

### Adversarial Testing

We explicitly welcome adversarial contributions, including:

- Prompt injection test cases
- Memory and RAG poisoning scenarios
- Token replay, substitution, or scope escalation attempts
- Multi-agent chain manipulation
- Executor bypass attempts

Adversarial tests may be **minimal proof-of-concept demonstrations**. They do not need to be production-grade exploits, but they **must be reproducible and clearly scoped**.

---

### Documentation Improvements

Help make the architecture more accessible and precise:

- Clarify technical explanations
- Add diagrams or visualizations
- Improve examples
- Improve onboarding documentation

---

### Mappings and Ecosystem Alignment

- Mappings to OWASP, NIST, MITRE ATLAS, CSA, ISO, SOC 2, etc.
- Corrections to mapping claims or scope assumptions
- New framework crosswalks

---

## Where to Contribute

- **Issues** — report problems, propose improvements, or identify gaps
- **Pull Requests** — submit concrete changes to documentation, specs, schemas, or code
- **Discussions** — architectural questions, design debates, and exploratory ideas

---

## Contribution Guidelines

### Be Specific and Reproducible

Security contributions should include, where applicable:

- Clear threat model assumptions
- Expected vs. actual behavior
- Reproducible steps or test harness references
- Evidence artifacts (logs, traces, payloads)

---

### Avoid Overclaiming

ZoD prioritizes **falsifiable security properties**.

Please avoid statements such as *“ZoD prevents X”* unless the claim is explicitly tied to:
- an enforceable mechanism, and
- a testable requirement.

---

### Keep Contributions Scoped

Small, high-quality pull requests are preferred over large, hard-to-review changes.

---

## How to Submit Changes

1. Fork the repository
2. Create a feature branch
3. Make changes with clear, scoped commits
4. Open a pull request (PR)
5. Reference related issues or discussions where applicable

---

## Pull Request Review Criteria

For a pull request to be accepted, it should clearly demonstrate **at least one** of the following:

- Improved clarity or correctness of a security requirement
- A new or refined enforceable security property
- A reproducible adversarial test case
- A concrete implementation pattern aligned with ZoD assumptions

Changes that affect security behavior should include corresponding documentation or specification updates where appropriate.

---

## Style and Formatting

- Use clear Markdown formatting for documentation changes
- Use consistent terminology across documents (ZoD layers, CA, executor, integrity channel)
- Use normative RFC language where applicable:
  - **MUST / MUST NOT**
  - **SHOULD / SHOULD NOT**
  - **MAY**

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

ZoD is an active research and engineering effort.

### Decision Authority

Final design and acceptance decisions are made by the ZoD maintainers.

While rigorous disagreement and adversarial review are encouraged, changes must align with ZoD’s core security assumptions, threat model, and architectural enforcement boundaries. In cases of unresolved disagreement, maintainers prioritize architectural coherence, enforceability, and testability over consensus.

If you are proposing a major change, please open an issue or discussion first.

---

## Getting Help / Contact

For general questions, open an issue or discussion.

For private security concerns, contact:

**security@bluvi.ai**
