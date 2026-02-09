# ZoD Specifications

This folder contains normative specifications and machine-readable schemas.

## Contents

| File | Description | Status |
|------|-------------|--------|
| ZOD-SPEC-v0.9-RFC.md | Companion Specification (normative requirements) | RFC Draft |
| schemas/ | JSON Schema definitions | Draft - Not Yet Validated |

## Schemas

| Schema | Purpose |
|--------|---------|
| execution-token.schema.json | Execution token structure |
| integrity-event.schema.json | ISC event format |
| delegation-chain.schema.json | Multi-agent chain format |
| memory-event.schema.json | Memory audit event format |

**Note:** Schemas are draft and have not been validated against a reference implementation. See ROADMAP.md Milestone 1.
```

### 4. Review Schemas Against Spec

The spec (Section 9.3) says execution tokens MUST include:
```
token_id, issuer, subject_agent_id, action_type, target_resource,
parameter_binding, content_binding, issued_at, expires_at, nonce,
executor_id, signature
