# Review Log (RFC v0.9)

Zones of Distrust (ZoD) is published as an RFC-stage architecture and specification.  
This file tracks external review feedback, critique themes, and open questions raised by the community.

ZoD is intended to be complementary to existing security standards and frameworks, not a replacement.

---

## Review Status

**Current status:** Open for review  
**Version under review:** v0.9 (RFC)  
**Maintainer:** BluVi

If you are providing feedback, please reference the document name and section number when possible.

See: `OPEN_REVIEW.md`

---

## How to Submit Review Feedback

Feedback is welcome through:

- GitHub Issues (specific critiques, gaps, or questions)
- Pull Requests (proposed edits to docs/spec/schemas)
- GitHub Discussions (broader architectural debate)

If you believe you found a vulnerability in any reference implementation code, report it privately:

**security@bluvi.ai**

See: `SECURITY.md`

---

## Review Log Entries

> Review entries may be anonymized unless reviewers request attribution.

### Entry Template

**Date:** YYYY-MM-DD  
**Reviewer:** (Name or Anonymous)  
**Scope Reviewed:** (e.g., white paper, spec, schema, mappings)  
**Key Feedback Themes:**
- ...
- ...
- ...

**Action Taken / Status:**
- [ ] Addressed in vX.Y
- [ ] Planned
- [ ] Under discussion
- [ ] Disputed / requires further evidence

**Notes / Links:**
- (issue/PR link)

---

## Open Review Themes (Current)

This section lists major critique areas currently being requested from reviewers.

### Implementation Readiness
- What is the minimum viable reference implementation required for adoption?
- Which components should be built first: CA, executor, or attack harness?

### Token and Binding Model
- What fields must be bound into execution tokens?
- What replay prevention mechanisms are required in real deployments?

### Policy Enforcement Feasibility
- How should semantic intent enforcement be implemented realistically?
- What should ZoD-L2 require vs recommend?

### Monitoring and Memory Audit
- What memory formats should ZoD treat as first-class?
- What minimum provenance guarantees should exist for memory writes?

### Integrity Signal Correlation
- What integrity events are required for reliable correlation?
- What is the minimum viable append-only integrity channel design?

### Multi-Agent Delegation Chains
- How should identity and delegation be represented across agents?
- What existing identity standards should be reused?

### Compliance and Mapping Accuracy
- Are mappings overstated?
- What disclaimers should be applied to prevent misinterpretation?

---

## Reviewer Credit Policy

Reviewers who provide meaningful critique, test cases, or improvements may be credited in project acknowledgements, release notes, or documentation.

If you prefer anonymity, please indicate that in your submission.
