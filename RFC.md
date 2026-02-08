# RFC: Zones of Distrust (ZoD) v0.9

Zones of Distrust (ZoD) is a layered security architecture for autonomous AI agents focused on separating reasoning from execution and enforcing runtime governance of privileged actions.

This repository is published as **RFC v0.9** to invite peer review, adversarial critique, and real-world implementation feedback.

ZoD is intended to be **complementary** to existing security standards and frameworks, not a replacement.

---

## RFC Status

This is an early public release intended for open review.

- The architecture is stable enough for critique and prototyping.
- The specification and schemas are still evolving.
- Reference implementations are incomplete and should be treated as experimental.

Breaking changes may occur prior to v1.0.

---

## What This RFC Contains

This RFC release includes:

- ZoD white paper and supporting documentation
- ZoD companion specification (normative requirements)
- security properties and threat model
- JSON schemas for core artifacts (tokens, integrity events, delegation chains, memory events)
- ecosystem mappings (OWASP, NIST, MITRE ATLAS, Cisco, CSA, ENISA, CoSAI, SAIF, ISO/SOC2, etc.)
- early reference implementation scaffolding

---

## What This RFC Is Asking Reviewers to Evaluate

We specifically want critique on whether ZoD is defensible, implementable, and meaningfully improves agent security.

Key questions:

### Architecture Validity
- Are the ZoD trust boundaries correct?
- Does the separation of reasoning and execution reduce real-world agent risk?
- Are Layers 4–5 defined clearly enough to prevent ambiguity?

### Execution Token Model
- Is the execution token design sufficient to prevent replay, substitution, and parameter tampering?
- What should be bound into tokens (parameters, intent, context, chain metadata)?
- What failure modes are not covered by token binding?

### Policy Enforcement
- Is ZoD-L2 semantic intent enforcement feasible at scale?
- Can policies remain enforceable without becoming brittle allowlists?
- Is "policy opacity" a valid security property or an operational risk?

### Memory Audit and Baseline Integrity
- Is memory audit defined clearly enough to be actionable?
- What minimum provenance requirements should exist for memory writes?
- Is baseline integrity verification realistic, and what metrics should be standardized?

### Integrity Signal Correlation
- What events must be emitted to enable reliable correlation?
- Can integrity logging be made tamper-resistant in real deployments?
- What is the minimum viable integrity signal channel implementation?

### Multi-Agent Delegation Chains
- Are delegation chain schemas sufficient to prevent chain abuse?
- How should chain risk scoring be defined?
- What are the strongest known multi-agent escalation patterns that ZoD must address?

---

## Out of Scope (For v0.9)

ZoD does not claim to solve all AI security problems.

The following are considered out of scope unless explicitly addressed:

- model training security and dataset provenance
- model extraction prevention
- hardware enclave security guarantees
- full OS-level agent isolation primitives
- general AI alignment and value alignment problems
- prevention of hallucination (ZoD focuses on constraining hallucinated execution)

ZoD assumes standard security practices exist for:
- IAM and identity management
- TLS and key management
- OS hardening
- network segmentation
- dependency and supply chain security

---

## What Would Make This RFC "Successful"

This RFC is considered successful if it produces:

- actionable critiques that improve the spec
- adversarial test cases that challenge ZoD assumptions
- implementable reference patterns for CA + executor enforcement
- stronger schema definitions for tokens, integrity events, and memory events
- early adopters who prototype ZoD-L1 or ZoD-L2 deployments
- contributors who expand the attack harness and compliance mappings

---

## How to Provide Feedback

We welcome feedback through:

- GitHub Issues (architecture critiques, schema issues, policy gaps)
- Pull Requests (documentation/spec improvements, test cases, schemas)
- GitHub Discussions (broader debates and design questions)

If you believe you found a security vulnerability in any reference implementation code,
please report it privately:

**security@bluvi.ai**

See: `SECURITY.md`

---

## Versioning

This repository is released as **ZoD v0.9**.

Breaking changes may occur prior to v1.0 as peer review and implementation feedback is incorporated.

---

## Attribution and Licensing

ZoD is dual-licensed:

- specification and documentation: **CC BY 4.0**
- code and reference implementation: **Apache 2.0**

See:
- `LICENSE`
- `LICENSE-CC-BY`
- `LICENSES.md`

If you use this work in research or publication, please cite using `CITATION.cff`.
