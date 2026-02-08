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
- Are Layers 4
