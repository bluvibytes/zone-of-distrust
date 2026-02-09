# ZoD JSON Schemas

Machine-readable JSON Schema definitions for Zones of Distrust data structures.

**Schema Version:** 0.9  
**JSON Schema Draft:** 2020-12  
**Status:** Draft — Not yet validated against reference implementation

---

## Schemas

| Schema | File | Spec Reference | Purpose |
|--------|------|----------------|---------|
| Execution Token | [execution-token.schema.json](execution-token.schema.json) | Section 9 | CA-issued capability tokens |
| Integrity Event | [integrity-event.schema.json](integrity-event.schema.json) | Section 13 | ISC event format |
| Delegation Chain | [delegation-chain.schema.json](delegation-chain.schema.json) | Section 14 | Multi-agent chain format |
| Memory Event | [memory-event.schema.json](memory-event.schema.json) | Section 12 | Memory audit events |

---

## Execution Token

**File:** `execution-token.schema.json`  
**Spec Reference:** ZOD-SPEC Section 9

Defines the structure for CA-issued execution tokens that authorize specific actions.

### Key Properties

| Property | Required | Description |
|----------|----------|-------------|
| `token_id` | ✓ | Unique token identifier |
| `issuer` | ✓ | CA identity |
| `subject_agent_id` | ✓ | Agent the token is issued for |
| `action` | ✓ | Action details (type, target, tool, bindings) |
| `issued_at` | ✓ | Issuance timestamp |
| `expires_at` | ✓ | Expiration timestamp (TTL enforcement) |
| `nonce` | ✓ | Single-use replay prevention |
| `executor_id` | ✓ | Authorized executor |
| `signature` | ✓ | Cryptographic signature (alg, kid, value) |
| `delegation_chain_id` | | Reference to delegation chain if multi-agent |
| `risk_score` | | CA-assessed risk (0-1) |

### Action Binding

Tokens include parameter and content binding per Sections 9.4-9.5:

```json
{
  "action": {
    "parameter_binding": {
      "binding_type": "digest",
      "digest": "sha256:abc123..."
    },
    "content_binding": {
      "binding_type": "hash",
      "hash": "sha256:def456..."
    },
    "mutable_fields": ["timestamp"]
  }
}
```

---

## Integrity Event

**File:** `integrity-event.schema.json`  
**Spec Reference:** ZOD-SPEC Section 13

Defines the structure for Integrity Signal Channel (ISC) events.

### Key Properties

| Property | Required | Description |
|----------|----------|-------------|
| `event_id` | ✓ | Unique event identifier |
| `timestamp` | ✓ | When event occurred |
| `source_layer` | ✓ | ZoD layer (1-7) emitting the event |
| `event_type` | ✓ | Event classification |
| `severity` | ✓ | debug, info, low, medium, high, critical |
| `data` | ✓ | Event payload |
| `priority` | | Queue priority for backpressure (normal, elevated, must_not_drop) |
| `correlation_id` | | Cross-layer correlation |

### Event Types

| Category | Events |
|----------|--------|
| **Token** | `token.issued`, `token.denied`, `token.replay_attempt`, `token.validation_failed`, `token.expired` |
| **Execution** | `execution.started`, `execution.succeeded`, `execution.failed`, `execution.mismatched_action`, `execution.orphaned_action` |
| **Policy** | `policy.changed`, `policy.evaluation` |
| **Monitor** | `monitor.drift_alert`, `monitor.goal_drift`, `monitor.baseline_update`, `monitor.anomaly` |
| **Memory** | `memory.event`, `memory.write`, `memory.poisoning_detected`, `memory.audit_completed` |
| **Key** | `key.rotated`, `key.accessed`, `key.compromise_indicator` |
| **CA** | `ca.failure`, `ca.recovery`, `ca.escalation`, `ca.approval_requested`, `ca.approval_granted`, `ca.approval_denied` |
| **ISC** | `isc.backpressure`, `isc.recovery`, `isc.overflow` |
| **Governance** | `governance.review_requested`, `governance.review_completed` |
| **Input** | `input.quarantined`, `input.flagged` |
| **Chain** | `chain.created`, `chain.extended`, `chain.completed`, `chain.rejected` |

### Priority Levels (Section 13.5)

| Priority | Usage |
|----------|-------|
| `normal` | Standard events |
| `elevated` | Important events, prioritized during backpressure |
| `must_not_drop` | Critical signals: key compromise, CA failure, validation failures |

---

## Delegation Chain

**File:** `delegation-chain.schema.json`  
**Spec Reference:** ZOD-SPEC Section 14

Defines the structure for multi-agent delegation chain validation.

### Key Properties

| Property | Required | Description |
|----------|----------|-------------|
| `chain_id` | ✓ | Unique chain identifier |
| `created_at` | ✓ | Chain initiation time |
| `links` | ✓ | Array of delegation hops |
| `max_chain_length` | | Policy limit on delegation depth |
| `cumulative_risk_score` | | Aggregated risk across links (0-1) |
| `status` | | active, completed, expired, revoked, rejected |

### Link Structure

Each link in the chain:

```json
{
  "index": 0,
  "from_agent_id": "agent-001",
  "to_agent_id": "agent-002",
  "timestamp": "2026-02-08T10:00:00Z",
  "delegation_scope": ["email.read", "calendar.read"],
  "risk_contribution": 0.2,
  "link_hash": "sha256:..."
}
```

### Chain Validation (Section 14.2)

The CA validates:
- Delegator authorization to delegate
- Delegate authorization to execute
- Chain length thresholds
- Cumulative risk scoring

---

## Memory Event

**File:** `memory-event.schema.json`  
**Spec Reference:** ZOD-SPEC Section 12

Defines the structure for memory audit events supporting poisoning detection.

### Key Properties

| Property | Required | Description |
|----------|----------|-------------|
| `event_id` | ✓ | Unique event identifier |
| `timestamp` | ✓ | When operation occurred |
| `agent_id` | ✓ | Agent performing operation |
| `operation` | ✓ | create, update, delete, read, snapshot, merge, expire |
| `content_digest` | ✓ | Hash of content (not raw content) |
| `source` | ✓ | Provenance information |
| `session_id` | | Cross-session tracking |
| `content_type` | | preference, fact, instruction, context, state, etc. |
| `risk_indicators` | | Poisoning detection flags |

### Risk Indicators (Section 12.3)

Supports detection of staged instruction fragments and poisoning:

```json
{
  "risk_indicators": {
    "contains_instructions": true,
    "references_tools": true,
    "references_targets": false,
    "scope_expansion_detected": false,
    "cross_session_pattern": true,
    "anomaly_score": 0.75,
    "flagged_patterns": ["tool_invocation", "multi_session_assembly"]
  }
}
```

### Source Provenance

```json
{
  "source": {
    "source_type": "tool_output",
    "source_id": "tool-xyz",
    "source_session": "session-123",
    "trust_level": "untrusted"
  }
}
```

---

## Validation

### Using JSON Schema Validators

```bash
# Node.js (ajv)
npx ajv validate -s execution-token.schema.json -d token.json

# Python (jsonschema)
python -m jsonschema -i token.json execution-token.schema.json
```

### Example: Valid Execution Token

```json
{
  "schema_version": "0.9",
  "token_id": "tok-abc123xyz",
  "request_id": "req-98765",
  "issuer": "ca-production-01",
  "subject_agent_id": "agent-assistant-main",
  "action": {
    "type": "email.send",
    "tool": "smtp-gateway",
    "target": "smtp://mail.example.com",
    "parameter_binding": {
      "binding_type": "digest",
      "digest": "sha256:a1b2c3d4e5f6..."
    },
    "content_binding": {
      "binding_type": "hash",
      "hash": "sha256:f6e5d4c3b2a1..."
    }
  },
  "risk_score": 0.3,
  "issued_at": "2026-02-08T10:00:00Z",
  "expires_at": "2026-02-08T10:05:00Z",
  "nonce": "nonce-unique-12345678",
  "executor_id": "executor-email-01",
  "signature": {
    "alg": "ES256",
    "kid": "ca-key-2026-02",
    "value": "MEUCIQDx...base64..."
  }
}
```

---

## Status & Roadmap

> **⚠️ Draft Status**  
> These schemas represent the intended structure per ZOD-SPEC v0.9 RFC but have not been validated against a reference implementation.

**Validation planned for:** Milestone 1 (ZoD-L1 Reference Implementation)

See [ROADMAP.md](../../ROADMAP.md) for implementation timeline.

---

## Related Documents

- [ZOD-SPEC-v0.9-RFC.md](../ZOD-SPEC-v0.9-RFC.md) — Companion Specification
- [Architecture](../../docs/ARCHITECTURE.md) — Full architecture specification
- [Security Properties](../../docs/security-properties.md) — Measurable security properties
- [Threat Model](../../docs/threat-model.md) — Attacker positions and trust assumptions

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
