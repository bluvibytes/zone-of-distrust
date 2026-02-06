# Zones of Distrust (ZoD) Companion Specification
## Core Requirements and Interfaces for Agent Execution Security

**Version:** 0.1 (Draft)  
**Date:** February 2026  
**Status:** Draft for Review  
**Publisher:** BluVi  
**Associated White Paper:** Zones of Distrust v3.1

---

## 1. Purpose

This specification defines the normative requirements, interfaces, schemas, and compliance criteria required to implement the Zones of Distrust (ZoD) architecture.

Zones of Distrust is designed to secure autonomous AI agents operating with real access to enterprise systems under the assumption that the agent may be compromised through adversarial input without being aware of the compromise.

This specification exists to enable:

- interoperable implementations
- verifiable security claims
- automated compliance testing
- adversarial evaluation by independent researchers
- open source collaboration on shared primitives

---

## 2. Conformance Language

The key words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** in this document are to be interpreted as described in RFC 2119.

---

## 3. Definitions

### 3.1 Agent
An **Agent** is a software entity that performs autonomous reasoning and generates structured requests to execute actions in external systems.

### 3.2 Reasoning Process
A **Reasoning Process** is the runtime component that performs inference, planning, and decision-making. It MUST NOT directly execute privileged operations.

### 3.3 Executor
An **Executor** is a component responsible for performing actions in external systems (e.g., email send, database query, API call). Executors MUST enforce ZoD execution token requirements.

### 3.4 Certificate Authority (CA)
The **CA** is the independent validation and authorization service that evaluates agent requests and issues execution tokens.

The CA is conceptually equivalent to a capability-issuing authority, not a credential proxy.

### 3.5 Policy Decision Point (PDP)
The **PDP** is the policy engine that evaluates authorization, semantic intent constraints, data classification constraints, and risk thresholds.

### 3.6 Policy Enforcement Point (PEP)
The **PEP** is the enforcement boundary that ensures agent actions cannot bypass the CA. The PEP may exist as a sidecar proxy, service mesh boundary, kernel control, or API gateway.

### 3.7 Execution Token
An **Execution Token** is a cryptographically signed, time-bound, single-use capability certificate issued by the CA authorizing exactly one action with bounded parameters.

### 3.8 Integrity Signal Channel (ISC)
The **Integrity Signal Channel** is an append-only, immutable logging channel that records security events emitted by each ZoD layer, independent of the agent’s execution control plane.

### 3.9 Memory Store
The **Memory Store** is any persistent state store used by an agent across sessions, including explicit agent memory, retrieval stores, long-term state, user preference stores, or RAG indices.

### 3.10 Memory Audit
A **Memory Audit** is an independent analysis of memory store contents and evolution over time, designed to detect poisoning, staged payloads, drift, and unauthorized persistence.

### 3.11 Delegation Chain
A **Delegation Chain** is a structured record of multi-agent request propagation where one agent’s output results in another agent’s action request.

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

- crafting arbitrary untrusted content for the agent to process
- embedding prompt injection instructions in external content
- poisoning retrieval stores (RAG) or persistent memory
- manipulating tool output channels
- attempting token replay or token misuse
- abusing multi-agent delegation chains

### 5.2 Trust Assumptions
ZoD assumes:

- the CA is not directly controllable by the agent runtime
- executors enforce token validation correctly
- the integrity signal channel is append-only and tamper-resistant

### 5.3 Explicit Non-Goals
ZoD does not attempt to:

- make LLMs non-injectable
- prevent adversarial influence on reasoning
- guarantee detection of all supply chain poisoning attacks
- prevent compromise under full OS kernel takeover

---

## 6. Layer 2 Requirements: Input Control

### 6.1 Pre-Reasoning Screening
All external inputs MUST pass through an input screening boundary before reaching the reasoning process.

External inputs include:

- emails
- documents
- web content
- calendar invites
- tool outputs
- RAG retrieval outputs
- inter-agent messages

### 6.2 Output Requirements
The input control system MUST output:

- sanitized content (if permitted)
- metadata tags describing risk classification
- confidence scores
- optional quarantine status

### 6.3 Degraded Mode Requirements
The input control system MUST support at least one degraded mode that preserves operational continuity.

At minimum, a compliant system MUST support:

- quarantine-with-summary, OR
- tagging mode (deliver but flag)

Degraded mode selection MUST be controlled by governance policy and MUST NOT be controlled by the agent.

---

## 7. Layer 3 Requirements: Cognitive Isolation

### 7.1 Execution Isolation
The reasoning process MUST NOT have network access to external services required for execution (e.g., SMTP, database endpoints, privileged APIs).

### 7.2 Structured Request Output
The reasoning process MUST output execution intent as structured requests rather than direct API calls.

Structured requests MUST include:

- action type
- target resource
- parameter payload
- contextual metadata (source, reason, confidence)

### 7.3 Enforced Boundary
The separation MUST be enforced by architecture (network policy, sandboxing, service mesh boundaries, kernel enforcement), not solely by application logic.

---

## 8. Layer 4 Requirements: Request Validation (CA)

### 8.1 Mandatory Validation Criteria
The CA MUST evaluate each request against at least:

1. Authorization Scope
2. Semantic Intent Policy
3. Risk Threshold Policy
4. Behavioral Baseline Context
5. Multi-Agent Chain Context (if applicable)

### 8.2 Denial Behavior
If a request fails validation, the CA MUST:

- deny issuance of the execution token
- log denial to the integrity signal channel
- optionally trigger escalation depending on policy

The CA MUST NOT provide the agent with detailed denial reasons if that would reveal policy boundaries.

### 8.3 Escalation
The CA MUST support escalation workflows for high-risk actions, including:

- hold-for-approval
- deny-and-escalate
- allow-with-monitoring

---

## 9. Execution Token Requirements

### 9.1 Cryptographic Requirements
Execution tokens MUST:

- be cryptographically signed
- be verifiable by executors
- include issuer identity
- include a unique nonce

### 9.2 Mandatory Token Properties
Each execution token MUST be:

- single-use
- time-bound
- parameter-bound
- replay-resistant
- executor-bound

### 9.3 Mandatory Token Fields
Each token MUST include:

- token_id
- issuer
- subject_agent_id
- action_type
- target_resource
- parameter_binding
- content_binding
- issued_at
- expires_at
- nonce
- executor_id
- signature

### 9.4 Parameter Binding
Parameter binding MUST ensure that the token authorizes only the specific action instance approved.

### 9.5 Content Binding
Tokens MUST include either:

- a cryptographic hash of the payload, OR
- declared constraints defining permitted payload variance

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

- signature validity
- token expiration
- nonce unused
- executor identity match
- parameter binding match
- content binding match

Execution MUST NOT occur unless validation succeeds.

### 10.2 Orphaned Action Detection
Executors MUST detect and log execution requests lacking valid tokens.

### 10.3 Mismatched Action Detection
Executors MUST detect and log requests where payload differs from approved content binding.

### 10.4 Execution Logging Requirements
Executors MUST log:

- token ID
- timestamp
- action performed
- result status
- target identifiers
- validation outcomes

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

---

## 12. Layer 6 Requirements: Monitoring and Memory Audit

### 12.1 Behavioral Baselining
A monitoring system MUST maintain a behavioral baseline per agent identity.

Baselines MUST include at minimum:

- tool usage distribution
- request frequency patterns
- time-of-day usage patterns
- target distribution

### 12.2 Drift Detection
The monitoring system MUST generate alerts when drift exceeds configured thresholds.

### 12.3 Memory Audit
If persistent memory exists, an external memory audit MUST be performed.

Memory audit MUST include detection attempts for:

- staged instruction fragments
- persistent preference poisoning
- gradual scope expansion
- cross-session payload assembly

### 12.4 Baseline Integrity Verification
The monitoring system MUST monitor drift of its own baseline models over time.

Rate-of-change limits SHOULD be implemented to prevent baseline poisoning.

---

## 13. Integrity Signal Channel (ISC) Requirements

### 13.1 Immutability
ISC logs MUST be append-only and tamper-resistant.

### 13.2 Out-of-Band Separation
ISC infrastructure MUST be logically separate from the agent runtime.

### 13.3 Required Event Classes
ISC MUST record at minimum:

- token issuance events
- token denial events
- token replay attempts
- execution success/failure
- mismatched payload attempts
- orphaned actions
- monitoring alerts
- policy changes

### 13.4 Correlation Requirement
ZoD implementations SHOULD support correlation queries between:

- CA issuance vs executor execution
- memory drift vs execution anomalies
- multi-agent chain propagation vs policy violations

---

## 14. Multi-Agent Chain Validation Requirements

### 14.1 Chain Representation
Requests originating from agent-to-agent delegation MUST include a delegation chain object.

### 14.2 Chain Validation Rules
The CA MUST validate:

- delegator authorization to delegate
- delegate authorization to execute
- chain length thresholds
- cumulative risk scoring across chain

### 14.3 Collective Abuse Detection
The CA SHOULD support detection of collective abuse patterns where multiple low-risk actions aggregate into a high-risk outcome.

---

## 15. Audit and Logging Requirements

### 15.1 Mandatory Audit Data
Systems MUST retain:

- CA decision logs
- token issuance records
- executor action logs
- policy version history
- escalation decisions
- drift alerts

### 15.2 Retention
Implementations MUST define a retention period and SHOULD default to at least 180 days.

---

## 16. Compliance Levels (Normative)

### 16.1 ZoD Level 1 (ZoD-L1): Tokenized Execution Enforcement
Requires:

- CA token issuance
- executor token validation
- TTL + nonce enforcement
- parameter binding enforcement

### 16.2 ZoD Level 2 (ZoD-L2): Semantic Intent Policy
Requires:

- tool-specific semantic constraints
- machine-evaluable policy engine integration

### 16.3 ZoD Level 3 (ZoD-L3): Monitoring and Memory Audit
Requires:

- drift detection
- memory audit processes
- baseline integrity verification

### 16.4 ZoD Level 4 (ZoD-L4): Integrity Signal Channel
Requires:

- append-only ISC
- cross-layer correlation capability

### 16.5 ZoD Level 5 (ZoD-L5): Multi-Agent Chain Validation
Requires:

- delegation chain schema
- chain-level validation logic
- collective abuse detection attempts

---

## 17. Testability Requirements

A compliant implementation MUST provide automated tests validating:

- token expiration enforcement
- replay rejection
- parameter mismatch rejection
- denial logging correctness
- policy enforcement correctness

ZoD Level 3+ systems MUST provide tests validating:

- drift detection triggers
- memory poisoning signature detection attempts

---

## 18. References (Informative)

- RFC 2119: Key words for use in RFCs to Indicate Requirement Levels
- OWASP Top 10 for Large Language Model Applications
- NIST AI Risk Management Framework
