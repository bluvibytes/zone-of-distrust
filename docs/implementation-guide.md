# Implementation Guide

**Zones of Distrust: Practical Deployment Guidance**

*Version 0.9 RFC — February 2026*

---

> ⚠️ **Document Status:** This guide provides *conceptual implementation patterns*, not production-validated configurations. The patterns described represent proposed approaches based on security principles. No reference implementation exists yet. Performance targets are aspirational, not measured. Implementers should validate all patterns in their specific environment.

---

## Overview

> **Core Thesis:** ZoD is **execution governance for compromised reasoning**. Traditional security assumes that after verification, the decision-maker can be trusted. ZoD assumes the decision-maker is the threat—and governs execution anyway.

Autonomous agents introduce risk classes—delegation abuse, tool misuse, long-horizon task drift, state poisoning—that aren't addressed by model governance alone. ZoD treats them as runtime security problems.

**Agent vs. user identity:** The agent principal is not the user principal. User intent is mediated; the agent is treated as an untrusted operator. Auditing must link: user request → agent reasoning step → validated action → executed action.

This guide provides practical implementation guidance for the Zones of Distrust architecture. It covers:

- Minimum viable security (what to deploy first)
- Phased implementation approach
- Technology patterns for each layer
- Performance considerations
- Recovery and remediation procedures

**Key principle:** Perfect is the enemy of deployed. Start with L3+L4+L5+logging. Expand from there.

---

## Minimum Viable Security

You don't need all seven layers on day one. Start with the core trust boundary.

### Week 1: The Critical Three

| Layer | Minimum Implementation | Why It's Essential |
|-------|----------------------|-------------------|
| **L3: Cognitive Isolation** | Separate reasoning process from execution process | Without this, a compromised agent can act directly |
| **L4: Request Validation** | Basic scope checking + action logging | Without this, no independent verification |
| **L5: Execution** | Token-based execution with logging | Without this, no audit trail |

### Implementation Pattern

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Agent Process  │────▶│  Validation     │────▶│  Execution      │
│  (Reasoning)    │     │  Service        │     │  Service        │
│                 │     │                 │     │                 │
│  • No external  │     │  • Scope check  │     │  • Token verify │
│    egress       │     │  • Risk score   │     │  • Perform act  │
│  • Emit request │     │  • Issue token  │     │  • Log result   │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                       │
        └───────────────────────┴───────────────────────┘
                                │
                        ┌───────▼───────┐
                        │  Audit Log    │
                        │  (Append-only)│
                        └───────────────┘
```

*L3 has network access only to L4 (internal); no external/internet egress.*

### Minimum Viable Code Structure

```
agent-system/
├── agent/                    # L3: Reasoning container
│   ├── Dockerfile           # No network, no exec capabilities
│   └── agent.py             # Emits structured requests only
├── validator/               # L4: CA service
│   ├── Dockerfile
│   ├── validator.py         # Scope check, token issuance
│   └── policies/            # Authorization rules
├── executor/                # L5: Execution service
│   ├── Dockerfile
│   └── executor.py          # Token verify, perform action, log
└── docker-compose.yml       # Network isolation
```

### Docker Compose: Basic Network Isolation

```yaml
version: '3.8'
services:
  agent:
    build: ./agent
    networks:
      - agent-net
    # No access to executor network
    
  validator:
    build: ./validator
    networks:
      - agent-net
      - executor-net
    # Bridge between agent and executor
    
  executor:
    build: ./executor
    networks:
      - executor-net
    # No access to agent network
    
networks:
  agent-net:
    internal: true
  executor-net:
    internal: true
```

*This Docker Compose example is illustrative. Production isolation should use Kubernetes NetworkPolicy (with Calico, Cilium, or similar CNI), host firewall rules, or equivalent controls—and must be tested. `internal: true` prevents external routing but does not guarantee complete isolation in all host networking configurations.*

---

## Phased Implementation

### Phase 1: Core Containment (Weeks 1-2)

**Goal:** Separate reasoning from execution with basic validation and logging.

| Component | Implementation |
|-----------|----------------|
| L3 | Container with no external network egress, hardened execution surface |
| L4 | Simple scope checking against allowlist |
| L5 | Token verification, action execution, append-only logging |
| L6 (basic) | Structured logging of all decisions/actions; basic alerting on obvious violations |

**Enforcement Mechanisms (must verify, not assume):**

| Control | Enforcement Method | Verification Test |
|---------|-------------------|-------------------|
| No external egress from L3 | Kubernetes NetworkPolicy / Docker network isolation / iptables | `curl` from agent container to external IP must fail |
| Hardened execution surface | No shells/package managers in L3 image; PATH minimal; filesystem read-only; seccomp/AppArmor profile | Attempt to invoke `/bin/sh` fails (not present); attempt to write to filesystem fails |
| Agent cannot reach L5 directly | NetworkPolicy denies L3→L5 traffic | Direct request from agent to executor must timeout |
| No credentials in L3 | No secrets mounted; no environment variables with credentials | Credential scan of container shows none |
| DNS restricted | NetworkPolicy or CoreDNS policy | Agent cannot resolve external hostnames |

*Note: NetworkPolicy requires a CNI that supports it (Calico, Cilium, etc.). Verify your CNI supports the policies you define.*

**Verification:**
- [ ] Agent process cannot make HTTP requests directly
- [ ] Agent process cannot execute system commands
- [ ] All actions appear in audit log
- [ ] Token without valid signature is rejected

---

### Phase 2: Input Control (Weeks 3-4)

**Goal:** Screen inputs before the agent reasons about them.

| Component | Implementation |
|-----------|----------------|
| L2 | Prompt injection detection model (open source or commercial) |
| L2 | Content classification for sensitive patterns |
| Integration | L2 sits upstream of L3, filtered input only |

**Example L2 options (non-exhaustive):**

*The L2 screening space evolves rapidly. Choose based on threat model, latency budget, and operational requirements.*

| Approach | Pros | Cons |
|----------|------|------|
| **Rebuff** (open source) | Free, customizable | Requires tuning |
| **LLM Guard** (open source) | Good coverage | Latency |
| **Lakera Guard** (commercial) | Production-ready | Cost |
| **Custom classifier** | Tailored to your threats | Development time |

**Verification:**
- [ ] Known prompt injection patterns blocked
- [ ] Blocked content logged for review
- [ ] False positive rate acceptable (starting target: <5%; adjust based on use case)

---

### Phase 3: Behavioral Baseline & Anomaly Detection (Weeks 5-6)

**Goal:** Move from basic logging to behavioral analysis and anomaly detection.

| Component | Implementation |
|-----------|----------------|
| L6 (enhanced) | Baseline modeling of normal request patterns per agent |
| L6 (enhanced) | Cross-layer signal correlation |
| Alerting | Anomaly detection on request frequency, targets, timing, behavioral drift |

*Note: Basic logging/alerting starts in Phase 1. This phase adds behavioral analysis.*

**Monitoring stack options:**

| Stack | Components |
|-------|------------|
| **ELK** | Elasticsearch + Logstash + Kibana |
| **Grafana** | Loki + Grafana + Prometheus |
| **Cloud-native** | CloudWatch / Stackdriver / Azure Monitor |
| **Security-focused** | Splunk / Datadog Security |

**Verification:**
- [ ] All L4 decisions logged and searchable
- [ ] All L5 actions logged and searchable
- [ ] Baseline established for each agent
- [ ] Alerts trigger on >2σ deviation

---

### Phase 4: Enhanced Validation (Weeks 7-8)

**Goal:** Add semantic intent policy and behavioral baseline checks to L4.

| Component | Implementation |
|-----------|----------------|
| L4 enhanced | Content hash binding in tokens |
| L4 enhanced | Semantic intent rules (e.g., no PII to external endpoints) |
| L4 enhanced | Behavioral baseline integration from L6 |
| L4 enhanced | Risk scoring with tiered validation paths |

**Token binding implementation:**

```python
@dataclass
class ExecutionToken:
    token_id: str           # Unique identifier
    agent_id: str           # Which agent requested
    action: str             # Exact action type
    target: str             # Exact target resource (canonicalized)
    content_hash: str       # SHA-256 of canonicalized request content
    executor_id: str        # Which executor can use this (audience)
    issued_at: datetime     
    expires_at: datetime    # TTL
    nonce: str              # Replay prevention
    policy_version: str     # Version of policy used for decision
    decision_id: str        # Unique ID of the validation decision
    signature: str          # CA signature over all fields
```

**Token semantics:**
- `executor_id` (audience): Only the specified executor may use this token
- `content_hash`: Hash of *canonicalized* request content to prevent encoding tricks
- `nonce`: Single-use; nonce store scoped per executor with TTL matching token TTL
- `policy_version`: Enables audit trail of which policy was applied
- `decision_id`: Links token to full decision record in L6 logs

**Verification:**
- [ ] Content modification after approval causes rejection
- [ ] Semantic policy violations blocked (test with PII egress)
- [ ] Requests outside behavioral baseline trigger enhanced scrutiny

---

### Phase 5: Memory Audit (Weeks 9-10)

**Goal:** External audit of agent persistent memory.

| Component | Implementation |
|-----------|----------------|
| L6 (memory) | Scheduled memory snapshots |
| L6 (memory) | Diff analysis against baseline |
| L6 (memory) | Source verification for memory entries |
| Alerting | Flag entries without verified origin |

**Memory audit approach:**

```
┌─────────────────────────────────────────────────────────┐
│                    Memory Audit Flow                     │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. Snapshot agent memory (scheduled, e.g., hourly)    │
│                        │                                │
│                        ▼                                │
│  2. Compare to previous snapshot                        │
│     • New entries                                       │
│     • Modified entries                                  │
│     • Deleted entries                                   │
│                        │                                │
│                        ▼                                │
│  3. For each change, verify source                      │
│     • Trace to input event                              │
│     • Verify input passed L2 screening                  │
│     • Flag unverified sources                           │
│                        │                                │
│                        ▼                                │
│  4. Pattern analysis                                    │
│     • Scope creep detection                             │
│     • Poisoning signature matching                      │
│     • Baseline drift calculation                        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Verification:**
- [ ] Memory snapshots captured on schedule
- [ ] Diff analysis identifies changes
- [ ] Unverified entries flagged for review
- [ ] Poisoning test payload detected

---

### Phase 6: OS Foundation (Weeks 11-12)

**Goal:** Harden the infrastructure layer.

| Component | Implementation |
|-----------|----------------|
| L1 | Agent identity as distinct principal |
| L1 | Credential broker (agent never sees secrets) |
| L1 | Model provenance verification |
| L1 | Hardware attestation (if available) |

**Credential broker pattern:**

```
┌─────────────────┐                    ┌─────────────────┐
│  Agent Process  │                    │  Target API     │
│                 │                    │                 │
│  "Send email    │                    │                 │
│   to X"         │                    │                 │
└────────┬────────┘                    └────────▲────────┘
         │                                      │
         │ Request (no credentials)             │ Request + credentials
         ▼                                      │
┌─────────────────────────────────────────────────────────┐
│                   Credential Broker                      │
│                                                         │
│  1. Receive request from validated token                │
│  2. Retrieve credentials from secure store              │
│  3. Execute request on agent's behalf                   │
│  4. Return result (credentials never exposed)           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Verification:**
- [ ] Agent process cannot read credentials from any store
- [ ] Agent identity tracked independently from user identity
- [ ] Model hash verified before load

---

### Phase 7: Human Governance (Ongoing)

**Goal:** Establish policy authority and escalation workflows.

| Component | Implementation |
|-----------|----------------|
| L7 | Risk classification definitions |
| L7 | Escalation thresholds |
| L7 | Approval workflows for high-risk actions |
| L7 | Policy change management (GitOps) |

**Risk classification example:**

| Risk Level | Actions | Handling |
|------------|---------|----------|
| **Low** | Read operations, status queries | Auto-approve, standard logging |
| **Medium** | Write operations, data modifications | Auto-approve, enhanced logging, L6 scrutiny |
| **High** | Financial transactions, data exports, credential operations | Human approval required |
| **Critical** | Bulk operations, privilege changes, cross-system actions | Multi-person approval |

**Verification:**
- [ ] High-risk actions require human approval
- [ ] Approvals logged immutably
- [ ] Policy changes go through PR review
- [ ] Escalation queue monitored

---

## Performance Budgets

> ⚠️ **Note:** The following are *target benchmarks*, not empirically validated measurements. Actual performance will depend on implementation choices, infrastructure, and workload characteristics. These targets are provided as design guidance for implementers.

| Layer | Target Latency | Notes |
|-------|---------------|-------|
| L2 screening | <100ms | Async for non-blocking use cases |
| L4 fast-path | <50ms | Pre-approved patterns, cached tokens |
| L4 standard | <500ms | Full validation |
| L4 elevated | <5s | Human escalation excluded |
| L5 token validation | <5ms | Signature + nonce check |
| L6 monitoring | Async | Does not block request path |

*These targets are based on typical enterprise latency tolerances, not ZoD-specific benchmarks. Validation through implementation is needed.*

### Tiered Validation

```
                    ┌─────────────────┐
                    │  Incoming       │
                    │  Request        │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Risk           │
                    │  Classification │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
         ▼                   ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   FAST PATH     │ │  STANDARD PATH  │ │  ELEVATED PATH  │
│                 │ │                 │ │                 │
│ • Pre-approved  │ │ • Full baseline │ │ • Max scrutiny  │
│   patterns      │ │   comparison    │ │ • Human review  │
│ • Cached tokens │ │ • Semantic      │ │   possible      │
│ • <50ms         │ │   analysis      │ │ • <5s (excl.    │
│                 │ │ • <500ms        │ │   human)        │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

### Total Overhead (Estimated)

For typical agent action (input screening → validation → execution):

| Use Case | Estimated Added Latency | Likely Acceptable? |
|----------|-------------------------|-------------------|
| Email processing | 100-300ms | ✓ Yes |
| Document analysis | 100-300ms | ✓ Yes |
| Database queries | 100-300ms | ✓ Yes |
| Workflow automation | 100-300ms | ✓ Yes |
| Interactive chat | 100-300ms | ✓ Usually |
| Real-time trading | 100-300ms | ✗ May need optimization |

*Estimates based on component targets above. Actual overhead requires implementation and measurement.*

**The trade-off is explicit:** Latency for security. Tune via risk classification.

---

## Degraded Operation Rules

When components fail, the system must have defined behavior—not undefined behavior.

### Layer Unavailability

| Layer | Unavailable | Default Behavior by Risk Class |
|-------|-------------|-------------------------------|
| **L2** | Screening service down | **Low risk:** Fail-open with enhanced L6 logging. **Medium/High risk:** Fail-closed, queue inputs. |
| **L4** | Validation service down | **All:** Fail-closed. No tokens issued. Agent can reason but not act. |
| **L5** | Execution service down | **All:** Fail-closed. No actions executed. Queue for retry with backoff. |
| **L6** | Monitoring service down | **Low/Medium:** Continue with local buffering, alert on buffer overflow. **High/Critical:** Fail-closed until monitoring restored. |

### Capacity Degradation

| Condition | Response |
|-----------|----------|
| L4 overloaded | Shed low-risk requests first; prioritize high-risk validation |
| L6 false positive storm | Automatic threshold adjustment + alert to L7; do not disable monitoring |
| Model provider outage | Queue requests; switch to fallback model if configured; alert immediately |
| Nonce store unavailable | Fail-closed; do not issue tokens without replay protection |

### Telemetry Loss

| Duration | Response |
|----------|----------|
| <1 minute | Buffer locally, continue operation |
| 1-5 minutes | Alert to L7, reduce to low-risk operations only |
| >5 minutes | Escalate to quarantine for high-value agents |

*Define these thresholds in L7 policy. The defaults above are starting points.*

---

## Recovery and Remediation

### When Compromise Is Detected

```
┌─────────────────────────────────────────────────────────┐
│              INCIDENT RESPONSE FLOW                      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. DETECTION                                           │
│     • L6 cross-layer correlation triggers alert         │
│     • Integrity channel shows anomaly pattern           │
│                                                         │
│  2. IMMEDIATE CONTAINMENT                               │
│     • L4 revokes all outstanding tokens for agent       │
│     • L4 refuses new token issuance                     │
│     • L5 rejects pending actions from agent             │
│     • Agent quarantined at L3 (can reason, can't act)   │
│     • Temporarily disable high-risk tools               │
│     • Rotate downstream credentials (even if brokered)  │
│     • Invalidate any cached approvals                   │
│                                                         │
│  3. FORENSIC ANALYSIS                                   │
│     • L6 memory audit: when did anomalies start?        │
│     • Integrity channel: what actions during window?    │
│     • Cross-agent assessment: did compromise spread?    │
│                                                         │
│  4. REMEDIATION                                         │
│     • Memory rollback to last-known-good state          │
│     • Or: selective purge of poisoned entries           │
│     • Or: full memory reset with verified rebuild       │
│                                                         │
│  5. RECOVERY                                            │
│     • Behavioral baseline reset                         │
│     • Probationary period: tighter constraints          │
│     • Gradual scope restoration                         │
│     • Re-enable tools incrementally                     │
│                                                         │
│  6. POST-INCIDENT                                       │
│     • Signature update for L2/L6                        │
│     • Policy revision if needed                         │
│     • Architecture adjustment if gap identified         │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Memory Recovery Options

| Option | When to Use | Risk |
|--------|-------------|------|
| **Rollback** | Clear compromise window, good baseline exists | May lose legitimate context |
| **Selective purge** | Poisoning signatures identified | May miss related entries |
| **Full reset** | Uncertain scope, high-value agent | Significant context loss |

### Multi-Agent Cascade Assessment

When one agent is compromised in a multi-agent system:

1. Identify all agents that received output from compromised agent
2. Trigger memory audit for each downstream agent
3. Check L6 for behavioral drift correlated with compromised output
4. Enhanced monitoring for all agents in interaction chain
5. Consider quarantine if drift detected

---

## Technology Stack Examples

### Option A: Kubernetes-Native

```yaml
# L3: Agent with no capabilities
apiVersion: v1
kind: Pod
metadata:
  name: agent
spec:
  containers:
  - name: agent
    securityContext:
      capabilities:
        drop: ["ALL"]
      readOnlyRootFilesystem: true
      runAsNonRoot: true
  # Network policy: only egress to validator
---
# L4: Validator service
apiVersion: v1
kind: Service
metadata:
  name: validator
spec:
  selector:
    app: validator
  ports:
  - port: 8080
---
# L5: Executor with secrets access
apiVersion: v1
kind: Pod
metadata:
  name: executor
spec:
  containers:
  - name: executor
    volumeMounts:
    - name: secrets
      mountPath: /secrets
      readOnly: true
  # Network policy: no ingress from agent
```

### Option B: Serverless

| Layer | AWS | GCP | Azure |
|-------|-----|-----|-------|
| L3 | Lambda (no VPC) | Cloud Function | Function (isolated) |
| L4 | Lambda + DynamoDB | Cloud Function + Firestore | Function + CosmosDB |
| L5 | Lambda (VPC) + Secrets Manager | Cloud Function + Secret Manager | Function + Key Vault |
| L6 | CloudWatch + Lambda | Cloud Monitoring + Function | Monitor + Function |
| Logging | CloudTrail + S3 | Cloud Logging + GCS | Log Analytics + Blob |

*Serverless requires explicit egress restrictions; cloud function defaults typically allow outbound internet. Use VPC egress controls, allowlist proxies, or cloud-native firewall rules to enforce L3 isolation.*

### Option C: Traditional Infrastructure

| Layer | Implementation |
|-------|----------------|
| L3 | VM or container with firewall rules, no outbound except to L4 |
| L4 | Dedicated validation service, horizontally scalable |
| L5 | Separate server pool with credential access |
| L6 | SIEM integration (Splunk, Elastic, etc.) |
| Logging | Append-only storage (WORM, immutable S3, etc.) |

---

## Extending Zero Trust

### What Zero Trust Already Provides

| Zero Trust Principle | ZoD Application |
|---------------------|-----------------|
| Never trust, always verify | Verify every agent request at L4 |
| Least privilege | No standing access, scoped tokens |
| Assume breach | Each layer assumes layers above have failed |
| Microsegmentation | L3/L4/L5 isolation |
| Continuous monitoring | L6 behavioral analysis |

### Where Zero Trust Falls Short for Agents

| Gap | Why It Matters | ZoD Addition |
|-----|----------------|--------------|
| **No pre-reasoning input control** | What agent sees = what agent thinks | L2 screens before reasoning |
| **Assumes entity knows it's compromised** | Injected agent IS the compromise | External monitoring (L6), recovery protocols |
| **No memory audit** | Delayed-execution attacks exploit persistent state | L6 memory audit function |

### The Message for Enterprise

> *Zero Trust is the foundation. The Zones of Distrust extend it into a domain Zero Trust was never designed for—autonomous entities that can be compromised without knowing it.*

---

## Implementation Checklist

### Minimum Viable (Week 1)

- [ ] L3: Agent process isolated (no network egress, no exec)
- [ ] L4: Basic scope validation
- [ ] L5: Token-based execution
- [ ] Logging: All actions logged with tokens

### Production Ready (Week 12)

- [ ] L1: Agent identity, credential broker, model verification
- [ ] L2: Input screening with degraded modes
- [ ] L3: Process isolation enforced at OS/container level
- [ ] L4: Full validation with semantic policy, behavioral baseline
- [ ] L5: Token binding with content hash, nonce, TTL
- [ ] L6: Behavioral monitoring, memory audit, baseline integrity
- [ ] L7: Risk classification, escalation workflows, GitOps policy
- [ ] Integrity channel: Append-only, out-of-band, correlation-ready
- [ ] Recovery: Documented playbook, tested procedures

---

## Common Pitfalls

| Pitfall | Why It Happens | How to Avoid |
|---------|----------------|--------------|
| **Agent can still reach internet** | Network isolation incomplete | Test with actual requests; verify firewall rules |
| **Credentials in environment variables** | Convenience over security | Use credential broker; agent never sees secrets |
| **Logging but not monitoring** | Logs exist but no one watches | Set up alerts on day 1; review weekly |
| **L4 validates identity but not content** | Traditional IAM thinking | Add semantic intent policy; content hash binding |
| **No recovery plan** | Focus on prevention | Document playbook before you need it |
| **Human approval fatigue** | Too many escalations | Tune risk thresholds; use tiered validation |

---

## Related Documents

- [Architecture Specification](architecture.md) — Layer definitions and interactions
- [Security Properties](security-properties.md) — What to verify (P1-P12)
- [Threat Model](threat-model.md) — What you're defending against

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
