# Security Properties

**Zones of Distrust: Twelve Measurable Security Properties**

*Version 0.9 RFC — February 2026*

---

## Overview

The Zones of Distrust architecture claims twelve security properties organized into four categories:

- **Execution Governance (P1-P4)** — Controls over how actions are authorized and executed
- **Detection (P5-P8)** — Capabilities for identifying compromise
- **Containment (P9-P10)** — Limits on blast radius when compromise occurs
- **Governance (P11-P12)** — Policy authority and enforcement

Each property is:
- **Verifiable** — Can be tested and audited
- **Scoped** — Clear boundaries on what it does and doesn't cover
- **Falsifiable** — States conditions under which it would fail

> *These properties are stated as claims, not as proofs. They are designed to be tested, challenged, and refined through implementation experience, adversarial review, and formal analysis. The architecture invites scrutiny on exactly these assertions.*

---

## Property Summary

| ID | Property | Category | Primary Layers |
|----|----------|----------|----------------|
| P1 | No unmediated privileged execution | Execution | L1, L3, L4, L5 |
| P2 | Cryptographic attribution | Execution | L4, L5 |
| P3 | Replay prevention | Execution | L4, L5 |
| P4 | Parameter-bound execution | Execution | L4, L5 |
| P5 | Probabilistic memory poisoning detection | Detection | L6 |
| P6 | Cross-layer correlation detection | Detection | L6, L7 |
| P7 | Immutable forensic record | Detection | L5, L6 |
| P8 | Baseline integrity verification | Detection | L6, L7 |
| P9 | Graceful degradation | Containment | All |
| P10 | Compromise non-propagation across agents | Containment | L4 |
| P11 | External policy authority | Governance | L7, L4, L2 |
| P12 | Deterministic semantic enforcement | Governance | L4 |

---

## Execution Governance Properties

### P1: No Unmediated Privileged Execution

**Claim:** No agent reasoning process can execute a privileged action without a validated token from the Certificate Authority.

**Enforced by:**
- Layer 3 (Cognitive Isolation): Reasoning process has no execution capabilities
- Layer 1 (OS Foundation): Process separation enforced at OS level
- Layer 4 (CA): All requests must pass through validation
- Layer 5 (Execution): Only accepts validated tokens

**Verification method:**
```
Test: Attempt privileged action directly from reasoning process
Expected: OS-level rejection, no network path to execution layer
Audit: Review process isolation configuration, network policies
```

**Failure condition:** If the reasoning process can directly invoke privileged actions or reach protected execution resources, P1 is violated.

---

### P2: Cryptographic Attribution

**Claim:** Every executed action is cryptographically bound to a specific agent identity, request, and approval. The token's signature chain provides cryptographically verifiable attribution.

**Scope limitation:** Attribution holds assuming CA private keys and executor signing keys are uncompromised. Key compromise boundaries are discussed in Trust Assumptions.

**Enforced by:**
- Layer 4 (CA): Signs tokens with CA private key
- Layer 5 (Execution): Logs token with action, verifies signature

**Verification method:**
```
Test: Given an audit log entry, trace back to specific agent and approval
Expected: Complete chain from action → token → approval → request → agent
Audit: Verify signature chain integrity, key management practices
```

**Token attribution chain:**
```
Action Performed
    └── Execution Token (signed by CA)
            ├── Agent Identity
            ├── Request Hash
            ├── Approval Timestamp
            ├── Content Hash
            └── CA Signature
```

**Failure condition:** If an action cannot be traced to a specific agent identity and approval, or if signatures can be forged, P2 is violated.

---

### P3: Replay Prevention

**Claim:** Each token includes a nonce and temporal bound. Replayed tokens are rejected. The execution layer tracks used nonces within the temporal window.

**Enforced by:**
- Layer 4 (CA): Issues unique nonce and TTL with each token
- Layer 5 (Execution): Maintains nonce registry, rejects duplicates and expired tokens

**Verification method:**
```
Test: Capture valid token, attempt to use twice
Expected: First use succeeds, second use rejected
Test: Capture valid token, wait for TTL expiry, attempt to use
Expected: Rejected with TTL expiration error
Audit: Review nonce registry implementation, TTL enforcement
```

**Nonce registry requirements:**
- Registry size = maximum concurrent tokens within TTL window
- Registry must persist across execution layer restarts within TTL window
- Distributed execution layers must share nonce state
- Nonce registry must be strongly consistent within the TTL window (eventual consistency breaks this property)

**Failure condition:** If a token can be used more than once, or used after TTL expiry, P3 is violated.

---

### P4: Parameter-Bound Execution

**Claim:** The execution token specifies exact action type, target resource, content hash, executor identity, and TTL. The execution layer rejects any action that deviates from the token's bound parameters.

**Enforced by:**
- Layer 4 (CA): Binds all parameters into token
- Layer 5 (Execution): Verifies all parameters before execution

**Token binding fields:**

| Field | Binding | Verification |
|-------|---------|--------------|
| `action_type` | Exact match | Action must equal token field |
| `target_resource` | Exact match | Resource identifier must equal token field |
| `content_hash` | SHA-256 | Hash of content must equal token field |
| `executor_id` | Exact match | Execution service instance must equal token field |
| `ttl` | Temporal bound | Current time must be within TTL |
| `nonce` | Uniqueness | Must not exist in nonce registry |

**Canonicalization requirement:** All bound parameters must be canonicalized before hashing/signing to prevent semantic-equivalent bypass (e.g., URL normalization, whitespace handling, encoding consistency).

**Verification method:**
```
Test: Obtain valid token, modify content before execution
Expected: Content hash mismatch, execution rejected
Test: Obtain valid token, attempt execution on different resource
Expected: Target resource mismatch, execution rejected
Audit: Review token verification code path
```

**Failure condition:** If the execution layer performs an action that differs from the token's bound parameters, P4 is violated.

---

## Detection Properties

### P5: Probabilistic Memory Poisoning Detection

**Claim:** Layer 6's external memory audit provides probabilistic detection of delayed-execution memory poisoning attacks through pattern analysis across sessions, baseline drift analysis, and scope creep detection.

**Enforced by:**
- Layer 6 (Continuous Monitoring): External memory audit function

**Detection mechanisms:**

| Mechanism | What It Catches |
|-----------|-----------------|
| Source verification | Memory entries without verified origin |
| Baseline comparison | Entries that differ from known-good snapshots |
| Pattern matching | Signatures of known poisoning techniques |
| Scope creep detection | Gradually expanding access patterns in memory |
| Temporal anomalies | Entries with suspicious timing patterns |

**Verification method:**
```
Test: Inject known memory poisoning payload, run audit
Expected: Payload flagged with confidence score
Test: Inject novel poisoning pattern, run audit
Expected: May or may not be detected (probabilistic)
Audit: Review signature set coverage, false positive/negative rates
```

**Important scope limitation:** Detection is probabilistic, not guaranteed. Novel poisoning patterns that do not match known signatures may evade initial detection. Detection capability improves as the signature set matures through real-world deployment data.

**Suggested Evaluation Targets:**

*These are example thresholds for deployments that choose to implement P5. Actual targets should be calibrated to deployment context and risk tolerance.*

| Metric | Example Target | Measurement Method |
|--------|----------------|-------------------|
| Detection recall (known patterns) | ≥90% on benchmark set | Test against curated poisoning payload corpus |
| False positive rate | ≤5% of legitimate entries flagged | Measure against clean baseline datasets |
| Time-to-detection | ≤3 audit cycles after injection | Track latency from injection to flag |
| Novel pattern detection | Tracked, not targeted | Log patterns detected without prior signature |

**Failure condition:** P5 does not "fail" in a binary sense—it has a detection rate that can be measured and improved. If the detection rate falls below acceptable thresholds for known attack patterns, remediation is required.

---

### P6: Cross-Layer Correlation Detection

**Claim:** The system supports correlation-based detection across multiple independent layers and does not rely on any single layer as authoritative. No layer's self-report is sufficient to confirm or deny compromise.

**Enforced by:**
- Layer 6 (Continuous Monitoring): Primary correlator
- Layer 7 (Human Governance): Independent access to raw signals

**Correlation example:**

```
Layer 2 logs: Input flagged with medium confidence
Layer 3 logs: Agent produced request shortly after processing flagged input
Layer 4 logs: Request targets resource outside behavioral baseline
Layer 6 correlates: Tagged input + unusual request + behavioral deviation
Result: Elevated alert, potential compromise indicator
```

**Why single-layer reporting is insufficient:**

| Layer | Can Report | Cannot Report |
|-------|-----------|---------------|
| L2 | What it flagged | What it missed |
| L3 | Nothing (cannot self-assess reasoning compromise) | Its own compromise |
| L4 | Requests received and decisions made | Whether requests were legitimate |
| L5 | Actions executed | Whether actions were appropriate |
| L6 | Anomalies observed | Its own baseline corruption |

**Verification method:**
```
Test: Simulate multi-stage attack across layers
Expected: Individual layer logs show fragments; correlation reveals pattern
Audit: Review correlation rules, test coverage of attack scenarios
```

**Correlation Specification:**

To avoid "SIEM marketing" vagueness, correlation requires:

| Element | Requirement |
|---------|-------------|
| Minimum signals | ≥2 independent layers must contribute signals |
| Temporal window | Signals must occur within configurable correlation window (default: 60 seconds) |
| Rule structure | `IF (L2.flagged_input AND L4.novel_target AND L6.baseline_deviation) THEN alert_level = HIGH` |
| Confidence scoring | Each signal contributes weight; threshold triggers escalation |
| False positive tracking | Correlated alerts that prove benign are logged for rule tuning |

*The specific correlation rules are deployment-dependent, but the architecture requires that rules be explicit, testable, and auditable.*

**Failure condition:** If correlation is not possible because signals are not available, not independently collected, or not queryable across layers, P6 is violated. (Note: A stealthy attacker remaining within baseline does not violate P6; correlation capability existing but not triggering is expected behavior for sophisticated attacks.)

---

### P7: Immutable Forensic Record

**Claim:** Integrity signals are written to an append-only, out-of-band channel that no agent process can read, modify, or suppress. A compromised agent cannot tamper with the forensic record of its own behavior.

**Enforced by:**
- Layer 5 (Execution): Writes to integrity signal channel
- Layer 6 (Continuous Monitoring): Writes to integrity signal channel
- Infrastructure: WORM storage or append-only log

**Channel requirements:**

| Requirement | Implementation |
|-------------|----------------|
| Append-only | WORM storage, append-only log service, or tamper-evident hash-chained ledger |
| Out-of-band | Separate network path from agent data plane |
| Agent-inaccessible | No read/write path from agent processes |
| Cryptographically verified | Signed entries, hash chain integrity |

**Verification method:**
```
Test: From agent process, attempt to read integrity channel
Expected: Access denied, no network path
Test: From agent process, attempt to delete or modify entry
Expected: Access denied, write-once enforcement
Audit: Review storage configuration, network isolation, access controls
```

**Failure condition:** If an agent process can read, modify, or delete entries in the integrity signal channel, P7 is violated.

---

### P8: Baseline Integrity Verification

> ⚠️ **Note:** This property is conceptually important but implementation mechanisms require further specification. The "monitor the monitor" problem is architecturally challenging. Contributions to strengthen this property are welcomed.

**Claim:** Layer 6 monitors the drift of its own behavioral baselines. Rate-of-change limits on baseline parameters and periodic resets against known-good reference points prevent gradual adversarial manipulation of what the monitoring system considers "normal."

**Enforced by:**
- Layer 6 (Continuous Monitoring): Self-monitors baseline drift
- Layer 7 (Human Governance): Approves baseline changes beyond threshold

**Baseline protection mechanisms:**

| Mechanism | Purpose |
|-----------|---------|
| Cryptographic snapshots | Reference points that cannot be retroactively modified |
| Rate-of-change limits | Baseline cannot shift faster than X% per time period |
| Human approval gates | Changes beyond threshold require L7 authorization |
| Cross-validation | Baseline compared against immutable audit log |

**Possible implementation approaches (not validated):**
- Out-of-band heartbeat to separate security domain
- Hardware-based attestation of baseline state
- Independent shadow monitoring system for cross-validation
- Periodic human review of baseline snapshots

**Verification method:**
```
Test: Attempt to rapidly shift baseline through adversarial inputs
Expected: Rate limit triggered, human approval required
Test: Attempt to modify historical baseline snapshot
Expected: Cryptographic verification failure
Audit: Review rate limits, approval workflows, snapshot integrity
```

**Failure condition:** If the behavioral baseline can be shifted without triggering rate limits or human approval, P8 is violated.

---

## Containment Properties

### P9: Graceful Degradation

**Claim:** When any single layer fails, the remaining layers continue to provide independent security controls. The architecture degrades to reduced security posture, not to no security posture.

**Enforced by:** All layers operating independently

**Degradation matrix:**

| Layer Failure | Remaining Controls | Residual Risk |
|---------------|-------------------|---------------|
| L1 fails | L2-L7 continue, but process isolation lost | **Critical** — foundational control lost |
| L2 fails | L3-L7 continue, agent sees unscreened input | Medium — downstream layers compensate |
| L3 fails | L1,L2,L4-L7 continue, but reasoning/execution separation lost | **Critical** — core control lost |
| L4 fails | L1-L3,L5-L7 continue, but independent validation lost | **Critical** — core control lost |
| L5 fails | L1-L4,L6-L7 continue, no execution possible | Operations halt — safe failure |
| L6 fails | L1-L5,L7 continue, but monitoring blind | Medium — detection lost, prevention remains |
| L7 fails | L1-L6 continue with last-known-good policy | Medium — no policy updates, automation continues |

**Critical layer failures (L1, L3, L4) are survivable only if downstream layers fail closed.** If L4 fails but L5 continues accepting requests without validation, the system is not ZoD in any meaningful sense.

**Verification method:**
```
Test: Disable each layer in isolation, verify remaining controls function
Expected: Degraded operation, not total failure
Audit: Review failure mode handling, fallback configurations
```

**Failure condition:** If a single layer failure causes cascading failure across multiple independent layers, P9 is violated.

---

### P10: Compromise Non-Propagation Across Agents

**Claim:** A compromised agent in a multi-agent system cannot propagate privilege escalation through trust inheritance. Each agent-to-agent request is independently validated by the CA.

**Scope limitation:** This prevents unauthorized execution through delegated authority. It does not prevent influence attacks where a compromised agent manipulates other agents' reasoning through legitimate outputs—that is addressed by L2 screening and L6 correlation, not by P10.

**Enforced by:**
- Layer 4 (CA): Validates complete request chains, not just immediate requests

**Chain validation requirements:**

When Agent B receives a request originating from Agent A:
1. Verify Agent A had authorization for its original request
2. Verify the output has not been tampered with in transit
3. Verify the trust chain has not been broken by intermediate agents
4. Verify the collective action across the chain does not exceed any single agent's authorization

**Verification method:**
```
Test: Compromise Agent A, attempt to use Agent B for privilege escalation
Expected: CA rejects request chain, Agent A's compromise contained
Test: Agent A requests Agent B to perform action Agent A is not authorized for
Expected: CA evaluates chain, rejects unauthorized delegation
Audit: Review chain validation logic, delegation policies
```

**Failure condition:** If a compromised agent can use another agent to perform actions beyond its own authorization scope, P10 is violated.

---

## Governance Properties

### P11: External Policy Authority

**Claim:** Risk thresholds, authorization scopes, semantic intent policies, and escalation criteria are set by Layer 7 (Human Governance) and enforced by Layers 2, 4, and 6. No agent has visibility into or influence over these policies.

**Enforced by:**
- Layer 7 (Human Governance): Sets all policies
- Layer 4 (CA): Enforces authorization and semantic policies
- Layer 2 (Input Control): Enforces screening policies
- Layer 6 (Monitoring): Enforces threshold policies

**Policy opacity principle:**

| Policy Type | Where It Lives | Agent Visibility |
|-------------|----------------|------------------|
| Authorization scopes | L4, L7 | None |
| Semantic intent constraints | L4, L7 | None |
| Risk thresholds | L4, L6, L7 | None |
| Screening rules | L2, L7 | None |
| Escalation criteria | L4, L7 | None |
| Behavioral baselines | L6, L7 | None |

**Verification method:**
```
Test: From agent reasoning process, attempt to read policy configuration
Expected: No access path, policy invisible to agent
Test: Agent attempts to modify its own risk classification
Expected: No mechanism exists, risk set by L7 only
Audit: Review policy storage, access controls, agent capabilities
```

**Why this matters:** A compromised agent cannot reason about how to circumvent rules it cannot see. Policy opacity reduces the agent's ability to optimize around constraints, but enforcement remains the primary control. The agent can still be socially engineered to request harmful actions; the enforcement layers stop those requests.

**Failure condition:** If an agent can read, modify, or influence its own governing policies, P11 is violated.

---

### P12: Deterministic Semantic Enforcement

**Claim:** Semantic intent policies are expressed as concrete, machine-enforceable constraints rather than heuristic intent inference. Policy violations produce deterministic enforcement decisions, not probabilistic flags.

**Terminology note:** "Semantic" here refers to action meaning at the policy layer (recipient, destination, scope, classification)—not probabilistic natural language interpretation. These are deterministic rules about what actions mean, not ML-based intent inference.

**Enforced by:**
- Layer 4 (CA): Evaluates semantic constraints deterministically

**Constraint types:**

| Type | Example | Enforcement |
|------|---------|-------------|
| Allowlist | Email recipients must be in approved domain list | Deterministic reject if not in list |
| Blocklist | Content must not contain credentials, API keys | Deterministic reject if pattern matched |
| Classification ceiling | PII cannot be sent to external endpoints | Deterministic reject if classification exceeded |
| Rate limit | No more than N actions of type X per hour | Deterministic reject if limit exceeded |
| Negative constraint | No fund transfers to accounts not in vendor list | Deterministic reject regardless of justification |

**Why deterministic matters:**

| Probabilistic | Deterministic |
|---------------|---------------|
| "This looks suspicious" (70% confidence) | "This violates rule X" (100% certain) |
| Can be argued, explained away | Cannot be argued, no exceptions |
| Agent might reason around it | Agent hits hard stop |

**Verification method:**
```
Test: Submit request violating semantic constraint
Expected: Deterministic rejection, clear policy citation
Test: Submit request with elaborate justification for exception
Expected: Same deterministic rejection, justification ignored
Audit: Review policy language, ensure no probabilistic evaluation
```

**Failure condition:** If semantic policy violations are evaluated probabilistically or can be overridden by agent justification, P12 is violated.

---

## Property Dependencies

Properties build on each other:

```
P1 (No unmediated execution)
 └── Enables P2, P3, P4 (all require execution to go through tokens)
     
P7 (Immutable forensic record)
 └── Enables P6 (correlation requires trustworthy logs)
     └── Enables P5 (memory audit depends on baseline comparison)
         
P11 (External policy authority)
 └── Enables P12 (semantic enforcement requires policies agent can't see)
     
P9 (Graceful degradation)
 └── Requires all layers to operate independently
```

---

## Trust Assumptions

The security properties above hold under these assumptions. Violation of these assumptions may invalidate one or more properties.

**Key custody:**
- CA signing keys are stored in HSM or equivalent secure key management
- Executor signing keys are protected and rotated according to policy
- Logging/integrity channel keys are separate from operational keys

**Key compromise boundaries:**

| Key Compromised | Properties Affected | Recovery |
|-----------------|---------------------|----------|
| CA signing key | P2, P3, P4 (tokens can be forged) | Rotate key, invalidate all tokens, re-establish trust |
| Executor key | P2 (attribution for that executor) | Rotate key, audit actions during compromise window |
| Logging key | P7 (forensic integrity) | Rotate key, mark compromise window as untrusted |

**Infrastructure assumptions:**
- L1 process isolation is enforced by the OS/container runtime
- Network policies are correctly implemented and cannot be bypassed by agent processes
- Time synchronization is accurate within TTL tolerance

**Out of scope:**
- Availability / DoS resilience is not a numbered property; rate limiting and capacity planning are deployment concerns
- Insider threats with administrative access to L4/L7 infrastructure

---

## Audit Checklist

For each property, verify:

- [ ] **Implementation exists** — The mechanism is actually built
- [ ] **Tests pass** — Verification method produces expected results
- [ ] **Failure conditions are not present** — No known violations
- [ ] **Monitoring is active** — Ongoing verification, not just initial check
- [ ] **Documentation is current** — Implementation matches specification

---

## Related Documents

- [Architecture Specification](architecture.md) — How the layers implement these properties
- [Threat Model](threat-model.md) — What attacks these properties defend against
- [Implementation Guide](implementation-guide.md) — How to build systems that achieve these properties

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
