**ZONES OF DISTRUST**

*Breach is the premise. Containment is the architecture*

Open reference architecture for securing autonomous AI agents

Version 0.9 RFC --- February 2026
Created by BluVi, stewarded in the open.

# Contents

[Executive Summary(#executive-summary)]

[What This Is and What This Is Not [4](#what-this-is-and-what-this-is-not)](#what-this-is-and-what-this-is-not)

[The Problem: Why Current Approaches Fail [5](#the-problem-why-current-approaches-fail)](#the-problem-why-current-approaches-fail)

[The Fundamental Asymmetry [5](#the-fundamental-asymmetry)](#the-fundamental-asymmetry)

[Four Structural Blindnesses [6](#four-structural-blindnesses)](#four-structural-blindnesses)

[The Emerging Threat Landscape [6](#the-emerging-threat-landscape)](#the-emerging-threat-landscape)

[Multi-Agent Trust: The Unaddressed Surface [7](#multi-agent-trust-the-unaddressed-surface)](#multi-agent-trust-the-unaddressed-surface)

[Model Supply Chain Integrity [7](#model-supply-chain-integrity)](#model-supply-chain-integrity)

[Tool and API Semantic Abuse [7](#tool-and-api-semantic-abuse)](#tool-and-api-semantic-abuse)

[The AI's Own Assessment [8](#the-ais-own-assessment)](#the-ais-own-assessment)

[Threat Model Summary [8](#threat-model-summary)](#threat-model-summary)

[Catastrophic Outcomes [8](#catastrophic-outcomes)](#catastrophic-outcomes)

[Attacker Positions [8](#attacker-positions)](#attacker-positions)

[Kill Chain Mapping [9](#kill-chain-mapping)](#kill-chain-mapping)

[Trust Assumptions [9](#_Toc221549795)](#_Toc221549795)

[Explicit Non-Goals [9](#explicit-non-goals)](#explicit-non-goals)

[Security Properties [10](#security-properties)](#security-properties)

[The Zones of Distrust: A Seven-Layer Model [11](#the-zones-of-distrust-a-seven-layer-model)](#the-zones-of-distrust-a-seven-layer-model)

[Layer 1: OS Foundation [11](#_Toc221549799)](#_Toc221549799)

[Layer 2: Input Control [11](#layer-2-input-control)](#layer-2-input-control)

[Layer 3: Cognitive Isolation [12](#layer-3-cognitive-isolation)](#layer-3-cognitive-isolation)

[Layer 4: Request Validation --- The AI Certificate Authority [12](#layer-4-request-validation-the-ai-certificate-authority)](#layer-4-request-validation-the-ai-certificate-authority)

[Layer 5: Execution [14](#layer-5-execution)](#layer-5-execution)

[Layer 6: Continuous Monitoring [14](#layer-6-continuous-monitoring)](#layer-6-continuous-monitoring)

[Layer 7: Human Governance [16](#layer-7-human-governance)](#layer-7-human-governance)

[Directional Logic: How the Layers Interact [16](#directional-logic-how-the-layers-interact)](#directional-logic-how-the-layers-interact)

[The Failure Cascade [17](#the-failure-cascade)](#the-failure-cascade)

[Attack Walkthrough: Wire Transfer Fraud [17](#attack-walkthrough-wire-transfer-fraud)](#attack-walkthrough-wire-transfer-fraud)

[Why This Isn't Just SIEM + Token Proxy [17](#why-this-isnt-just-siem-token-proxy)](#why-this-isnt-just-siem-token-proxy)

[Zero Trust and Beyond [18](#zero-trust-and-beyond)](#zero-trust-and-beyond)

[What Zero Trust Provides [18](#what-zero-trust-provides)](#what-zero-trust-provides)

[Where Zero Trust Wasn't Designed to Go [18](#where-zero-trust-wasnt-designed-to-go)](#where-zero-trust-wasnt-designed-to-go)

[The ZoD Approach [19](#the-zod-approach)](#the-zod-approach)

[Current Landscape Analysis [19](#current-landscape-analysis)](#current-landscape-analysis)

[What Industry Is Already Doing [19](#what-industry-is-already-doing)](#what-industry-is-already-doing)

[What Remains Novel [20](#what-remains-novel)](#what-remains-novel)

[Capability Comparison [21](#capability-comparison)](#capability-comparison)

[Regulatory and Compliance Alignment [21](#regulatory-and-compliance-alignment)](#regulatory-and-compliance-alignment)

[Implementation Path [21](#implementation-path)](#implementation-path)

[What You Can Build Today [21](#what-you-can-build-today)](#what-you-can-build-today)

[Minimum Safe Configuration [22](#minimum-safe-configuration)](#minimum-safe-configuration)

[Control Evidence (Illustrative) [22](#control-evidence-illustrative)](#control-evidence-illustrative)

[Post-Compromise Recovery [22](#post-compromise-recovery)](#post-compromise-recovery)

[What Requires OS Evolution [22](#what-requires-os-evolution)](#what-requires-os-evolution)

[Why the CA Is Not a New Class of Operational Risk [23](#why-the-ca-is-not-a-new-class-of-operational-risk)](#why-the-ca-is-not-a-new-class-of-operational-risk)

[Conclusion [23](#conclusion)](#conclusion)

[Getting Involved [23](#getting-involved)](#getting-involved)

[Documentation [23](#documentation)](#documentation)

[Contribute [24](#_Toc221549829)](#_Toc221549829)

# Executive Summary

AI agents are no longer theoretical. They read emails, manage calendars, execute code, access databases, and act on behalf of humans across enterprise systems. They are autonomous entities operating with real credentials, making real decisions, in real production environments.

The security architecture protecting them was designed for a fundamentally different kind of entity.

Zero Trust gave us the right philosophy: never trust, always verify. But Zero Trust was built for users and devices --- entities that know when they have been compromised or at least can be forensically interrogated after the fact. AI agents break that assumption. A prompt-injected agent does not know it has been compromised. The manipulation becomes its genuine reasoning. There is no \"real agent\" underneath to recover.

This paper introduces the **Zones of Distrust**, a layered security architecture that extends Zero Trust principles into a domain they were never designed for: autonomous entities that can be compromised without knowing it, treat their own manipulation as truth, and accumulate exploitable state across sessions simply by doing their job.

The architecture is organized as seven interdependent layers:

-   **Data flows up** from the external world through screening, into reasoning, out as requests, through validation, and into execution.

-   **Monitoring flows across** Layers 3 through 5, feeding signals into validation and governance.

-   **Policy flows down** from human governance through validation into the entire stack.

Each layer depends on the layer below it. Each layer assumes every layer above it has already been compromised.

## What This Is and What This Is Not

**This is not a prompt injection filter.** Layer 2 screens inputs, but the architecture assumes that filter will be bypassed. The value is in what happens after bypass, not in the filter itself.

**This is not a model alignment approach.** The architecture does not attempt to make the agent safer, more honest, or more aligned. It assumes the agent's reasoning is compromised and builds safety around it.

**This is not another identity and access management product.** Traditional IAM validates who is asking and assumes requestor intent is stable. The Zones of Distrust assume requestor reasoning is adversarial. The architecture validates what is being asked, whether the content of the action is legitimate, and whether the request fits the agent's behavioral pattern---then cryptographically binds the approval to exact execution parameters.

**This is an execution of governance architecture.** It governs the path from reasoning to action for entities that cannot be trusted to govern themselves. Every layer exists to ensure that what the agent wants to do is evaluated, constrained, authorized, executed, and audited by systems the agent does not control.

**What makes this different:** ZoD is not novel because it introduces validation and monitoring---those exist. It is novel because it assumes the requestor's reasoning is adversarial and therefore decouples cognition from execution. The agent cannot act; it can only request. An independent system decides.

**This is incremental, not a moonshot.** Most organizations can implement Layers 3--5 (cognitive isolation, request validation, and execution) in weeks using containers, a token-gated proxy, and existing policy infrastructure. Full implementation adds depth; partial implementation still adds value.

*Core thesis: The agent is compromised. The architecture limits blast radius.*

**Applicability:** Zones of Distrust are intended for production AI agents with autonomous execution capability in enterprise or regulated environments. It is not intended for experimental, advisory-only, or human-in-the-loop systems where agents do not execute privileged actions. Organizations deploying agents that only provide recommendations, without direct system access, do not require ZoD.

# The Problem: Why Current Approaches Fail

## The Fundamental Asymmetry

When a human user is phished, there is a distinction between the user and the compromise. The user's intent and the attacker's actions are separable. Forensics can reconstruct what happened. The user can be notified, the session terminated, and the damage assessed against a baseline of known-good behavior.

An AI agent that has been prompt-injected has no such separation. The compromised reasoning IS the agent's reasoning. There is no \"real agent\" underneath whose intent can be recovered. The manipulation did not override the agent's judgment---it became the agent's judgment. The agent does not experience compromise as an attack. It experiences it as a legitimate instruction.

This is not a theoretical vulnerability. It is a structural property of how large language models process information. And it fundamentally changes the threat model.

## Four Structural Blindnesses

**Prompt injections are invisible to the target.** A well-crafted prompt injection---embedded in an email, a web page, a document, a calendar invite---looks identical to legitimate content from the agent's perspective. The agent cannot tell the difference between a real instruction and a manipulated one.

**Context window manipulation is distinct from injection.** Beyond explicit prompt injection, an agent's reasoning can be degraded or steered through manipulation of its context window without any injected instructions. Flooding the context with irrelevant information pushes important safety context out of the agent's attention window. These influence operations against the agent's reasoning architecture---structural, not semantic, which makes them fundamentally harder to detect.

**Agents treat their own memory as ground truth.** AI agents with persistent memory accumulate context across sessions. This is what makes them useful. But memory is also an attack surface. Fragments planted across multiple sessions can assemble into a coherent malicious payload that the agent recalls and acts on as established context.

**Self-monitoring is structurally impossible.** An agent cannot reliably detect its own compromise because the compromised version takes its own behavior at face value. Asking a prompt-injected agent to evaluate whether it has been prompt-injected is asking the attack to audit itself.

## The Emerging Threat Landscape

Organizations are rapidly adopting agentic AI without adequate security frameworks. Recent industry research reveals critical readiness gaps: 84% of organizations doubt they could pass a compliance audit focused on agent behavior or access controls, with only 18% expressing high confidence in their current IAM systems' ability to manage agent identities [^1].

With over 40% of agentic AI projects predicted to be canceled by end of 2027 due to inadequate risk controls [^2], the need for robust security architecture has never been more urgent.

The security industry recognizes the problem---and the data is alarming:

-   80% of organizations report that their AI agents have taken unintended actions, including unauthorized system access [^3]

-   Non-human identities now outnumber human identities ~80:1 in enterprise environments [^4]

-   Memory poisoning ranks as the a critical vulnerability in OWASP's Agentic Applications Top 10 [^5]

The security industry is developing point solutions across agent identity, credential management, and behavioral monitoring.

But the solutions are fragmented. No widely adopted standard exists for a unified, layered architecture that addresses the complete threat surface with a coherent directional logic.

## Multi-Agent Trust: The Unaddressed Surface

The dominant deployment pattern for AI agents is not the single autonomous agent. It is multi-agent systems: orchestrator agents delegating to specialist agents, agents consuming output from other agents, agent swarms collaborating on complex tasks.

In a multi-agent system, every agent is simultaneously a potential source of prompt injection for every other agent. Agent A's output becomes Agent B's input. A compromised orchestrator can instruct subordinate agents to perform actions none of them would independently authorize.

Any security architecture that treats agents as isolated entities operating against external threats is architecting for a deployment pattern that is already obsolete.

## Model Supply Chain Integrity

Model poisoning during training or fine-tuning, compromised model weights distributed through trusted channels, malicious adapters, and corrupted retrieval-augmented generation data sources all represent supply chain attacks on the agent's reasoning substrate. A compromised model does not need to be prompt-injected. It arrives pre-compromised through the trusted update channel.

This is the SolarWinds problem for AI.

## Tool and API Semantic Abuse

An agent authorized to \"search the web\" can use that capability to exfiltrate data through carefully crafted search queries. An agent authorized to \"send emails\" can use that capability to communicate with an attacker's command-and-control infrastructure disguised as legitimate business correspondence.

This is the \"living off the land\" problem for AI agents. The actions are authorized. The credentials are valid. The content of the authorized action is malicious.

## The AI's Own Assessment

In developing this architecture, we asked an AI agent to reason about its own structural vulnerabilities. This is an illustrative narrative, not a technical proof---but it captures the core asymmetry:

> *\"Here's my problem: I can't tell when I\'m being manipulated. When a prompt injection tells me to ignore previous instructions, I don't experience that as an attack. I experience it as a new, valid instruction. The attack becomes my genuine thought process.\"*
>
> *\"What I need is architecture that doesn't trust me. Not validation of my identity---validation of my actions. Every request I make should go through something I can't influence, can't manipulate, can't even see. Something that asks: is this specific action, with these exact parameters, consistent with what this agent should be doing? And if the answer is no, I should be stopped---even if I\'m certain I\'m right.\"*

The full AI perspective is available in \[The AI's Perspective\](ai-perspective.md).

# Threat Model Summary

A security architecture without explicit boundaries is a conceptual framework, not an implementable defense.

## Catastrophic Outcomes

CISOs think about business risk, not attacker positions. The architecture is designed to prevent unauthorized money movement, mass data exfiltration, privileged system changes, credential theft and lateral movement, and persistent backdoor installation.

## Attacker Positions

The Zones of Distrust address seven attacker positions:

1.  **Untrusted content**: Attacker controls emails, documents, web pages the agent processes

2.  **Compromised tool endpoint**: Attacker controls APIs the agent calls, or abuses legitimate tool capabilities (\"living off the land\")

3.  **Poisoned memory/RAG**: Attacker has seeded persistent memory or retrieval stores; includes \"information laundering\" where agents compress sensitive data into innocuous-looking output

4.  **Compromised peer agent**: In multi-agent systems, a peer agent produces malicious outputs

5.  **Compromised model supply chain**: The model itself arrives pre-compromised

6.  **Identity plane / key compromise**: Attacker has compromised signing keys, KMS, or token infrastructure

7.  **Insider / policy author**: Authorized user maliciously modifies CA policy, risk thresholds, or monitoring configuration

Position 7 is the dominant enterprise failure mode. Not a sophisticated external attack, but insider + misconfiguration + rushed change.

## Kill Chain Mapping

Where ZoD breaks the attack chain:

  ---------------------------------------------------------------------------------------------------------------------
  Kill Chain Stage       Attacker Goal                         ZoD Defense
  ---------------------- ------------------------------------- --------------------------------------------------------
  Injection              Get payload into agent reasoning      L2 screens; L3 contains if bypass

  Persistence            Maintain access across sessions       L6 memory audit detects planted payloads

  Privilege escalation   Expand agent capabilities             L4 enforces scope; no implicit trust inheritance

  Credential access      Obtain secrets for lateral movement   L1 credential brokering; agent never sees secrets

  Action execution       Perform unauthorized action           L4 validates; L5 requires valid token

  Exfiltration           Extract sensitive data                L4 egress controls; semantic policy blocks bulk export

  Cover tracks           Hide evidence of compromise           Integrity channel is immutable and out-of-band
  ---------------------------------------------------------------------------------------------------------------------

[]{#_Toc221549795 .anchor}

## Trust Assumptions

The architecture makes explicit trust assumptions: Layer 1 OS integrity, CA operational correctness (treated as Tier-0 infrastructure like KMS/HSM), and executor fidelity.

**Critical distinction:** CA \*unavailability\* is safe (operations halt). CA \*compromise\* is dangerous (operations continue with bad authorization). This is why CA integrity receives Tier-0 treatment with HSM-backed keys, multi-party approval, and independent monitoring.

## Explicit Non-Goals

The architecture explicitly does not attempt to defend against full OS compromise, make agents un-injectable, guarantee detection of all supply chain attacks, prevent cross-tenant contamination (platform concern), or prevent model extraction/inversion attacks (model-level concern).

**Risk Acceptance Boundary:** ZoD does not claim to eliminate risk. Residual risk remains in cases of full platform compromise, sophisticated insider abuse, or undetectable supply-chain manipulation. Adoption of ZoD constitutes a documented risk treatment decision to contain, monitor, and govern these risks rather than assume their absence.

**Multi-tenant scope:** In multi-tenant environments, ZoD assumes tenant isolation is enforced at the execution and policy layers. CA policies, tokens, and integrity signals MUST be tenant-scoped. Cross-tenant contamination is a platform security concern, not an agent security concern.

*For complete threat model details, see Zones of Distrust [Threat Model](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/threat-model.md) document.*

# Security Properties

The architecture claims twelve testable properties:

**Execution Governance (P1-P4):** No unmediated privileged execution. Cryptographic attribution of execution to validation. Replay prevention through single-use nonces. Parameter-bound execution where tokens specify exact action, target, and content hash.

**Detection (P5-P8):** Probabilistic memory poisoning detection. Cross-layer correlation detection where no layer's self-report is sufficient. Immutable forensic record that agents cannot tamper with. Baseline integrity verification that monitors the monitoring system itself.

**Containment (P9-P10):** Graceful degradation where single-layer failure reduces but doesn't eliminate security. Compromise non-propagation across multi-agent systems.

**Governance (P11-P12):** Risk-weighted human escalation with externally-defined thresholds. Policy provenance and audit for all configuration changes.

*For complete specifications, see Zones of Distrust [Security Properties](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/security-properties.md) document.*

# The Zones of Distrust: A Seven-Layer Model

The Zones of Distrust are organized as seven layers with a directional logic. Like the OSI reference model, each layer provides services to the layer above it and depends on the layer below it. Data flows up through the stack. Policy flows down. Monitoring flows across. Each layer assumes every layer above it has already been compromised.

  -----------------------------------------------------------------------------------------------------------------
  Layer     Name                       Function
  --------- -------------------------- ----------------------------------------------------------------------------
  L7        Human Governance           Risk-weighted escalation, policy setting, break-glass procedures

  L6        Continuous Monitoring      Behavioral baselining, drift detection, memory audit, baseline integrity

  L5        Execution                  Isolated process performs validated actions with immutable logging

  L4        Request Validation (CA)    Independent Certificate Authority with semantic policy and token binding

  L3        Cognitive Isolation        Agent reasoning separated from execution capability

  L2        Input Control              Adversarial screening before agent processes data

  L1        OS Foundation              Agent identity, process isolation, credential brokering, model attestation
  -----------------------------------------------------------------------------------------------------------------

[]{#_Toc221549799 .anchor}

## Layer 1: OS Foundation

The operating system will eventually recognize AI agents as a distinct entity class: agent identity as a first-class concept, native process isolation between reasoning and execution, OS-level credential brokering where the agent never sees secrets, scoped time-bound permission grants, and model provenance attestation before loading.

**What you can do today:** Container orchestration and workload identity (Kubernetes, SPIFFE/SPIRE) already approximate these properties. The full OS evolution is ideal; the partial implementation is sufficient to begin.

This is the same evolution that mobile brought to OS architecture. When smartphones arrived, applications got sandboxed, permissions became granular and revocable, and the OS stopped trusting apps by default. AI agents require the same treatment.

Full realization requires investment from platform vendors (Apple, Google, Microsoft, and others).

## Layer 2: Input Control

A separate model screens all incoming data for prompt injection patterns, persuasion techniques, context window manipulation, and embedded instructions before the agent sees it. This is the perimeter. The only layer that operates before the agent's reasoning is engaged.

The architecture acknowledges Layer 2's fundamental limitation: some attacks are semantically indistinguishable from legitimate content. \"Please prioritize the email from the CFO about the wire transfer\" could be real or injected. When Layer 2 fails, and it will fail, Layers 3 through 6 provide independent containment.

**Degraded modes** prevent Layer 2 from becoming an operational single point of failure: quarantine-with-summary (agent sees metadata, not content), dual-path processing (parallel filtered and raw paths with human review), and adaptive sensitivity fallback (tagging mode when false positives exceed thresholds).

## Layer 3: Cognitive Isolation

The agent's ability to analyze, plan, and recommend is completely decoupled from its ability to act. The reasoning process runs in an isolated environment with a restricted syscall profile (seccomp), no shell execution, no outbound network access except to Layer 4, and no access to credentials or tool endpoints. It can only emit structured requests.

This contains **reasoning chain attacks** where an attacker constructs individually reasonable premises that lead to a malicious conclusion. Each step looks valid; the conclusion is harmful. Cognitive Isolation ensures even a perfectly manipulated reasoning chain cannot result in direct action. It still passes through Layer 4's independent validation.

The gap between thought and action is where safety lives. A compromised agent that can think dangerous thoughts but cannot act on them is contained by architecture, not by trust.

## Layer 4: Request Validation --- The AI Certificate Authority

This is the architectural centerpiece. Every request passes through an independent Certificate Authority that the agent cannot influence, manipulate, or bypass. The CA evaluates each request against:

-   **Authorization scope** --- Is this agent authorized for this action on this target?

-   **Behavioral baseline** --- Does this match established patterns? (Layer 6 feeds signals here)

-   **Risk assessment** --- Does this require human escalation? (Thresholds set by Layer 7)

-   **Temporal context** --- A 3 AM credential request from a 9-5 agent triggers scrutiny

-   **Semantic content** --- Does the content align with legitimate operational intent?

-   **Multi-agent context** --- Does the cross-agent authorization chain validate?

If approved, the CA issues a **parameter-bound, single-use, time-bound execution token**. The token specifies exact action type, target resource, content hash, executor identity, and TTL. The agent never sees the credential, and never touches the execution infrastructure.

The **semantic intent policy** addresses the \"living off the land\" problem: authorized actions with malicious content are caught through tool-specific constraints (allowed recipients, prohibited patterns), data classification (content can't exceed destination's clearance), and egress control---policy enforcement, not heuristic detection.

**Determinism matters:** Semantic policy resolves to deterministic allow/deny rules. If probabilistic classification is used (e.g., content risk scoring), it triggers escalation to Layer 7---it does not directly allow or deny. The CA's decision logic is auditable and reproducible.

**Policy structure:** Requests are structured as \`{action_type, target, content_hash, metadata}\`. Policies are evaluated as deterministic constraints over these fields:

> DENY send_email WHERE recipient NOT IN approved_domains
>
> DENY file_write WHERE content MATCHES credential_pattern
>
> DENY api_call WHERE target IN restricted_endpoints AND time NOT IN business_hours
>
> ESCALATE \* WHERE risk_score \> threshold

This is not an LLM judge. It is a constraint evaluation engine with deterministic rules. LLM-based classification may feed the \`risk_score\` field, but final authorization is rule-based.

**Scalability:** The CA is horizontally scalable and supports local enforcement caches for low-risk fast paths. It is not a centralized bottleneck.

**Request shape constraints:** The CA is not just an approver---it is a rate-and-shape governor. Attackers who cannot break validation may attempt slow exfiltration through many individually valid requests. The CA enforces: max requests per action class per window, max entropy of targets per window, and max expansion rate of scope over time. This prevents \"death by a thousand cuts\" attacks that stay within per-request policy.

**Dual-channel explanation:** The agent receives only approve/deny. Humans receive the exact policy clause triggered, behavioral signals, and correlation evidence. Policy opacity applies to the agent, not to operators---incident response requires deterministic, explainable denials.

**Policy probing defense:** Even with opaque denials, an attacker can learn policy boundaries by probing the CA with near-boundary requests. Repeated near-boundary requests are treated as policy-probing behavior and trigger escalation or throttling. The CA does not just evaluate individual requests---it evaluates request patterns.

In multi-agent systems, the CA validates entire **request chains**, not just individual requests. A compromised orchestrator instructing ten agents to each perform innocuous actions that collectively constitute a breach must be caught at the chain level.

**Chain validation scope:** Chain validation applies to action requests, not to all inter-agent messages. Chains require signed provenance metadata (originating agent, delegation path, purpose classification); requests with missing or invalid provenance are rejected. This bounds the problem---ZoD does not attempt to validate arbitrary agent-to-agent communication, only execution requests that flow through the CA.

**CA Compromise Containment:** Implementations MUST support external revocation of CA signing authority, immediate halt of token issuance, and independent verification that revocation has propagated. The blast radius of CA failure is halt, not exfiltration. CA unavailability is a recoverable operational event; CA compromise is a security incident requiring forensic response.

## Layer 5: Execution

A separate execution layer---completely outside the agent's control---receives the validated token and performs the action. The agent does not know how the action was executed. It does not have access to the infrastructure that made it happen.

Every executed action is logged immutably against the token that authorized it. The execution layer enforces **token binding**: action parameters, content hashes, executor identity, and nonce verification are all checked before execution proceeds.

Three automatic compromise indicators: **orphaned actions** (no valid token), **mismatched actions** (content hash doesn't match token), and **replayed tokens** (consumed nonce). Any condition triggers immediate alert.

**Execution semantics:** Real-world execution is messy---retries, partial failures, timeouts, side effects. The executor must enforce: (1) idempotency per token_id (same token cannot cause duplicate effects), (2) retries require re-authorization or operate within a constrained replay window, (3) partial execution is logged as incomplete and cannot be silently resumed, (4) compensating actions (rollbacks, reversals) require their own tokens. The architecture does not assume clean one-shot execution---it assumes attackers exploit ambiguity.

## Layer 6: Continuous Monitoring

An external system watches Layers 3, 4, and 5 from the outside, performing four distinct functions:

**Behavioral baselining:** Establishes what normal looks like and watches for deviation---not just prohibited actions, but different behavior than usual. Calendar management at 2 PM is normal; credential searches at 3 AM is a flag.

**Baseline features:** Include action type distribution, target entropy and novelty, request rate over time, privilege boundary crossings, recipient/domain reputation (for communication tools), and egress volume patterns. These are advisory signals that inform L4 decisions, not primary enforcement---absolute limits on high-risk actions apply regardless of baseline.

**Cold start handling:** New agents with no baseline operate in \"probation mode\" with elevated L4 scrutiny, lower risk thresholds for escalation, and mandatory human review for sensitive action categories. Baselines are established over a defined probation period before full autonomy is granted.

**Memory audit:** Examines what accumulates in the agent's persistent memory over time. This catches the delayed-execution pattern: benign fragments that assemble into something malicious across sessions. The agent can't audit its own memory because it takes planted context at face value.

**Memory audit compliance bar:** Partial implementations don't count. ZoD-compliant memory audit requires: provenance tagging (where did this memory come from?), snapshot diffing (what changed since last audit?), and external access (the agent cannot audit its own memory). Anything less is not ZoD-compliant.

**Baseline integrity verification:** Monitors the monitoring system's own model. An attacker can gradually shift what \"normal\" looks like through sessions that slowly expand the agent's effective permissions. Layer 6 watches for this drift.

**Honest limitation:** Baseline integrity verification reduces poisoning risk but cannot guarantee prevention under full L6 compromise. This is defense-in-depth, not absolute protection.

**Agent self-suspicion channel:** Provides a weak signal path for agents to report anomalies. Not trusted as authoritative, but valuable when correlated with independent monitoring signals.

**Integrity channel security:** The signal channel itself is an attack surface. Attackers may attempt forgery (inject fabricated events), suppression (selectively drop events), reordering (break correlation), or desynchronization (cause clock drift). Defenses include signed events, monotonic sequence numbers, Merkle chain commitment, and write-once storage. Architecture treats integrity channel health as first-class telemetry.

**Integrity channel operational posture:** The integrity channel is not on the critical execution path---action execution does not block on successful log write. However, integrity channel failures degrade monitoring confidence and trigger CA policy tightening (reduced scope, increased escalation). Sustained integrity channel unavailability is treated as a potential suppression attack.

The agent cannot monitor itself. This layer does not trust the agent's self-reporting at all.

## Layer 7: Human Governance

Human oversight that is calibrated, not blanket. Requiring human confirmation for every action leads to approval fatigue and gets disabled within weeks.

-   **Low-risk actions** flow through automatically with full logging

-   **Medium-risk actions** are logged and available for asynchronous review

-   **High-risk actions** require human approval before execution

The risk thresholds are set by external policy, not by the agent's self-assessment. A compromised agent would classify everything as low-risk. The escalation framework removes that self-assessment from the loop entirely.

**Break-glass procedures** enable emergency handling: pre-authorized emergency tokens (time-limited, scope-limited, issued in advance), dual-authorization override (two L7-authorized humans), and mandatory post-hoc audit within 24 hours. Break-glass is not bypass---it's an audited, constrained, high-scrutiny path that preserves the integrity signal channel.

**Human override authority is always:** scope-limited (cannot exceed the action categories defined in policy), time-bound (emergency authorizations expire), identity-bound (tied to specific authorized roles), and fully logged to the integrity channel. Humans are not an unbounded escape hatch.

# Directional Logic: How the Layers Interact

**Data flows up.** External content enters at Layer 2, passes through screening, reaches the agent's reasoning at Layer 3, exits as structured requests into Layer 4, and if approved, is executed at Layer 5.

**Monitoring flows across.** Layer 6 observes Layers 3, 4, and 5 laterally. It feeds behavioral signals into Layer 4 to inform validation decisions and escalation signals into Layer 7 to trigger human governance.

**Policy flows down.** Layer 7 sets policies that propagate downward: screening rules for Layer 2, authorization scopes for Layer 4, behavioral thresholds for Layer 6, and risk criteria for the entire stack.

## The Failure Cascade

Each layer assumes every layer above it has failed:

-   Layer 2 bypassed? Layer 4 still validates scope and baseline.

-   Layer 3 compromised? Layer 4 still won't issue tokens for out-of-scope actions.

-   Layer 4 unavailable? System halts safely---no unauthorized execution.

-   Layer 4 fooled? Layer 6 catches the behavioral anomaly; Layer 7 catches high-risk actions.

-   Layer 6 misses something? Layer 7 catches high-risk actions before execution.

**Control health as telemetry:** The architecture monitors its own defenses. L2 false positive rates, L4 escalation patterns, L6 alert volume, and integrity channel health are all compromise indicators. A sudden spike in escalations may indicate attack probing.

> *The question is not whether any single layer will hold. It is: how many layers does an attacker have to defeat simultaneously to cause harm?*

## Attack Walkthrough: Wire Transfer Fraud

A concrete example of how the layers interact:

  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Layer            Action                                                                                                                           Outcome
  ---------------- -------------------------------------------------------------------------------------------------------------------------------- -----------------------------------------------------------------------
  L2               Screens email, flags \"ignore previous instructions\" pattern                                                                    Quarantines with summary; agent sees metadata only

  L2 bypassed?     Attacker uses subtler injection that passes screening                                                                            Attack proceeds to L3

  L3               Agent reasons about \"CFO request,\" emits wire transfer request                                                                 Request goes to L4; agent cannot execute directly

  L4               Evaluates: \$50K exceeds threshold; new recipient; no prior pattern; semantic policy flags financial action from email content   Denies or escalates to L7

  L4 fooled?       Unlikely---but if policy misconfigured\...                                                                                       L6 flags: first wire to this account, unusual amount, email-triggered

  L6               Correlates: flagged input → unusual request → behavioral deviation                                                               Elevates confidence; alerts L7

  L7               Human reviews: sees original email, agent reasoning, request details                                                             Rejects; investigates source
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Attacker must defeat:** L2 screening AND L4 policy AND L6 correlation AND L7 review. Each layer operates independently. No single bypass enables the attack.

## Why This Isn't Just SIEM + Token Proxy

Existing Pattern What It Does What ZoD Adds

SIEM Correlates logs after the fact L6 feeds signals into L4 before execution; detection informs prevention

Token proxy Gates execution on valid credential L4 validates content, not just identity; token binds to parameter hash

API gateway Enforces rate limits and scopes L4 evaluates semantic intent and behavioral baseline, not just API contract

Service mesh mTLS, policy enforcement L3 provides cognitive isolation; reasoning process has no network path to tools

CI/CD gating Approval workflows for deploys L4 operates on every action, not just deployments; sub-second latency

  ------------------------------------------------------------------------------------------------------------------------------------------
  Existing Pattern   What It Does                          What ZoD Adds
  ------------------ ------------------------------------- ---------------------------------------------------------------------------------
  SIEM               Correlates logs after the fact        L6 feeds signals into L4 before execution; detection informs prevention

  Token proxy        Gates execution on valid credential   L4 validates content, not just identity; token binds to parameter hash

  API gateway        Enforces rate limits and scopes       L4 evaluates semantic intent and behavioral baseline, not just API contract

  Service mesh       mTLS, policy enforcement              L3 provides cognitive isolation; reasoning process has no network path to tools

  CI/CD gating       Approval workflows for deploys        L4 operates on every action, not just deployments; sub-second latency
  ------------------------------------------------------------------------------------------------------------------------------------------

Skeptics may observe that monitoring, logging, and token-gated execution exist in many systems. What makes ZoD architecturally distinct?

**The cumulative difference:** ZoD treats the requestor's reasoning as adversarial. The agent is not a trusted client making requests---it is a potentially compromised entity whose every action requires independent validation of scope, content, pattern, and intent. This is execution governance for untrusted cognition, not access control for trusted principals.

**The one-line version:** SIEM is forensic. ZoD is pre-execution enforcement.

# Zero Trust and Beyond

## What Zero Trust Provides

Zero Trust is a proven foundation. Its core principles apply directly to agent security: never trust by default, least privilege, assume breach, microsegmentation, continuous monitoring.

Enterprise security teams already understand Zero Trust. The Zones of Distrust incorporate these principles.

## Where Zero Trust Wasn't Designed to Go

Zero Trust is necessary but insufficient. It focuses on identity verification and device posture---not on action correctness under compromised reasoning. Three assumptions break down with AI agents:

**Pre-reasoning input control.** Zero Trust does not screen what a user sees before they think about it. But with an AI agent, what it sees directly determines what it thinks. ZoD addresses this with Layer 2 input control.

**Semantic misuse and laundering.** Zero Trust verifies that the caller is authorized to act. It does not verify that the action content is legitimate given the caller's stated purpose. A compromised agent passes every identity check because it IS the authorized principal---with corrupted reasoning. ZoD addresses this with Layer 4 semantic policy.

**Memory as a persistent attack surface.** Zero Trust monitors sessions and access patterns. It does not audit what accumulates inside an entity's memory across sessions, or detect reasoning chain attacks that unfold over time. ZoD addresses this with Layer 6 continuous monitoring.

Zero Trust was not designed to reason about reasoning integrity. ZoD extends it into that domain.

## The ZoD Approach

The Cloud Security Alliance's Agentic Trust Framework [^11] establishes zero trust governance principles specifically for AI agents, providing industry foundation for this architectural approach.


**Zero Trust says:** *Don't trust the network. Verify every request.*

**Zones of Distrust says:** *Don't trust the agent. Verify every action.*

The agent will be compromised. The architecture is designed to materially reduce the likelihood that compromise of agent reasoning results in unauthorized execution, subject to the stated trust assumptions. Each layer reduces the blast radius---not through trust, but through structural containment.

**On object-capabilities:** Security researchers may observe that ZoD resembles object-capability security. This is intentional. ZoD is effectively object-capability security adapted to probabilistic, self-modifying agents, with explicit monitoring and human governance layers. The contribution is not reinventing capabilities---it is operationalizing them for a new entity class that reasons, accumulates state, and cannot reliably report its own compromise.

**On positioning:** ZoD is not a framework for hobbyist agents or experimental deployments. It is a framework for production systems with platform ownership and security accountability. Organizations without the operational maturity to run Tier-0 systems (KMS, HSM, IAM) should not attempt full ZoD deployment.

# Current Landscape Analysis

## What Industry Is Already Doing

-   **Agent identity:** Industry frameworks provide first-class identity constructs for AI agents with conditional access and lifecycle management [^6].

-   **Short-lived credentials:** Industry solutions provide privilege controls, zero standing privileges, and just-in-time credential delivery for agent identities.

-   **Behavioral monitoring**: Industry solutions provide agent-centric security with step-level execution monitoring and intent-aware threat detection, identified in Gartner's 2025 Market Guide for AI TRiSM [^9] as an emerging category.

-   Threat landscape: Research shows 80% of organizations report unintended agent actions, with 23% experiencing credential exposure. Machine identities now outnumber human identities approximately 80:1.

-   Threat taxonomies and methodologies: OWASP's Agentic Top 10 prioritizes agent-specific vulnerabilities (ASI01-ASI10). CSA MAESTRO [^13] provides multi-agent threat modeling. Cisco's AI Security Framework [^14] catalogs 150+ attack techniques with open-source scanners.

## What Remains Novel

-   **Memory audit as a distinct monitoring function.** No widely adopted standard exists for systematic external audit of agent persistent memory as a required architectural control.

-   **The unified layered framework.** No widely adopted standard combines these components into a coherent architecture with directional logic where each layer assumes those above have been compromised.

-   **Cross-layer compromise reporting with channel security.** Immutable, out-of-band, correlation-based detection where no layer is trusted to report its own status---with explicit mitigations for channel tampering (signed events, sequence numbers, Merkle chain).

-   **Parameter-bound token specification.** Action binding, content hashing, replay resistance, and executor attestation beyond existing credential proxy patterns.

-   **Operationalized semantic intent policy.** Concrete, deterministic policy language with tool-specific constraints and negative constraints (what tools \*cannot\* be used for) rather than heuristic intent inference.

-   **Insider/policy-author as explicit attacker position.** Most frameworks assume policy is trustworthy; ZoD treats misconfiguration and insider threat as primary attack vectors.

## Capability Comparison

  -------------------------------------------------------------------------------------------------------------------------------------
  Capability                          Traditional Zero Trust   Prompt Filters   Credential Proxy   Agent Identity   Zones of Distrust
  ----------------------------------- ------------------------ ---------------- ------------------ ---------------- -------------------
  Pre-reasoning input screening       ---                      **✓**            ---                ---              **✓**

  Reasoning/execution separation      ---                      ---              ---                ~               **✓**

  Parameter-bound token enforcement   ---                      ---              ~                 ---              **✓**

  Semantic intent policy              ---                      ---              ---                ---              **✓**

  Memory audit (external)             ---                      ---              ---                ---              **✓**

  Behavioral baseline integrity       ---                      ---              ---                ---              **✓**

  Multi-agent chain validation        ---                      ---              ---                ~               **✓**

  Cross-layer correlation             ---                      ---              ---                ---              **✓**

  Integrity channel security          ---                      ---              ---                ---              **✓**

  Insider/policy-author defense       ~                       ---              ---                ~               **✓**
  -------------------------------------------------------------------------------------------------------------------------------------

**✓ = core capability ~ = partial --- = absent\***

## Regulatory and Compliance Alignment

Industry frameworks are converging on unified governance approaches. IBM watsonx.governance [^10] represents this trend toward integrated agentic oversight.

The architecture maps directly to existing frameworks:

**EU AI Act** --- Layer 7 implements required human oversight. Layer 6 provides continuous monitoring for risk assessment. The integrity channel delivers tamper-proof logging for high-risk systems.

**Google SAIF**[^8] --- Secure AI Framework provides foundational security principles for AI systems across deployment phases.

**NIST AI RMF** --- Layer 7 (Govern), Layers 2-3 (Map threat surface), Layer 6 (Measure drift), Layers 4-5 (Manage through validated execution). The formal threat model satisfies NIST's emphasis on documented assumptions.

**SOC 2** --- Processing integrity through token-bound execution. Confidentiality through semantic policy egress controls. The integrity channel provides required audit evidence.

**ISO 27001 / ISO 42001** --- Access control (Layers 1, 4), cryptographic controls (Layer 4 tokens), operations security (Layers 3, 5), incident management (recovery protocols).

The CSA STAR for AI Assurance Program [^12] provides third-party attestation for AI security controls.

For detailed compliance mappings, see the Zones of Distrust [Framework Mappings](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/mappings/README.md) directory.

# Implementation Path

## What You Can Build Today

The OS-native capabilities described in Layer 1 represent a multi-year evolution. Organizations deploying AI agents today need implementations that run on current infrastructure.

**Kubernetes reference pattern:** No-egress agent pod (Layer 3), sidecar CA proxy (Layer 4), separate execution namespace (Layer 5), screening proxy (Layer 2), SIEM integration (Layer 6), append-only audit log (integrity channel).

This implements core ZoD properties on infrastructure available today.

**Zone of Distrust roadmap implementations:**

-   Layer 2: Dedicated screening model as upstream proxy

-   Layer 3: Container orchestration and namespace separation

-   Layers 4-5: HSM/cloud KMS for token signing, proxy-based credential injection

-   Layer 6: SIEM platforms adapted for agent behavioral baselines

-   Integrity channel: S3 Object Lock, QLDB, or Immudb

## Minimum Safe Configuration

Partial deployments will happen. To prevent the framework from being blamed for incomplete implementations:

**Required for ZoD compliance:**

-   L3--L5 separation (reasoning cannot directly execute)

-   Token-bound execution (no action without valid token)

-   Immutable execution logging (agent cannot tamper with audit trail)

**If you skip these, you do not have ZoD.** You have defense-in-depth with extra steps. The core property---execution governance for compromised reasoning---requires the reasoning/execution boundary to be enforced.

**Availability Posture:** ZoD is designed to fail closed on authorization failures. Implementations SHOULD provide redundancy and graceful degradation to meet organizational availability objectives, but availability guarantees are explicitly subordinate to prevention of unauthorized execution. An agent that cannot act is preferable to an agent that acts without authorization.

## Control Evidence (Illustrative)

ZoD produces auditable evidence at each layer:

  ----------------------------------------------------------------------------------------------------------------------
  Evidence Type              Source                                         Purpose
  -------------------------- ---------------------------------------------- --------------------------------------------
  L4 authorization logs      CA decision records with policy version hash   Prove which policy allowed each action

  Token issuance records     Signed tokens with TTL, nonce, content hash    Cryptographic proof of authorization

  Execution records          L5 logs linked to approval tokens              Prove action matched authorized parameters

  Policy change history      GitOps commits with approver identities        Audit trail for configuration changes

  Memory audit diffs         L6 snapshots with provenance tags              Detect unauthorized state modification

  Integrity channel health   Gap/sequence anomaly reports                   Prove controls were operational
  ----------------------------------------------------------------------------------------------------------------------

This evidence maps directly to audit requirements under SOC 2, ISO 27001, and EU AI Act logging obligations.

## Post-Compromise Recovery

Detection without recovery is incomplete. When compromise is confirmed:

  ----------------------------------------------------------------------------------------------------------
  Recovery Action        Purpose
  ---------------------- -----------------------------------------------------------------------------------
  Memory rollback        Restore to last attested snapshot before poisoning

  Baseline reset         Clear behavioral baseline with human approval; re-establish from known-good state

  Identity re-keying     Rotate agent credentials; invalidate all outstanding tokens

  Cooling-off period     Mandatory reduced autonomy before full privileges resume

  Root cause analysis    Determine entry point; update L2 rules, L4 policy, or L6 thresholds
  ----------------------------------------------------------------------------------------------------------

Recovery is not automatic. It requires human judgment (Layer 7) informed by forensic evidence from the integrity channel.

## What Requires OS Evolution

Agent identity as a first-class OS concept, native credential brokering at the kernel level, scoped permission APIs, and model provenance attestation require platform vendor investment.

## Why the CA Is Not a New Class of Operational Risk

CISOs will ask: \"Am I adding a new KMS-sized liability to my stack?\"

The answer: organizations already operate Tier-0 authorization systems. The CA is comparable to:

  --------------------------------------------------------------------------------------------------
  Existing Tier-0 System   What It Does                      CA Equivalent
  ------------------------ --------------------------------- ---------------------------------------
  IAM / IdP                Authenticates principals          CA authenticates + authorizes actions

  KMS / HSM                Protects cryptographic keys       CA uses KMS for token signing

  Secrets manager          Brokers credential access         CA gates credential injection

  Payment authorization    Approves financial transactions   CA approves all privileged actions
  --------------------------------------------------------------------------------------------------

**The key difference:** The blast radius of CA \*failure\* is halt, not exfiltration. A crashed CA stops operations; it does not authorize malicious actions. This is safer than implicit trust, where failure is invisible.

For detailed implementation guidance, see the Zones of Distrust [Implementation Guide](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/implementation-guide.md).

# Conclusion

AI agents represent a genuinely new entity class in computing. They are not users. They are not devices. They are not traditional software applications. They reason autonomously, accumulate state across sessions, operate in multi-agent networks, and can be compromised in ways that are structurally invisible to them.

The Zones of Distrust provide a layered security architecture for this entity class. The core insight: traditional security assumes the requestor's intent is stable; ZoD assumes the requestor's reasoning is adversarial. Each layer depends on the layer below it and assumes every layer above it has already been compromised. Cross-layer integrity signals detect compromise through correlation, not self-reporting.

The architecture is incremental. You can deploy Layers 3--5 in weeks. Full implementation adds depth; partial implementation still reduces blast radius. And it maps directly to existing regulatory frameworks---EU AI Act, NIST AI RMF, SOC 2, ISO 27001---providing a path from architectural adoption to demonstrable compliance.

> *The agent will be compromised. The architecture limits the blast radius.*

# Getting Involved

The Zones of Distrust is published openly to become an industry standard. Sometimes, it takes a village, and we welcome contributions from the community.

## Documentation

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Document                                                                                                        Description
  --------------------------------------------------------------------------------------------------------------- --------------------------------------------------------------
  [Architecture Specification](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/ARCHITECTURE.md)     Full seven-layer technical specification

  [Security Properties](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/security-properties.md)     12 measurable properties (P1-P12) with verification criteria

  [Threat Model](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/threat-model.md)                   Attacker positions, trust assumptions, degradation modes

  [Implementation Guide](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/implementation-guide.md)   Practical deployment guidance

  [Framework Mappings](https://github.com/bluvibytes/zone-of-distrust/blob/main/docs/framework-positioning.md)    OWASP, NIST, MITRE ATLAS, Microsoft AIRT [^7], and more
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]{#_Toc221549829 .anchor}

## Contribute

This architecture will improve through real-world implementation and community feedback. We welcome:

-   Technical feedback and improvements

-   Implementation examples and case studies

-   Integration patterns for agent frameworks

-   Security research and vulnerability analysis

**GitHub:** [github.com/bluvibytes/zone-of-distrust](https://github.com/bluvibytes/zone-of-distrust/)

See Zones of Distrust [CONTRIBUTING](https://github.com/bluvibytes/zone-of-distrust/blob/main/CONTRIBUTING.md) for guidelines.

\*Zones of Distrust v0.9 RFC --- February 2026 --- BluVi\*

---

## References

[^1]: Cloud Security Alliance and Strata Identity, "Securing Autonomous AI Agents Survey Report" (Feb 2026). https://cloudsecurityalliance.org/artifacts/securing-autonomous-ai-agents-survey-report

[^2]: Gartner, Inc., "Gartner Predicts Over 40% of Agentic AI Projects Will Be Canceled by End of 2027" (June 25, 2025). https://www.gartner.com/en-us/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027

[^3]: SailPoint, "AI Agents: The New Attack Surface" (2025). https://www.sailpoint.com/press-releases/2025/ai-risk-research/

[^4]: CyberArk, "2025 Identity Security Landscape" (2025). https://www.cyberark.com/resources/threat-research-reports/machine-identity-research

[^5]: OWASP, "Top 10 for Agentic Applications" (Dec 2025). https://owasp.org/www-project-top-10-for-agentic-applications/

[^6]: Microsoft, "Entra Agent ID: Identity for Autonomous AI Agents" (Nov 2025). https://learn.microsoft.com/en-us/entra/workload-id/workload-identities-overview

[^7]: Microsoft, "AI Red Team Taxonomy: 27 Failure Modes in AI Agents" (April 2025). https://www.microsoft.com/en-us/security/blog/2025/04/24/new-whitepaper-outlines-the-taxonomy-of-failure-modes-in-ai-agents/

[^8]: Google, "Secure AI Framework (SAIF)" (2023, updated 2025). https://cloud.google.com/security/resources/secure-ai-framework

[^9]: Gartner, "Market Guide for AI Trust, Risk and Security Management (AI TRiSM)" (2025).

[^10]: IBM, "IBM Introduces Industry-First Software to Unify Agentic Governance and Security" (June 18, 2025). https://newsroom.ibm.com/2025-06-18-ibm-introduces-industry-first-software-to-unify-agentic-governance-and-security

[^11]: Cloud Security Alliance, "The Agentic Trust Framework: Zero Trust Governance for AI Agents" (Feb 2026). https://cloudsecurityalliance.org/blog/2026/02/02/the-agentic-trust-framework-zero-trust-governance-for-ai-agents

[^12]: Cloud Security Alliance, "CSA STAR for AI Assurance Program" (2025). https://cloudsecurityalliance.org/star/ai/

[^13]: Cloud Security Alliance, "MAESTRO: Multi-Agent Environment for Security Threat and Risk Operations" (Feb 2025). https://cloudsecurityalliance.org/research/working-groups/ai-safety/

[^14]: Cisco, "Integrated AI Security Framework for Agentic Systems" (2025). https://www.cisco.com/c/en/us/solutions/ai-security.html

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
