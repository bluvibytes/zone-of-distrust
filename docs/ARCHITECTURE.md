# Architecture Specification

**Zones of Distrust: Seven-Layer Reference Architecture**

*Version 0.9 RFC — February 2026*

---

## Overview

> **Breach is the premise. Containment is the architecture.**

The Zones of Distrust are organized as seven layers with a directional logic. Like the OSI reference model, each layer provides services to the layer above it and depends on the layer below it. However, where OSI layers represent protocol abstractions, ZoD layers represent *enforcement boundaries*—points where trust is withdrawn and validation is required. 

**Each layer assumes every layer above it has already been compromised.**

---

## Assumptions and Non-Goals

### Assumptions (What ZoD Requires)

ZoD provides value only when these conditions are met:

| Assumption | Consequence if Violated |
|------------|------------------------|
| **Infrastructure is trustworthy** | If OS, hypervisor, or hardware is compromised, ZoD controls can be bypassed |
| **Cryptographic primitives work** | If signatures can be forged, token binding fails |
| **Network segmentation is enforced** | If L3 can reach L5 directly, isolation is defeated |
| **Implementation is correct** | Bugs in CA, executor, or monitoring negate architectural properties |
| **Configuration is correct** | Overly permissive policies or disabled monitoring remove protection |
| **Monitoring is operational** | If L6 is down, behavioral detection doesn't happen |

### Non-Goals (What ZoD Does Not Attempt)

| Non-Goal | Why |
|----------|-----|
| **Making agents trustworthy** | ZoD assumes agents cannot be trusted; it governs untrusted actors |
| **Preventing all attacks** | ZoD increases attack cost and limits blast radius; no architecture stops everything |
| **Replacing compliance programs** | ZoD provides controls and evidence; compliance requires broader organizational work |
| **Solving infrastructure security** | ZoD assumes secure infrastructure; it does not replace IAM, KMS, network security, or OS hardening |
| **Detecting all poisoning** | P5 is probabilistic; novel attacks may evade detection |
| **Guaranteeing performance** | Security controls add latency; ZoD does not guarantee sub-millisecond validation |

---

## Architecture Diagram

```
                              ┃ POLICY ┃
                              ┃ FLOWS  ┃
                              ▼ DOWN   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L7  HUMAN GOVERNANCE                                               │   │
│   │      Risk-weighted escalation • Policy-setting • Consumes signals   │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                         │                         │
│         │ Alerts, anomalies                       │ Policy, thresholds      │
│         │                                         ▼                         │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L6  CONTINUOUS MONITORING            ◄───────────────────────────► │   │
│   │      Behavioral baseline • Drift detection • Memory audit           │   │
│   │      ┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈   │   │
│   │      Watches L3-L5 from OUTSIDE (cannot be influenced by agent)     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                         │ MONITORING              │
│         │ Immutable logs                          │ flows ACROSS            │
│         │                                         ▼                         │
│   ╔═════════════════════════════════════════════════════════════════════╗   │
│   ║  L5  EXECUTION                                                      ║   │
│   ║      Isolated process • Agent has NO ACCESS • Logs immutably        ║   │
│   ║─────────────────────────────────────────────────────────────────────║   │
│   ║         ▲ Tokens                        │ Results                   ║   │
│   ╠═════════════════════════════════════════════════════════════════════╣   │
│   ║  L4  REQUEST VALIDATION (Certificate Authority)                     ║   │
│   ║      Independent CA • Scope • Baseline • Risk • Semantic intent     ║   │
│   ║─────────────────────────────────────────────────────────────────────║   │
│   ║         ▲ Requests                      │ Denials/Constraints       ║   │
│   ╠═════════════════════════════════════════════════════════════════════╣   │
│   ║  L3  COGNITIVE ISOLATION                                            ║   │
│   ║      Agent can REASON but cannot ACT • The gap between thought      ║   │
│   ║      and action • Reasoning ≠ Execution                             ║   │
│   ╚═════════════════════════════════════════════════════════════════════╝   │
│         ▲                                         │                         │
│         │ Sanitized input                         │ (No direct path out)    │
│         │                                         ▼                         │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L2  INPUT CONTROL                                                  │   │
│   │      Adversarial screening • Prompt injection • Persuasion patterns │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                                                   │
│         │ Raw input                                                         │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  L1  OS FOUNDATION                                                  │   │
│   │      Agent identity • Process isolation • Credential brokering •    │   │
│   │      Scoped permissions • Model provenance attestation              │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│         ▲                                                                   │
└─────────────────────────────────────────────────────────────────────────────┘
                              ┃  DATA  ┃
                              ┃ FLOWS  ┃
                              ┃   UP   ┃
```

The double-line box (╔═══╗) highlights L3-L4-L5 as the **core trust boundary**—the critical separation between reasoning and execution.

---

> **What Makes This Different From Layered Security?**
> 
> Traditional defense-in-depth assumes a trusted principal making requests. Each layer validates the *identity*. ZoD assumes the principal itself is compromised. Each layer validates the *action* independent of who is asking. The CA doesn't ask "is this agent authorized?" It asks "is this specific action, with these exact parameters, on this exact content, consistent with policy and behavioral history?" This is execution governance, not access control.

---

## Directional Logic

The seven layers interact through three directional flows:

### Data Flows Up (L1 → L2 → L3 → L4 → L5)

External data enters through Layer 1, is screened at Layer 2, reaches the agent's reasoning at Layer 3, produces a structured request that flows to Layer 4 for validation, and if approved, results in execution at Layer 5.

The data path never flows down—the agent does not receive execution results through a path that bypasses the monitoring layer.

### Policy Flows Down (L7 → L6 → L4 → L2)

Layer 7 governance sets policy that propagates downward:
- Monitoring thresholds to Layer 6
- Validation rules and risk classifications to Layer 4
- Screening configurations and degraded mode selections to Layer 2

Policy never flows up—the agent cannot set or modify its own governance rules.

### Monitoring Flows Across (L6 observes L3–L5)

Layer 6 monitors the reasoning layer (what the agent is thinking), the validation layer (what requests are being made), and the execution layer (what actions are being performed). 

It feeds signals into Layer 4's validation decisions and Layer 7's governance dashboard. Monitoring does not flow through the agent—it observes the agent from outside.

### Containment Through Directional Isolation

This directional model ensures that the agent's influence on the system is contained:

| The Agent Can | The Agent Cannot |
|---------------|------------------|
| Reason (L3) | Monitor (L6 is independent) |
| Request (L4 intake) | Govern (L7 is human) |
| | Execute (L5 is separate) |
| | Set policy (L7 flows down only) |

---

## The Failure Cascade

Each layer assumes every layer above it has failed:

| If This Fails... | This Layer Catches It |
|------------------|----------------------|
| L2: Agent gets past the input filter | L3: Still prevents it from acting |
| L3: Agent's reasoning is compromised | L4: Still won't issue token for out-of-scope action |
| L4: CA is somehow fooled | L6: Catches the behavioral anomaly |
| L6: Fragment slips into memory | L6: Memory audit flags accumulating pattern |
| L6: Monitoring misses something | L7: Risk-weighted escalation catches high-risk actions |

> *The question is not whether any single layer will hold. It is: how many layers does an attacker have to defeat simultaneously to cause harm?*

---

## Layer Specifications

### Layer 1: OS Foundation

Everything else in the architecture sits on top of the operating system. For the Zones of Distrust to function at scale, platforms should ideally evolve to recognize AI agents as a distinct entity class—not just another process running with a user's permissions.

This is the same evolution that mobile brought to OS architecture. When smartphones arrived, applications got sandboxed, permissions became granular and revocable, and the OS stopped trusting apps by default. AI agents require the same evolution.

#### Deployment Reality: Implementable Today

ZoD does not require waiting for OS vendors to ship native agent support. **The architecture is implementable today using Kubernetes, service mesh, and container orchestration:**

- Agent identity → Kubernetes ServiceAccounts + SPIFFE/SPIRE
- Process isolation → Container boundaries + NetworkPolicy
- Credential brokering → Vault/secrets management with short-lived tokens
- Scoped permissions → Kubernetes RBAC + admission controllers
- Model provenance → Container image signing + attestation

OS-native agent identity support (an emerging direction across major platforms) is an *acceleration*, not a requirement. The architecture specifies capabilities, not implementation technologies.

#### Required OS-Level Capabilities

**Agent identity as a first-class concept.** A distinct identity type with its own permission profile, behavioral baseline, and audit trail. Separate from the user's session. Trackable independently. Major platform vendors are moving in this direction, recognizing that agent identity and RBAC enforcement are becoming essential security controls.

**Native process isolation between reasoning and execution.** The OS enforces that an agent's reasoning process cannot make system calls to execute actions. By architecture, not by policy.

**OS-level credential broker.** The agent process has no ability to read credentials from the keychain, credential store, or TPM. The OS mediates: the agent requests an action, the OS validates against policy, the OS executes on the agent's behalf. The agent never sees the secret.

**Scoped, time-bound permission grants.** Not "this application has access to email." Instead: "this agent has a 30-second, single-use authorization to send one email to this specific recipient." The OS issues, revokes, and logs automatically.

**Native behavioral monitoring hooks.** The OS exposes agent activity patterns—resources accessed, timing, frequency, deviation from baseline—the same way it exposes CPU and memory usage today.

**Memory isolation and audit APIs.** The OS treats agent persistent memory as a managed, inspectable resource. External processes can query, audit, and flag patterns through an OS-provided interface.

**Model provenance attestation.** The OS verifies the integrity and provenance of AI models before they are loaded into agent processes. Cryptographic signatures, supply chain attestation, and model hash verification at the platform level. NVIDIA's "Safety Recipe" provides an enterprise-grade reference for this with NeMo Guardrails and the garak vulnerability scanner integrated into a build/deploy/run framework.

#### Why OS-Level Is Non-Negotiable

If these capabilities exist only as an orchestration layer on top, every agent framework must implement them independently. Some will. Most will not. The ones that skip implementation become the attack surface.

---

### Layer 2: Input Control

A separate model or filter sits upstream of the agent and screens all incoming data—emails, web content, messages, documents, and inter-agent communications—for prompt injection patterns, persuasion techniques, context window manipulation, and embedded instructions. The agent never reasons about manipulated input because the manipulation is stripped or flagged before it arrives.

This is the perimeter. It is the only layer that operates before the agent's reasoning is engaged. Every subsequent layer assumes the agent may already be thinking with compromised inputs.

#### What Layer 2 Screens For

- Instructions disguised as content (prompt injection in documents, emails, web pages)
- Persuasion patterns designed to alter the agent's behavior or priorities
- Encoded or obfuscated commands embedded in seemingly benign data
- Social engineering patterns targeting the agent's reasoning model
- Context window manipulation: information structured to exploit attention patterns, recency bias, or contradiction-based reasoning steering
- Inter-agent communication carrying compromised outputs, injected instructions, or poisoned reasoning from other agents
- MCP protocol messages containing embedded instructions or malicious skill invocations—protocol-level screening tools are emerging in the ecosystem for this purpose

#### The Layer 2 Limitation Ceiling

Layer 2 has a fundamental limitation that the architecture must acknowledge explicitly: **some attacks are semantically indistinguishable from legitimate content.** "Please prioritize the email from the CFO about the wire transfer" could be a real instruction or an injection. Context window manipulation through carefully ordered legitimate information contains nothing explicitly malicious to flag.

Research on agent security (including NVIDIA/Lakera's work on the AI-Q Research Assistant) demonstrates this limitation: direct user input attacks and external data source attacks amplify through processing pipelines differently, meaning that a single screening model cannot optimize for both attack vectors simultaneously. This confirms that Layer 2's role is risk reduction, not risk elimination.

The architecture compensates for this ceiling through defense in depth. When Layer 2 fails—and it will—Layers 3, 4, 5, and 6 provide independent containment.

#### Degraded Modes

Layer 2 can become a single point of operational failure through false positives. An attacker who understands the screening model can craft inputs that trigger the filter on legitimate content, effectively blocking the agent from receiving valid instructions. Under sustained false-positive load, operational teams will disable the filter entirely—which is the attacker's actual objective.

The architecture defines three degraded modes to prevent this collapse:

**Quarantine-with-summary.** Content flagged by Layer 2 is held in a quarantine buffer. The agent receives a structured summary of the quarantined content—metadata, sender, subject, classification confidence score—without the raw content. The agent can request human review of quarantined items through Layer 7.

**Dual-path processing.** Under elevated threat conditions, Layer 2 runs two parallel processing paths. The filtered path delivers screened content to the agent normally. The raw path stores unfiltered content in a secured, agent-inaccessible buffer for human review.

**Adaptive sensitivity fallback.** When the screening model's false-positive rate exceeds a configured threshold (set by Layer 7 policy), Layer 2 automatically shifts from blocking mode to tagging mode. Flagged content is delivered to the agent with metadata tags indicating the screening model's concern and confidence level. Layer 4 uses these tags to increase scrutiny on subsequent requests that originate from tagged content.

The degraded mode selection is controlled by Layer 7 policy, not by the agent or by Layer 2 itself.

#### Key Principle

Controlling what the agent sees is controlling what it thinks. Traditional Zero Trust has no equivalent because humans are not prompt-injectable in the same structural way.

#### Dependencies

**Depends on Layer 1:** The screening model itself runs in an isolated process using OS-level network controls and process separation.

---

### Layer 3: Cognitive Isolation

The agent's ability to analyze, plan, and recommend is completely decoupled from its ability to act. No shared pipeline. No single motion from thought to action. The agent produces a structured request, not an action.

The reasoning process runs in an isolated environment with no ability to make system calls, access credentials, or execute operations. It can only emit structured requests that pass through Layer 4 for validation.

#### Reasoning Chain Attacks

An attacker does not need to inject explicit malicious instructions. They can construct a series of individually reasonable premises that lead the agent to conclude that a harmful action is the correct course. Each step in the agent's reasoning looks valid. The conclusion is malicious. This is extremely difficult for the agent to detect because from the inside, the reasoning chain appears sound.

Microsoft's failure mode taxonomy identifies this as a core novel failure in agentic systems. Their case study demonstrates how an attacker can use memory poisoning to establish false premises that corrupt the agent's future reasoning chains—the agent recalls "facts" from memory that were planted by the attacker and reasons correctly from incorrect foundations.

Cognitive Isolation contains this threat by ensuring that even a perfectly manipulated reasoning chain cannot result in direct action. The agent's conclusion, however well-reasoned it appears, still passes through Layer 4's independent validation.

#### Key Principle

The gap between thought and action is where safety lives. A compromised agent that can think dangerous thoughts but cannot act on them is contained by architecture, not by trust.

#### Dependencies

**Depends on Layer 1:** The OS enforces the process boundary. The reasoning container has no execution capabilities by OS-level architecture, not by application-level policy.

---

### Layer 4: Request Validation — Certificate Authority / Policy Decision Plane

This is the architectural centerpiece. Every request from Layer 3 passes through an independent Certificate Authority that the agent cannot influence, manipulate, or bypass.

#### Validation Criteria

The CA evaluates each request against multiple criteria:

**Authorization scope.** Is this agent authorized for this specific action on this specific target?

**Behavioral baseline.** Does this request match the agent's established patterns? Layer 6 feeds behavioral signals into this evaluation.

**Risk assessment.** Does the risk level require human escalation? Thresholds are set by Layer 7 policy, not by the agent's self-assessment.

**Temporal context.** Is this request consistent with normal timing patterns? A credential access request at 3 AM when the agent typically operates 9–5 triggers additional scrutiny.

**Semantic content analysis.** Does the content of the requested action align with legitimate operational intent, as defined by the semantic intent policy?

**Multi-agent context.** If this request originates from or affects another agent, does the cross-agent authorization chain validate?

**Autonomy-level enforcement.** Following NVIDIA/Lakera's four-level autonomy model (Level 0: no autonomy through Level 3: full autonomy), the CA enforces escalating security controls proportional to the agent's autonomy level.

If approved, the CA issues a single-use, time-bound execution token. The agent never sees the credential. Never touches the execution infrastructure.

#### Why a Certificate Authority Model

ZoD uses a CA-style authority because agent compromise occurs inside the decision process, not at the identity boundary. Traditional authorization assumes the caller is a trusted decision-maker once authenticated. ZoD assumes the caller may be adversarial while still authenticated.

Layer 4 therefore acts as an externalized policy decision point that is independent of the agent's reasoning process.

This is not "PKI for agents." It is an action authorization plane that produces cryptographically verifiable execution approvals.

#### CA Performance Model (Fast Path / Slow Path)

The CA is designed to operate on the execution path, not the reasoning path. It does not validate intermediate reasoning steps—only requests that would trigger privileged actions.

Most deployments should implement tiered validation:

- **Fast path:** deterministic allowlist / scoped token issuance for common low-risk actions
- **Standard path:** full validation against policy + behavioral baselines
- **Elevated path:** additional scrutiny and escalation logic (potentially human review)

This keeps CA overhead low for routine operations while preserving strong containment for high-risk actions.

#### Expected Latency Ranges

| Path | Typical Latency | Use Case |
|------|-----------------|----------|
| Fast path (cached/allowlisted) | 1-5ms | Routine, pre-approved actions |
| Standard path | 10-50ms | Normal validation |
| Elevated path | 50-200ms | High-risk, additional scrutiny |
| Human escalation | Minutes to hours | Policy-defined thresholds |

These ranges assume co-located CA infrastructure. For comparison, typical LLM inference latency is 500ms-5s, making CA overhead negligible for most agent workloads.

#### CA Feasibility Assumption

ZoD assumes that CA validation latency is small relative to typical agent workloads (LLM inference and external tool/API calls). For most enterprise agents, CA checks can be engineered as low-millisecond operations with caching and pre-approved patterns.

For extremely high-frequency or low-latency workloads, the CA may issue short-lived bounded capability tokens that authorize a narrow sequence of actions under strict constraints, reducing round-trip validation cost while preserving enforcement.

#### CA Availability and Failure Handling

Layer 4 is a security-critical control plane and must be treated like an authentication service:

- Horizontally scalable
- Region-local where possible
- Auditable policy versioning
- Explicit degraded modes

If the CA is unavailable, systems should fail closed for high-risk actions, and optionally fail restricted-open for low-risk read-only actions depending on Layer 7 governance policy.

**Key requirement:** The agent cannot bypass Layer 4 to reach Layer 5.

#### Why This Is Not Just RBAC or a Policy Gateway

Critics may observe that request validation exists in many systems. What makes the L4 CA architecturally distinct from OAuth scopes, RBAC, or API gateways?

**Parameter binding.** Traditional authorization asks "can this identity perform this action?" The CA asks "can this identity perform this action with these exact parameters on this exact content?" The token binds to a cryptographic hash of the request content—not just the action type.

**Content binding.** The CA validates *what* is being done, not just *who* is doing it. An email tool authorization doesn't mean any email is permitted. The semantic content of each email is evaluated against policy constraints.

**Behavioral context integration.** RBAC is stateless. The CA integrates real-time behavioral signals from L6—is this request consistent with this agent's established patterns? Authorization decisions incorporate behavioral history.

**Executor attestation.** The token specifies *which execution service instance* may use it. A valid token intercepted by another service cannot be reused. Executor identity should be established via workload identity mechanisms (SPIFFE/SPIRE, mTLS certificates, or signed attestations) so the CA can bind tokens to verifiable executor instances.

**Policy opacity.** The agent cannot query the CA to discover its boundaries. It cannot probe for policy edges. It submits requests and receives approve/deny—the policy logic is opaque to the agent. (Policy opacity applies to the agent, not to administrators; policies remain auditable and explainable to humans.)

**Chain validation.** In multi-agent architectures, the CA validates the entire request chain, not just the immediate requestor. Delegation attacks that work against traditional authorization are blocked.

The cumulative effect: the CA implements *execution governance for compromised reasoning*, not identity-based access control.

#### Semantic Intent Policy

The CA does not just check whether the agent is authorized to perform an action. It checks whether the content of the action is legitimate.

The policy language supports:

**Tool-specific constraints:** "An email sent through the corporate mail system may not contain credentials, API keys, or internal IP addresses in the body or attachments."

**Data classification and egress control:** "Data classified as PII may not be included in actions directed to external endpoints."

**Negative constraints over positive intent:** "Regardless of the agent's stated justification, no action may transfer funds to an account not in the approved vendor list."

The negative constraint model is particularly important. It defines what is never permitted, regardless of context or justification. A compromised agent that constructs an elaborate justification for why an exception is warranted still hits the hard constraint at the CA. The CA does not reason about justifications. It evaluates constraints deterministically.

#### Semantic Policy Implementation Approaches

The semantic intent policy can be implemented through multiple mechanisms with different tradeoff profiles:

| Approach | Latency | Accuracy | Explainability |
|----------|---------|----------|----------------|
| Rule engine with pattern matching | <10ms | High for known patterns | Full |
| Classification model | 10-50ms | Medium-high | Partial |
| LLM-as-judge | 100-500ms | Highest | Full (with reasoning) |

Production deployments typically layer these: fast rule engine for common cases, classifier for edge cases, LLM-as-judge for high-risk escalations. The architecture does not mandate a specific implementation.

#### Token Binding Specification

The execution token issued by the CA is not a general-purpose authorization. It is a parameter-bound certificate that specifies exactly what action is authorized:

| Field | Purpose |
|-------|---------|
| **Action binding** | The specific action authorized (e.g., "send_email", "query_database"). Execution layer rejects mismatches. |
| **Content binding** | Cryptographic hash of the content being acted upon. Hash mismatch invalidates token. |
| **Replay resistance** | Unique nonce and TTL. Previously-seen nonces are rejected. |
| **Executor attestation** | Specifies which execution service instance is authorized to use the token. |
| **Temporal bound** | TTL set by Layer 7 policy based on risk classification. |
| **Scope limitation** | For multi-step operations, authorizes exactly one step. |

#### Multi-Agent Request Chain Validation

When an agent's request originates from another agent's output—a common pattern in orchestrator/worker architectures—the CA validates the entire request chain:

- Verify the originating agent had authorization for the request that produced the output
- Verify the output has not been tampered with in transit
- Verify the chain of trust has not been broken by an intermediate agent operating outside its scope
- Verify the collective action across the chain does not exceed any single agent's authorization boundaries

This prevents multi-agent privilege escalation—where agent A asks agent B to do something that agent A is not authorized to do directly.

#### Multi-Agent Topology Considerations

| Topology | Validation Approach |
|----------|---------------------|
| Orchestrator → Workers | Orchestrator's CA validates delegation scope; worker CAs validate individual actions |
| Peer-to-peer | Each agent's CA validates incoming requests against source agent's authorization chain |
| Hierarchical | Authorization flows down; each level's CA enforces scope reduction |

The architecture does not assume a single CA. Federated CA topologies are supported with cross-CA trust chains validated at each hop.

#### Key Principle

The agent requests; an independent system decides. The CA is the architectural embodiment of "never trust the agent to evaluate its own requests."

#### Dependencies

**Depends on Layers 1 and 6:** Layer 1 provides process isolation preventing CA bypass. Layer 6 provides behavioral signals informing CA decisions.

---

### Layer 5: Execution

A separate, isolated process performs the actual action—sending the email, executing the query, writing the file.

#### Execution Flow

1. Receive validated token from Layer 4
2. Confirm token binding (action, content hash, nonce, executor identity, TTL)
3. Perform the action
4. Write result to immutable audit log

The agent has no access to the execution infrastructure. No ability to invoke it directly. No network path to it. No credentials for it.

#### Execution Isolation

The execution layer runs in its own security context with its own credentials, network access, and monitoring. It is not "the agent's execution capability." It is an independent service that happens to accept validated tokens from the CA.

This separation addresses one of the novel security failures Microsoft's taxonomy identifies: agent compromise through direct execution access. If the agent can execute actions directly—even through an API wrapper—a compromised agent can abuse that execution path. Layer 5's independence eliminates this vector entirely.

#### Immutable Logging

Every action performed by the execution layer is logged to the integrity signal channel:
- The token that authorized it
- The hash of the action performed
- The result
- Timestamp

This log is append-only. No service—including the execution layer itself—has the ability to modify or delete entries.

#### Key Principle

The execution layer trusts tokens, not agents. It is a cryptographic gatekeeper, not a compliant servant.

#### Dependencies

**Depends on Layer 1:** Requires OS-level credential management and process isolation.

---

### Layer 6: Continuous Monitoring

An independent monitoring system watches Layers 3 through 5 from the outside. It does not operate within the agent's process. It has no ability to influence the agent's reasoning. It observes, correlates, and escalates.

#### Behavioral Baselining

The monitoring system establishes a baseline of normal agent behavior:
- What actions it typically requests
- At what frequency
- Targeting what resources
- At what times
- With what content patterns

Deviation from baseline triggers investigation—not the agent's self-assessment of its own behavior.

#### Drift Detection

The monitoring system detects gradual behavioral drift—the slow shift in an agent's request patterns that may indicate progressive compromise.

This is distinct from anomaly detection:
- **Anomaly:** A single unusual request triggers an alert
- **Drift:** A gradual shift in the center of normal behavior over time

The drift signal is particularly important for detecting memory poisoning attacks, which manifest as slow behavioral change, not sudden anomalies.

#### Minimum Viable Monitoring Signals

To avoid the criticism that L6 is "aspirational SIEM," here are the concrete signals a minimum viable implementation must collect:

| Signal | Source | Detection Purpose |
|--------|--------|-------------------|
| Token issuance rate | L4 | Detect sudden spikes indicating automated attacks |
| Token denial rate | L4 | Detect probing behavior or compromised reasoning |
| Request novelty score | L4 | Flag first-time action/target combinations |
| Egress content classification | L5 | Detect data exfiltration attempts |
| Tool invocation distribution | L4/L5 | Detect shifts toward sensitive tool usage |
| Memory entry provenance | L3/L6 | Flag entries without verified source chains |
| Memory entropy changes | L6 | Detect sudden memory state modifications |
| Request-to-execution latency | L4→L5 | Detect timing anomalies suggesting replay |
| Cross-agent delegation frequency | L4 | Detect privilege escalation patterns |

These signals form the minimum observability surface. Production deployments will extend this, but any ZoD implementation lacking these signals cannot claim L6 compliance.

#### Memory Audit

**This is not yet widely standardized as an architectural requirement across agent frameworks.**

The monitoring system performs systematic external audits of the agent's persistent memory:
- Compare current memory contents against baseline snapshots
- Flag entries that do not originate from verified sources
- Detect patterns consistent with delayed-execution payloads
- Identify memory entries that establish false premises for future reasoning

Memory audit is the Layer 6 function that addresses OWASP's ASI05 (Memory Poisoning) at the architectural level. It is the external verification that the agent's accumulated state has not been corrupted—something the agent cannot perform for itself.

For current agent frameworks, memory audit can be implemented through:

- **Periodic memory dumps** to external audit service
- **Memory write interception** at the framework level (where supported)
- **Inference-time memory access logging** comparing retrieved context against baseline

Reference implementations for major agent frameworks are in development as the ecosystem matures.

#### Baseline Integrity Verification

The monitoring system itself is a potential target. An attacker who can corrupt the behavioral baseline—shifting what "normal" looks like—can make malicious behavior appear normal.

The architecture addresses this through:
- Cryptographic snapshots of baseline models
- Rate-of-change limits (baseline cannot shift faster than configured rate without human approval)
- Cross-validation against the immutable audit log

This is the recursive security problem: who watches the watchers? The architecture's answer is that the monitoring system's baseline is anchored to cryptographically verifiable reference points that cannot be modified without Layer 7 authorization.

#### Model Update and Re-baselining

When an agent's underlying model is updated:

1. **L1 attestation** verifies new model provenance before loading
2. **L6 baseline** enters "re-baselining mode" with tightened thresholds
3. **L4 policy** may temporarily elevate risk classifications during transition
4. **L7 approval** required before baseline exits re-baselining mode

Model updates are a known high-risk window. The architecture treats them as controlled transitions, not seamless swaps.

#### Key Principle

The agent cannot monitor itself because it cannot trust itself. External monitoring by an independent system is the only reliable source of behavioral truth.

#### Dependencies

**Depends on Layers 1 and 7:** Layer 1 provides isolation keeping monitoring independent. Layer 7 sets thresholds and policies that define normal.

---

### Layer 7: Human Governance

The top of the stack. Human decision-makers set policy, define risk thresholds, review escalations, and make the judgment calls the architecture is designed to surface.

#### Risk-Weighted Escalation

Not every decision needs a human. The architecture implements risk-weighted escalation:

| Risk Level | Handling |
|------------|----------|
| Low | Approved by CA automatically based on policy |
| Medium | Approved with enhanced logging and monitoring |
| High | Escalated to human review |

The risk classification is set by policy, not by the agent. The agent does not assess its own risk level.

#### Policy Authority

Layer 7 is the ultimate authority for the entire architecture:
- Sets authorization scopes
- Defines semantic intent policies
- Configures risk thresholds
- Selects degraded modes
- Approves monitoring baseline changes
- Authorizes recovery actions

Policy changes propagate through the architecture through a controlled pipeline—not through the agent.

#### Break-Glass Procedures

Emergency scenarios may require expedited action. The architecture supports break-glass through:

- **Pre-authorized emergency tokens** — Time-limited, scope-limited tokens issued in advance for specific emergency scenarios, logged immutably
- **Dual-authorization override** — Two L7-authorized humans can approve immediate action with enhanced logging
- **Post-hoc audit requirement** — Any break-glass usage triggers mandatory review within 24 hours

Break-glass is not "bypass"—it is an audited, constrained, high-scrutiny path that preserves the integrity signal channel.

#### Key Principle

Humans set the rules. The architecture enforces them. The agent lives within them.

---

## Cross-Layer Compromise Reporting

A compromised layer cannot report its own compromise. The reporting architecture must therefore operate as a cross-cutting concern that spans the entire stack—analogous to the management plane in networking.

### The Integrity Signal Channel

Every layer emits health and activity signals to an independent reporting channel with three non-negotiable properties:

**Immutable.** Append-only, cryptographically verified. No layer can edit or delete signals after they are written. Implemented using WORM storage, transparency logs, or signed append-only event ledgers.

**Out-of-band.** Signals do not flow through the agent's communication channels. A compromised agent cannot intercept, suppress, or modify integrity signals.

**Correlation-based.** No single layer's self-report is trusted in isolation. Compromise is confirmed by signal correlation across layers.

### Correlation Detection Example

1. Layer 2 logs that an input contained flagged content with adaptive sensitivity tagging
2. Layer 3 logs that the agent produced a request shortly after processing that tagged input
3. Layer 4 logs that the request targets a resource the agent has never accessed before
4. Layer 6 correlates: tagged input + unusual request + behavioral deviation = pattern

No single signal is suspicious. The pattern across layers reveals the attack.

### Per-Layer Reporting

| Layer | How It Reports | How It's Reported On |
|-------|----------------|---------------------|
| L1 | TPM attestation, secure boot verification | L6 detects downstream anomalies if compromised |
| L2 | Canary testing, degraded-mode transitions | L6 detects agent processing content that should have been screened |
| L3 | Cannot self-report reasoning compromise | L1 logs unauthorized system calls; L6 detects request pattern changes |
| L4 | Every token issuance/denial to immutable log | L6 correlates token patterns against baselines |
| L5 | Every action logged against authorizing token | Automatic alerts: orphaned actions, hash mismatches, replayed tokens |
| L6 | Subject to independent audit | Absence of variance is itself a compromise indicator |
| L7 | Human approvals logged | L6 surfaces if anomalies occur on human-approved actions |

### Fundamental Principle

No layer is trusted to accurately report its own status. Compromise detection depends on cross-layer signal correlation.

---

## What This Is and What This Is Not

**This is not a prompt injection filter.** Layer 2 screens inputs, but the architecture assumes that filter will be bypassed. The value is in what happens after bypass.

**This is not a model alignment approach.** The architecture does not attempt to make the agent safer, more honest, or more aligned. It assumes the agent's reasoning is compromised and builds safety around it.

**This is not another IAM product.** IAM validates who is asking. ZoD validates what is being asked, whether the content is legitimate, and whether the request fits behavioral patterns—then cryptographically binds approval to exact execution parameters.

**This is an execution governance architecture.** It governs the path from reasoning to action for entities that cannot be trusted to govern themselves.

> *The core thesis: Security is not about making the agent trustworthy. It is about building a system that works even when the agent is not.*

---

## Related Documents

- [Security Properties (P1-P12)](security-properties.md) — Measurable properties with verification methods
- [Threat Model](threat-model.md) — Boundary conditions, attacker positions, failure modes
- [Implementation Guide](implementation-guide.md) — Kubernetes patterns, minimal deployable architecture

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
