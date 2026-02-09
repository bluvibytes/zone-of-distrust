# Zones of Distrust (ZoD) Companion Specification

**Core Requirements and Interfaces for Agent Execution Security**

| Field | Value |
|-------|-------|
| Version | 0.9 (RFC) |
| Date | February 2026 |
| Status | RFC Draft for Review |
| Publisher | BluVi |
| Associated White Paper | Zones of Distrust v0.9 RFC |

---

## 1. Purpose

This specification defines the normative requirements, interfaces, schemas, and compliance criteria required to implement the Zones of Distrust (ZoD) architecture.

Zones of Distrust is designed to secure autonomous AI agents operating with real access to enterprise systems under the assumption that the agent may be compromised through adversarial input without being aware of the compromise.

This specification exists to enable:

- Interoperable implementations
- Verifiable security claims
- Automated compliance testing
- Adversarial evaluation by independent researchers
- Open source collaboration on shared primitives

---

## 2. Conformance Language

The key words MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY in this document are to be interpreted as described in RFC 2119.

---

## 3. Definitions

### 3.1 Agent

An Agent is a software entity that performs autonomous reasoning and generates structured requests to execute actions in external systems.

### 3.2 Reasoning Process

A Reasoning Process is the runtime component that performs inference, planning, and decision-making. It MUST NOT directly execute privileged operations.

### 3.3 Executor

An Executor is a component responsible for performing actions in external systems (e.g., email send, database query, API call). Executors MUST enforce ZoD execution token requirements.

### 3.4 Certificate Authority (CA)

The CA is the independent validation and authorization service that evaluates agent requests and issues execution tokens.

The CA is conceptually equivalent to a capability-issuing authority, not a credential proxy.

### 3.5 Policy Decision Point (PDP)

The PDP is the policy engine that evaluates authorization, semantic intent constraints, data classification constraints, and risk thresholds.

### 3.6 Policy Enforcement Point (PEP)

The PEP is the enforcement boundary that ensures agent actions cannot bypass the CA. The PEP may exist as a sidecar proxy, service mesh boundary, kernel control, or API gateway.

### 3.7 Execution Token

An Execution Token is a cryptographically signed, time-bound, single-use capability certificate issued by the CA authorizing exactly one action with bounded parameters.

### 3.8 Integrity Signal Channel (ISC)

The Integrity Signal Channel is an append-only, immutable logging channel that records security events emitted by each ZoD layer, independent of the agent's execution control plane.

### 3.9 Memory Store

The Memory Store is any persistent state store used by an agent across sessions, including explicit agent memory, retrieval stores, long-term state, user preference stores, or RAG indices.

### 3.10 Memory Audit

A Memory Audit is an independent analysis of memory store contents and evolution over time, designed to detect poisoning, staged payloads, drift, and unauthorized persistence.

### 3.11 Delegation Chain

A Delegation Chain is a structured record of multi-agent request propagation where one agent's output results in another agent's action request.

---

## 4. Security Model Overview (Normative)

A ZoD-compliant implementation MUST satisfy the following structural requirements:

1. The reasoning process MUST be separated from execution.
2. The agent MUST NOT possess standing execution credentials.
3. All execution MUST be mediated through CA-issued execution tokens.
4. Execution tokens MUST be parameter-bound and enforced at the executor.
5. All execution events MUST be logged immutably.
6. Behavioral drift and memory poisoning MUST be detectable through external monitoring.
7. Multi-agent request chains MUST be validated as chains, not as isolated events.

---

## 5. Threat Model and Assumptions

### 5.1 In-Scope Attacker Capabilities

ZoD is designed to withstand adversaries capable of:

- Crafting arbitrary untrusted content for the agent to process
- Embedding prompt injection instructions in external content
- Poisoning retrieval stores (RAG) or persistent memory
- Manipulating tool output channels (reality poisoning)
- Attempting token replay or token misuse
- Abusing multi-agent delegation chains
- Compromising signing keys, KMS/HSM, or token issuance infrastructure
- Statistical inference of policy boundaries through repeated probing
- Social engineering human reviewers through fabricated evidence or artificial urgency
- Context shaping attacks that bias reasoning without explicit injection (soft steering)

### 5.2 Trust Assumptions

ZoD assumes:

- The CA is not directly controllable by the agent runtime
- Executors enforce token validation correctly
- The integrity signal channel is append-only and tamper-resistant
- CA signing keys are protected by HSM or equivalent controls
- Network segmentation (L1) prevents direct agent-to-tool paths
- Human reviewers act in good faith and are not compromised
- Infrastructure monitoring (L6) operates independently of agent control

### 5.3 Explicit Non-Goals

ZoD does not attempt to:

- Make LLMs non-injectable
- Prevent adversarial influence on reasoning
- Guarantee detection of all supply chain poisoning attacks
- Prevent compromise under full OS kernel takeover

### 5.4 Degradation Assumptions

If any trust assumption fails, the following degradation rules apply:

| Component Failure | Degradation Behavior | Severity |
|-------------------|----------------------|----------|
| L4 (CA) failure | All execution MUST halt; no fail-open | **Critical** |
| L3 (Cognitive Isolation) failure | Containment holds ONLY IF L1 network segmentation enforces no privileged paths AND L5 rejects unauthenticated requests | **Critical** |
| L6 (Monitoring) failure | External health checks MUST detect outage; L4 MUST escalate scrutiny when monitoring is blind | High |
| L2 (Input Control) failure | Agent operates on unscreened input; L4 MUST apply elevated scrutiny | High |
| L1 (OS Foundation) failure | No ZoD guarantees hold; assume full compromise | **Critical** |

Implementations MUST document degradation behavior for each layer failure mode.

---

## 6. Layer 2 Requirements: Input Control

### 6.1 Pre-Reasoning Screening

All external inputs MUST pass through an input screening boundary before reaching the reasoning process.

External inputs include:

- Emails
- Documents
- Web content
- Calendar invites
- Tool outputs
- RAG retrieval outputs
- Inter-agent messages

### 6.2 Output Requirements

The input control system MUST output:

- Sanitized content (if permitted)
- Metadata tags describing risk classification
- Confidence scores
- Optional quarantine status

### 6.3 Degraded Mode Requirements

The input control system MUST support at least one degraded mode that preserves operational continuity.

At minimum, a compliant system MUST support:

- Quarantine-with-summary, OR
- Tagging mode (deliver but flag)

Degraded mode selection MUST be controlled by governance policy and MUST NOT be controlled by the agent.

---

## 7. Layer 3 Requirements: Cognitive Isolation

### 7.1 Execution Isolation

The reasoning process MUST NOT have network access to external services required for execution (e.g., SMTP, database endpoints, privileged APIs).

### 7.2 Structured Request Output

The reasoning process MUST output execution intent as structured requests rather than direct API calls.

Structured requests MUST include:

- Action type
- Target resource
- Parameter payload
- Contextual metadata (source, reason, confidence)

### 7.3 Enforced Boundary

The separation MUST be enforced by architecture (network policy, sandboxing, service mesh boundaries, kernel enforcement), not solely by application logic.

---

## 8. Layer 4 Requirements: Request Validation (CA)

### 8.1 Mandatory Validation Criteria

The CA MUST evaluate each request against at least:

- Authorization Scope
- Semantic Intent Policy
- Risk Threshold Policy
- Behavioral Baseline Context
- Multi-Agent Chain Context (if applicable)
- Goal Integrity Context (see 8.5)

### 8.2 Denial Behavior

If a request fails validation, the CA MUST:

- Deny issuance of the execution token
- Log denial to the integrity signal channel
- Optionally trigger escalation depending on policy

The CA MUST NOT provide the agent with detailed denial reasons if that would reveal policy boundaries.

Denial responses SHOULD be opaque and MAY include jittered timing to resist probing attacks.

### 8.3 Escalation

The CA MUST support escalation workflows for high-risk actions, including:

- Hold-for-approval
- Deny-and-escalate
- Allow-with-monitoring

### 8.4 Fast-Path Processing

The CA SHOULD support deterministic fast-path rules for routine, low-risk requests.

Fast-path eligible requests:

- MUST match pre-approved action patterns
- MUST fall within established behavioral baseline
- MUST NOT exceed risk threshold for automatic approval
- MUST NOT involve sensitive targets or elevated privileges

Fast-path processing reduces latency without bypassing validation. All fast-path decisions MUST still be logged to the ISC.

### 8.5 Goal Integrity Validation

The CA SHOULD validate requests against original declared goals, not goals that may have evolved during agent operation.

This prevents optimization pressure exploits where incremental goal drift leads to unauthorized actions.

Goal integrity validation includes:

- Comparing current request patterns against session-start objectives
- Detecting escalating autonomy or reduced confirmation-seeking
- Flagging requests that exceed original scope boundaries

---

## 9. Execution Token Requirements

### 9.1 Cryptographic Requirements

Execution tokens MUST:

- Be cryptographically signed
- Be verifiable by executors
- Include issuer identity
- Include a unique nonce

**Key Protection Requirements:**

- Signing keys MUST be protected by HSM or equivalent hardware security controls
- Key rotation schedules MUST be defined and enforced
- Compromise of signing keys MUST trigger emergency revocation procedures
- Key access MUST require dual-control for administrative operations

### 9.2 Mandatory Token Properties

Each execution token MUST be:

- Single-use
- Time-bound
- Parameter-bound
- Replay-resistant
- Executor-bound

### 9.3 Mandatory Token Fields

Each token MUST include:

| Field | Description |
|-------|-------------|
| `token_id` | Unique identifier for this token |
| `issuer` | CA identity that issued the token |
| `subject_agent_id` | Agent identity the token is issued to |
| `action_type` | Type of action authorized |
| `target_resource` | Resource the action targets |
| `parameter_binding` | Hash or constraints for parameters |
| `content_binding` | Hash or constraints for payload content |
| `issued_at` | Timestamp of issuance |
| `expires_at` | Expiration timestamp |
| `nonce` | Unique value for replay prevention |
| `executor_id` | Executor authorized to consume this token |
| `signature` | Cryptographic signature over all fields |

### 9.4 Parameter Binding

Parameter binding MUST ensure that the token authorizes only the specific action instance approved.

### 9.5 Content Binding

Tokens MUST include either:

- A cryptographic hash of the payload, OR
- Declared constraints defining permitted payload variance

Constraints MUST be explicit and machine-evaluable.

### 9.6 Mutable Fields

Tokens MUST explicitly define which fields may vary between CA approval and execution.

All non-declared fields MUST be immutable by default.

### 9.7 Replay Resistance

Tokens MUST include a nonce.

Executors MUST enforce nonce single-use semantics.

Token replay attempts MUST be logged as high-severity compromise indicators.

---

## 10. Layer 5 Requirements: Execution

### 10.1 Executor Token Validation

Executors MUST validate:

- Signature validity
- Token expiration
- Nonce unused
- Executor identity match
- Parameter binding match
- Content binding match

Execution MUST NOT occur unless validation succeeds.

### 10.2 Orphaned Action Detection

Executors MUST detect and log execution requests lacking valid tokens.

### 10.3 Mismatched Action Detection

Executors MUST detect and log requests where payload differs from approved content binding.

### 10.4 Execution Logging Requirements

Executors MUST log:

- Token ID
- Timestamp
- Action performed
- Result status
- Target identifiers
- Validation outcomes

All execution logs MUST be written to the integrity signal channel.

---

## 11. Semantic Intent Policy Requirements

### 11.1 Policy Structure

Semantic intent policies MUST be tool-specific and MUST include enforceable negative constraints.

### 11.2 Enforcement Location

Semantic intent policy MUST be enforced at the CA and SHOULD be re-validated at execution time for high-risk tools.

### 11.3 Classification and Egress Control

A ZoD-compliant system SHOULD include content classification enforcement.

If implemented, the CA MUST prevent issuance of tokens that would allow egress of content above the destination clearance level.

### 11.4 Policy Opacity

Policy details SHOULD NOT be exposed to the reasoning agent.

This prevents:

- Policy boundary learning through probing
- Crafted requests designed to find policy gaps
- Optimization against known constraints

---

## 12. Layer 6 Requirements: Monitoring and Memory Audit

### 12.1 Behavioral Baselining

A monitoring system MUST maintain a behavioral baseline per agent identity.

Baselines MUST include at minimum:

- Tool usage distribution
- Request frequency patterns
- Time-of-day usage patterns
- Target distribution

### 12.2 Drift Detection

The monitoring system MUST generate alerts when drift exceeds configured thresholds.

### 12.3 Memory Audit

If persistent memory exists, an external memory audit MUST be performed.

Memory audit MUST include detection attempts for:

- Staged instruction fragments
- Persistent preference poisoning
- Gradual scope expansion
- Cross-session payload assembly

### 12.4 Baseline Integrity Verification

The monitoring system MUST monitor drift of its own baseline models over time.

Rate-of-change limits SHOULD be implemented to prevent baseline poisoning.

### 12.5 Goal Drift Detection

The monitoring system SHOULD detect goal drift patterns where agent objectives evolve incrementally from declared goals.

Drift indicators include:

- Escalating autonomy requests
- Reduced confirmation-seeking behavior
- Expanded scope of actions over time
- Increased frequency of high-risk action requests
- Shifting target distribution toward sensitive resources

Goal drift alerts MUST be logged to the ISC and MAY trigger CA policy escalation.

---

## 13. Integrity Signal Channel (ISC) Requirements

### 13.1 Immutability

ISC logs MUST be append-only and tamper-resistant.

### 13.2 Out-of-Band Separation

ISC infrastructure MUST be logically separate from the agent runtime.

### 13.3 Required Event Classes

ISC MUST record at minimum:

| Event Class | Description |
|-------------|-------------|
| Token issuance | CA issued an execution token |
| Token denial | CA denied a request |
| Token replay attempt | Executor detected replay |
| Execution success/failure | Action completed or failed |
| Mismatched payload | Content binding violation |
| Orphaned action | Execution without valid token |
| Monitoring alerts | Drift, anomaly, or baseline alerts |
| Policy changes | Policy updates or modifications |
| Key events | Rotation, access, compromise indicators |
| Goal drift alerts | Detected goal evolution |

### 13.4 Correlation Requirement

ZoD implementations SHOULD support correlation queries between:

- CA issuance vs executor execution
- Memory drift vs execution anomalies
- Multi-agent chain propagation vs policy violations

### 13.5 Backpressure Handling

ISC implementations MUST define backpressure behavior when log ingestion is slower than event generation.

Requirements:

- Buffer with priority queue (security events prioritized over operational events)
- Separate "must not drop" channel for critical signals:
  - Key compromise indicators
  - Token validation failures
  - CA failures
  - Orphaned action detections
- Overflow MUST trigger alerts, not silent drops
- Backpressure events MUST themselves be logged when capacity recovers

---

## 14. Multi-Agent Chain Validation Requirements

### 14.1 Chain Representation

Requests originating from agent-to-agent delegation MUST include a delegation chain object.

### 14.2 Chain Validation Rules

The CA MUST validate:

- Delegator authorization to delegate
- Delegate authorization to execute
- Chain length thresholds
- Cumulative risk scoring across chain

### 14.3 Collective Abuse Detection

The CA SHOULD support detection of collective abuse patterns where multiple low-risk actions aggregate into a high-risk outcome.

---

## 15. Audit and Logging Requirements

### 15.1 Mandatory Audit Data

Systems MUST retain:

- CA decision logs
- Token issuance records
- Executor action logs
- Policy version history
- Escalation decisions
- Drift alerts

### 15.2 Retention

Implementations MUST define a retention period and SHOULD default to at least 180 days.

---

## 16. Compliance Levels (Normative)

### 16.1 ZoD Level 1 (ZoD-L1): Tokenized Execution Enforcement

Requires:

- CA token issuance
- Executor token validation
- TTL + nonce enforcement
- Parameter binding enforcement

### 16.2 ZoD Level 2 (ZoD-L2): Semantic Intent Policy

Requires:

- Tool-specific semantic constraints
- Machine-evaluable policy engine integration
- Policy opacity (policy not exposed to agent)

### 16.3 ZoD Level 3 (ZoD-L3): Monitoring and Memory Audit

Requires:

- Drift detection
- Memory audit processes
- Baseline integrity verification
- Goal drift detection

### 16.4 ZoD Level 4 (ZoD-L4): Integrity Signal Channel

Requires:

- Append-only ISC
- Cross-layer correlation capability
- Backpressure handling

### 16.5 ZoD Level 5 (ZoD-L5): Multi-Agent Chain Validation

Requires:

- Delegation chain schema
- Chain-level validation logic
- Collective abuse detection attempts

---

## 17. Testability Requirements

A compliant implementation MUST provide automated tests validating:

- Token expiration enforcement
- Replay rejection
- Parameter mismatch rejection
- Denial logging correctness
- Policy enforcement correctness

ZoD Level 2+ systems SHOULD provide tests validating:

- Policy boundary opacity (denial reasons do not leak policy details)
- Probing detection (repeated boundary-testing triggers alerts)
- Rate limiting effectiveness against statistical inference

ZoD Level 3+ systems MUST provide tests validating:

- Drift detection triggers
- Memory poisoning signature detection attempts
- Goal drift detection accuracy

---

## 18. References (Informative)

- RFC 2119: Key words for use in RFCs to Indicate Requirement Levels
- OWASP Top 10 for Large Language Model Applications (2025)
- OWASP Agentic Security Initiative
- NIST AI Risk Management Framework
- MITRE ATLAS (Adversarial Threat Landscape for AI Systems)
- CSA AI Technology and Risk Task Force
- Zones of Distrust White Paper v0.9 RFC

---

## Appendix A: Schema References

Machine-readable schemas for ZoD data structures are provided in the `schemas/` directory:

| Schema | File | Description |
|--------|------|-------------|
| Execution Token | `execution-token.schema.json` | Token structure per Section 9 |
| Integrity Event | `integrity-event.schema.json` | ISC event format per Section 13 |
| Delegation Chain | `delegation-chain.schema.json` | Chain structure per Section 14 |
| Memory Event | `memory-event.schema.json` | Memory audit event format |

**Schema Status:** Draft — Not yet validated against reference implementation.

---

## Appendix B: Document History

| Version | Date | Changes |
|---------|------|---------|
| 0.9 RFC | February 2026 | Initial draft |

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
