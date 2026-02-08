# Governance

Zones of Distrust (ZoD) is an open security architecture and specification project maintained by **BluVi**.

This document defines how decisions are made, how contributions are reviewed, and how the project evolves over time.

ZoD is intended to be complementary to existing security standards and frameworks, not a replacement.

---

## Project Goals

ZoD exists to:

- define a layered security architecture for autonomous AI agents
- provide enforceable security boundaries (reasoning vs execution)
- define falsifiable security properties and compliance levels
- enable reproducible adversarial testing and validation
- support open review and collaborative improvement

---

## Maintainers

The project is currently maintained by:

- **BluVi** (primary maintainer)

Maintainers are responsible for:

- approving or rejecting pull requests
- defining project direction and roadmap
- managing releases and versioning
- handling security disclosures and coordinated fixes
- ensuring documentation consistency and quality

---

## Decision Making

ZoD uses a maintainer-led governance model.

- Maintainers have final authority on merges and project direction.
- Major architectural changes should be proposed through issues or discussions before implementation.
- Decisions are guided by security defensibility, clarity, and practical deployability.

Where possible, maintainers will seek community feedback before finalizing significant changes.

---

## Contribution Review Process

All contributions are reviewed through pull requests.

Review criteria include:

- technical correctness and security impact
- clarity and reproducibility
- alignment with ZoD layer definitions and security properties
- avoidance of overclaiming or unverifiable guarantees
- compatibility with the existing spec and roadmap

Maintainers may request revisions before merging.

---

## Specification Evolution

ZoD is expected to evolve through open critique and implementation feedback.

Changes to the specification should:

- be clearly justified
- include rationale and threat model context
- use normative RFC language where applicable (MUST, SHOULD, MAY)
- avoid ambiguous enforcement requirements

Breaking changes may occur during pre-1.0 versions.

---

## Versioning

ZoD follows semantic versioning principles where practical:

- **0.x releases**: active development; breaking changes may occur
- **1.0+ releases**: stable specification baseline; breaking changes require major version increments

---

## Security Disclosures

ZoD is a security-focused project. Vulnerabilities must be reported privately.

See: `SECURITY.md`

Security issues are coordinated through maintainers and handled with responsible disclosure practices.

---

## Conflicts of Interest

ZoD is maintained by BluVi. BluVi may implement ZoD commercially and may make decisions that preserve architectural integrity, attribution requirements, and long-term project stability.

This project welcomes community participation while maintaining consistent stewardship of the specification.

---

## Community Standards

All participation is governed by:

- `CODE_OF_CONDUCT.md`

Harassment, abuse, or bad-faith participation will not be tolerated.

---

## Changes to Governance

Changes to this governance model may be proposed through issues or pull requests, but final approval remains with the maintainers.
