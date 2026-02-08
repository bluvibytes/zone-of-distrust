# Open Review and External Feedback

Zones of Distrust (ZoD) is published as an RFC-stage security architecture and specification.  
This repository explicitly welcomes adversarial critique and technical review.

ZoD is intended to be complementary to existing security standards and frameworks, not a replacement.

Review is welcome from researchers, practitioners, engineers, students, and anyone able to provide rigorous technical critique.

---

## What We Are Asking Reviewers to Evaluate

We welcome review and critique of:

- architectural trust boundaries and layer definitions
- execution token model (binding, replay prevention, signing, TTL semantics)
- CA validation assumptions and failure modes
- executor enforcement requirements and bypass possibilities
- integrity signal channel design and correlation requirements
- monitoring and memory audit assumptions
- multi-agent delegation chain validation
- schema clarity and versioning strategy
- compliance levels (ZoD-L1 through ZoD-L5)
- mapping accuracy (OWASP, NIST, MITRE ATLAS, CSA, ENISA, etc.)

---

## How to Submit Feedback

Feedback is welcome via:

- GitHub Issues (specific technical concerns or gaps)
- Pull Requests (proposed edits to specs/docs/schemas)
- GitHub Discussions (broader architectural debate)

If you believe you found a security vulnerability in any reference implementation code, please report it privately:

**security@bluvi.ai**

See: `SECURITY.md`

---

## What Makes Feedback High Value

The most useful feedback includes:

- a clear claim or critique
- the assumptions being challenged
- reproducible examples or attack scenarios
- suggested mitigations or alternative designs
- references to relevant prior work where applicable

---

## Attribution and Credit

Significant contributors and reviewers may be credited in project acknowledgements, release notes, or related documentation.

If you prefer anonymity, please indicate that in your submission.

---

## Scope Note

ZoD is an architecture and enforcement model. It does not claim to solve all AI safety or AI alignment problems.

ZoD assumes reasoning systems can be manipulated and focuses on preventing unauthorized execution despite that manipulation.
