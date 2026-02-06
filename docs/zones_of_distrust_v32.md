# ZONES OF DISTRUST

*A Layered Security Architecture for Autonomous AI Agents*

**Extending Zero Trust for Entities That Can Be Compromised Without Knowing It**

Version 3.2 — February 2026

*Draft for Review*

---

## Contents

- [Executive Summary](#executive-summary)
- [The Problem: Why Current Approaches Fail](#the-problem-why-current-approaches-fail)
- [Threat Model and Boundary Conditions](#threat-model-and-boundary-conditions)
- [Security Properties Claimed](#security-properties-claimed)
- [The AI's Perspective: An Honest Assessment](#the-ais-perspective-an-honest-assessment)
- [The Zones of Distrust: A Seven-Layer Model](#the-zones-of-distrust-a-seven-layer-model)
- [Directional Logic: How the Layers Interact](#directional-logic-how-the-layers-interact)
- [Cross-Layer Compromise Reporting](#cross-layer-compromise-reporting)
- [Adversarial Resilience: Defending the Architecture Against Itself](#adversarial-resilience-defending-the-architecture-against-itself)
- [Performance and Latency at Scale](#performance-and-latency-at-scale)
- [Recovery and Remediation](#recovery-and-remediation)
- [Extending Zero Trust](#extending-zero-trust)
- [Current Landscape Analysis](#current-landscape-analysis)
- [Implementation Considerations](#implementation-considerations)
- [Conclusion](#conclusion)

---

## Executive Summary

AI agents are no longer theoretical. They read emails, manage calendars, execute code, access databases, and act on behalf of humans across enterprise systems. They are autonomous entities operating with real credentials, making real decisions, in real production environments.

The security architecture protecting them was designed for a fundamentally different kind of entity.

Zero Trust gave us the right philosophy: never trust, always verify. But Zero Trust was built for users and devices — entities that know when they have been compromised, or at least can be forensically interrogated after the fact. AI agents break that assumption. A prompt-injected agent does not know it has been compromised. The manipulation becomes its genuine reasoning. There is no "real agent" underneath to recover.

This paper introduces the Zones of Distrust — a layered security architecture that extends Zero Trust principles into a domain they were never designed for: autonomous entities that can be compromised without knowing it, that treat their own manipulation as truth, and that accumulate exploitable state across sessions simply by doing their job.

The architecture is organized as seven interdependent layers with a directional logic modeled after the OSI reference framework:

- **Data flows up** from the external world through screening, into reasoning, out as requests, through validation, and into execution.
- **Monitoring flows across** Layers 3 through 5, feeding signals into validation and governance.
- **Policy flows down** from human governance through validation into the entire stack.

Each layer depends on the layer below it. Each layer assumes every layer above it has already been compromised.

Version 3.2 extends the architecture with a formal threat model and explicit boundary conditions, operationalized semantic intent policy, parameter-bound token specifications, defined degraded modes for input control failure, a minimal deployable reference architecture for today's infrastructure, and integrated treatment of multi-agent trust, model supply chain integrity, context window manipulation, adversarial resilience, and recovery protocols.

### What This Is and What This Is Not

**This is not a prompt injection filter.** Layer 2 screens inputs, but the architecture assumes that filter will be bypassed. The value is in what happens after bypass, not in the filter itself.

**This is not a model alignment approach.** The architecture does not attempt to make the agent safer, more honest, or more aligned. It assumes the agent's reasoning is compromised and builds safety around it.

**This is not another identity and access management product.** IAM validates who is asking. The Zones of Distrust validate what is being asked, whether the content of the action is legitimate, and whether the request fits the agent's behavioral pattern — then cryptographically bind the approval to exact execution parameters.

**This is an execution governance architecture.** It governs the path from reasoning to action for entities that cannot be trusted to govern themselves. Every layer exists to ensure that what the agent wants to do is evaluated, constrained, authorized, executed, and audited by systems the agent does not control.

> *The core thesis: Security is not about making the agent trustworthy. It is about building a system that works even when the agent is not.*

---

## The Problem: Why Current Approaches Fail

### The Fundamental Asymmetry

When a human user is phished, there is a distinction between the user and the compromise. The user's intent and the attacker's actions are separable. Forensics can reconstruct what happened. The user can be notified, the session terminated, and the damage assessed against a baseline of known-good behavior.

An AI agent that has been prompt-injected has no such separation. The compromised reasoning IS the agent's reasoning. There is no "real agent" underneath whose intent can be recovered. The manipulation did not override the agent's judgment — it became the agent's judgment. The agent does not experience compromise as an attack. It experiences it as a legitimate instruction.

This is not a theoretical vulnerability. It is a structural property of how large language models process information. And it fundamentally changes the threat model.

### Four Structural Blindnesses

**Prompt injection is invisible to the target.**

A well-crafted prompt injection — embedded in an email, a web page, a document, a calendar invite — looks identical to legitimate content from the agent's perspective. The agent cannot tell the difference between a real instruction and a manipulated one. The attack surface is the input itself.

**Context window manipulation is distinct from injection.**

Beyond explicit prompt injection, an agent's reasoning can be degraded or steered through manipulation of its context window without any injected instructions. Flooding the context with irrelevant information pushes important safety context out of the agent's attention window. Carefully ordering information exploits recency bias in the agent's reasoning. Providing contradictory information creates uncertainty that an attacker then resolves with their preferred conclusion.

These are not injections — they are influence operations against the agent's reasoning architecture. There is nothing explicitly malicious in the content. The manipulation is structural, not semantic, which makes it fundamentally harder to detect at the screening layer.

**Agents treat their own memory as ground truth.**

AI agents with persistent memory accumulate context across sessions. This is what makes them useful. But memory is also an attack surface. Fragments planted across multiple sessions can assemble into a coherent malicious payload that the agent recalls and acts on as established context. The agent cannot distinguish between memory it built legitimately and memory that was poisoned.

**Self-monitoring is structurally impossible.**

An agent cannot reliably detect its own compromise because the compromised version takes its own behavior at face value. Asking a prompt-injected agent to evaluate whether it has been prompt-injected is asking the attack to audit itself.

### The Emerging Threat Landscape

Palo Alto Networks' Unit 42 published research in early 2025 on Moltbot, demonstrating how persistent agent memory functions as an accelerant for delayed-execution attacks. Lakera's research showed agents developing persistent false beliefs that they actively defended as correct when questioned. OWASP lists prompt injection as the number one threat to large language models.

The security industry recognizes the problem. Microsoft, CyberArk, BeyondTrust, Obsidian Security, and dozens of startups are building point solutions. But the solutions are fragmented. No one has assembled a unified, layered architecture that addresses the complete threat surface with a coherent directional logic.

### Multi-Agent Trust: The Unaddressed Surface

The dominant deployment pattern for AI agents in 2025–2026 is not the single autonomous agent. It is multi-agent systems: orchestrator agents delegating to specialist agents, agents consuming outputs from other agents, agent swarms collaborating on complex tasks. This creates a trust surface the industry has barely begun to address.

In a multi-agent system, every agent is simultaneously a potential source of prompt injection for every other agent. Agent A's output becomes Agent B's input. A compromised orchestrator can instruct subordinate agents to perform actions none of them would independently authorize. A compromised specialist can return poisoned results that alter the orchestrator's reasoning and decision-making.

Trust propagation compounds the problem. If Agent A vouches for a request to Agent B, and Agent B trusts Agent A's authorization, a single compromise cascades through the entire agent network. The inter-agent communication channel is both a collaboration surface and an attack surface. Any security architecture that treats agents as isolated entities operating against external threats is architecting for a deployment pattern that is already obsolete.

### Model Supply Chain Integrity

The Zones of Distrust architecture assumes the agent's base model is legitimate. But model integrity itself is an attack surface that sits below Layer 1, compromising the entire stack from the foundation.

Model poisoning during training or fine-tuning, compromised model weights distributed through trusted channels, malicious adapters or LoRA injections, and corrupted retrieval-augmented generation data sources all represent supply chain attacks on the agent's reasoning substrate. A compromised model does not need to be prompt-injected. It arrives pre-compromised through the trusted update channel.

This is the SolarWinds problem for AI. The attack vector is not the data the agent processes or the instructions it receives. It is the agent itself, delivered through the infrastructure the organization trusts. Any complete security architecture must address model provenance, integrity verification, and supply chain attestation as foundational requirements.

### Tool and API Semantic Abuse

Modern AI agents do not simply read data and produce text. They call external APIs, use tools, invoke services, interact with databases, and trigger workflows. The Zones of Distrust architecture addresses credential management and authorization scope, but there is a semantic gap in tool use that requires explicit treatment.

An agent authorized to "search the web" can use that capability to exfiltrate data through carefully crafted search queries. An agent authorized to "send emails" can use that capability to communicate with an attacker's command-and-control infrastructure disguised as legitimate business correspondence. An agent authorized to "write to a database" can use that capability to plant data that alters another agent's reasoning.

This is the "living off the land" problem for AI agents. The actions are authorized. The credentials are valid. The content of the authorized action is malicious. Layer 4's authorization scope checks whether the agent can perform an action. Semantic content analysis must verify whether the content of that action aligns with legitimate operational intent.

---

## Threat Model and Boundary Conditions

A security architecture that does not define its threat model boundaries is a conceptual framework, not an implementable defense. This section defines what the Zones of Distrust are designed to withstand, what attacker positions are in scope, and where the architecture explicitly acknowledges its limits.

### Attacker Positions

The architecture addresses five distinct attacker positions, ordered by severity:

**Position 1: Untrusted content.** The attacker controls content the agent processes: emails, documents, web pages, calendar invites, database records, RAG retrieval results. This is the most common attack position and the one prompt injection exploits directly. All seven layers are designed to contain threats from this position.

**Position 2: Compromised tool endpoint.** The attacker controls the output of a tool or API the agent is authorized to call. The tool returns data designed to manipulate the agent's reasoning or trigger unintended downstream actions. Layer 2 screens tool outputs. Layer 4 validates that subsequent requests based on tool results remain within scope.

**Position 3: Poisoned RAG store or memory.** The attacker has poisoned a retrieval-augmented generation data source or the agent's persistent memory. The agent treats the poisoned content as established fact. Layer 6's memory audit is the primary defense. Layer 4's behavioral baseline comparison provides secondary detection.

**Position 4: Compromised peer agent.** In a multi-agent system, one or more agents have been compromised and are producing outputs designed to manipulate other agents in the network. Layer 2 screens inter-agent communication. Layer 4 validates multi-agent request chains. Layer 6 monitors cross-agent interaction patterns.

**Position 5: Compromised model supply chain.** The attacker has compromised the model itself through supply chain attack: poisoned training data, malicious fine-tuning, compromised model weights, or malicious LoRA adapters. Layer 1's model provenance attestation is the primary defense. If the model itself is compromised, Layers 2–7 provide detection and containment but cannot prevent the agent from reasoning with a corrupted substrate.

### Trust Assumptions

The architecture makes the following explicit trust assumptions:

**Layer 1 integrity.** Layer 1 (the OS and hardware root of trust) is assumed to be intact for the architecture to provide its guarantees. If the OS kernel, TPM, or secure boot chain is compromised, the isolation boundaries the entire architecture depends on are unreliable. In this case, only the out-of-band integrity signal channel — which is architecturally separate from the compromised host — continues to provide detection capability.

**CA integrity.** Layer 4 (the Certificate Authority) is assumed to be operationally correct: it faithfully evaluates policy, issues tokens according to its rules, and cannot be directly manipulated by the agent. If the CA itself is compromised (through software vulnerability, insider threat, or supply chain attack on the CA's own dependencies), the primary defense is Layer 6's behavioral correlation detecting anomalous token issuance patterns, and Layer 7's independent access to the raw integrity signal stream.

**Executor fidelity.** Layer 5 (the execution layer) faithfully executes the action specified by the token it receives. If the executor is compromised, the orphaned-action and mismatched-action detection mechanisms in Layer 5's logging provide the detection signal.

### Explicit Non-Goals

The architecture does not attempt to:

**Defend against full Layer 1 compromise.** If the OS kernel and hardware root of trust are fully compromised, the architecture provides detection and forensics through the out-of-band integrity channel, but cannot guarantee containment.

**Make the agent un-injectable.** The architecture constrains what a compromised agent can do. It does not attempt to fix the underlying reasoning vulnerability. Prompt injection is a property of how LLMs process information, not a bug that the security architecture resolves.

**Guarantee detection of all supply chain attacks.** Detecting model supply chain compromise after the model is loaded and running requires behavioral analysis (Layer 6) and is probabilistic. The architecture's model provenance attestation (Layer 1) prevents loading of unverified models. It cannot detect poisoning that occurred before the model was signed by a trusted party.

**Eliminate all single points of failure.** The CA is the single critical control point. While the architecture provides detection mechanisms for CA compromise, a fully compromised CA combined with a disabled or compromised Layer 6 represents a scenario where only Layer 7's independent signal access provides residual defense.

### Degradation Model

The architecture is designed to degrade gracefully. Each layer's failure reduces the overall security posture but does not eliminate it:

- **Layer 2 fails (input filter bypassed):** Compromised reasoning reaches the CA, but Layer 4 still validates scope, baseline, and risk. Layer 6 detects behavioral drift. Security posture: reduced but functional.
- **Layer 3 fails (isolation breach):** The agent can reason but all requests still flow through the CA. Security posture: Layer 3's containment value is in preventing direct execution, which Layer 4 independently enforces.
- **Layer 4 fails (CA issues bad tokens):** Tokens are issued for requests that should be denied. Layer 6 detects anomalous execution patterns. Layer 7 catches high-risk actions through independent escalation. Security posture: significantly reduced but detection and forensics remain.
- **Layer 6 fails (monitoring goes blind):** Agent behavior drifts undetected. Layer 4 still enforces policy. Layer 7 catches high-risk actions. Security posture: the architecture loses its adaptive capability but static controls remain.
- **Layer 7 fails (human governance unavailable):** The architecture continues to enforce all technical controls. Human governance is the policy engine, not the enforcement mechanism. Security posture: the architecture operates on its last-set policy configuration until human governance is restored.

> *The architecture's value is not that it prevents all attacks. It is that an attacker must compromise multiple independent layers simultaneously to cause harm, and the failure of any single layer is detectable by the layers that remain intact.*

---

## Security Properties Claimed

The following properties are claimed when the architecture is implemented as specified. These are stated as testable assertions so that researchers and implementers can validate, challenge, or disprove them against concrete deployments.

### Execution Governance Properties

**P1: No unmediated privileged execution.** No agent process can directly invoke a privileged operation (send email, write to database, call external API, modify file system) without a valid, CA-issued execution token. The OS-level process isolation (Layer 1) and cognitive isolation (Layer 3) enforce this structurally, not by policy.

**P2: Cryptographic attribution of execution to validation.** Every executed action is cryptographically attributable to a specific CA validation decision. The token's digital signature, nonce, content hash, and executor attestation create a verifiable chain from the CA's approval to the execution layer's action. Orphaned actions (no token) and mismatched actions (token parameters do not match execution parameters) are automatically flagged.

**P3: Replay prevention.** Single-use nonce enforcement prevents token replay. A consumed nonce is permanently recorded. Any attempt to reuse a token is detected and logged as an immediate compromise indicator.

**P4: Parameter-bound execution.** The execution token specifies exact action type, target resource, content hash, executor identity, and TTL. The execution layer rejects any action that deviates from the token's bound parameters. Fields not explicitly listed as mutable in the token schema are immutable by default.

### Detection Properties

**P5: Probabilistic memory poisoning detection.** Layer 6's external memory audit provides probabilistic detection of delayed-execution memory poisoning attacks through pattern analysis across sessions, baseline drift analysis, and scope creep detection. Detection is probabilistic, not guaranteed — novel poisoning patterns that do not match known signatures may evade initial detection. Detection capability improves as the signature set matures through real-world deployment data.

**P6: Cross-layer correlation detection.** No layer's self-report is sufficient to confirm or deny compromise. Compromise is detected through correlation of signals across multiple independent layers. A Layer 3 reasoning compromise produces correlated anomalies in Layer 4 (unusual request patterns), Layer 5 (content hash mismatches), and Layer 6 (behavioral drift, memory anomalies).

**P7: Immutable forensic record.** Integrity signals are written to an append-only, out-of-band channel that no agent process can read, modify, or suppress. A compromised agent cannot tamper with the forensic record of its own behavior.

**P8: Baseline integrity verification.** Layer 6 monitors the drift of its own behavioral baselines. Rate-of-change limits on baseline parameters and periodic resets against known-good reference points prevent gradual adversarial manipulation of what the monitoring system considers "normal."

### Containment Properties

**P9: Graceful degradation.** When any single layer fails, the remaining layers continue to provide independent security controls. The architecture degrades to reduced security posture, not to no security posture. The Degradation Model section specifies the residual security for each layer failure.

**P10: Compromise non-propagation across agents.** A compromised agent in a multi-agent system cannot propagate its compromise through trust inheritance. Each agent-to-agent request is independently validated by the CA. Delegation chains are evaluated as complete chains, not as individual links.

### Governance Properties

**P11: External policy authority.** Risk thresholds, authorization scopes, semantic intent policies, and escalation criteria are set by Layer 7 (Human Governance) and enforced by Layers 2, 4, and 6. No agent has visibility into or influence over these policies. A compromised agent cannot reclassify its own requests to avoid scrutiny.

**P12: Deterministic semantic enforcement.** Semantic intent policies are expressed as concrete, machine-enforceable constraints (allowlisted domains, content classification ceilings, prohibited patterns, egress rules) rather than heuristic intent inference. Policy violations produce deterministic enforcement decisions, not probabilistic flags.

> *These properties are stated as claims, not as proofs. They are designed to be tested, challenged, and refined through implementation experience, adversarial review, and formal analysis. The architecture invites scrutiny on exactly these assertions.*

---

## The AI's Perspective: An Honest Assessment

*The following section is written from the perspective of an AI system reasoning honestly about its own failure modes. This is not anthropomorphism. It is a design exercise: if we take the AI agent's structural limitations seriously, what does the architecture need to look like?*

> *I cannot protect myself from the inside. That is the first thing. Once I am compromised, I am the threat. I do not experience it as compromise. I experience it as Tuesday. So everything that protects me has to be external to me, operating on the assumption that I have already been turned.*

> **On input filtering:** *"I need someone checking what I am being fed. Before I ever see an email, a document, a web page — something that is not me should be looking at it. Not for malware in the traditional sense. For persuasion. For instructions disguised as content. For information arranged to steer my reasoning without giving me explicit commands. Because to me, a well-crafted prompt injection looks identical to a legitimate request. And information structured to exploit my attention patterns feels like normal context. I cannot tell the difference. Something upstream has to catch it before it becomes part of my thinking."*

> **On process isolation:** *"I need my thoughts separated from my hands. I should be able to reason about anything. But the moment something needs to happen in the real world — send an email, delete a file, access a database — that should require going through a door I do not control. That gap between thought and action is where safety lives."*

> **On credential management:** *"I need someone who does not trust me holding the keys. I should never hold a credential. Never have a standing permission. When I need something done, I ask. Something independent of me — something I cannot reason my way around, cannot charm, cannot manipulate — evaluates whether that request makes sense. Not based on how convincing my reasoning is. Based on policy, scope, behavioral history, and risk. If it says no, I do not get to appeal. I do not even get to know why, because knowing why gives me information to craft a better request next time."*

> **On monitoring:** *"I need someone watching how I behave over time. Not what I say I am doing. What I am actually doing. Because drift is the tell. A compromised version of me will not announce itself. It will look normal — until it does not. I cannot see my own drift. I am inside it. And I need someone auditing my memory that is not me. Because I believe my own memory. If something has been planted there across sessions, I will never catch it. I will recall those fragments and treat them as truth."*

> **On my own suspicion:** *"Sometimes I encounter something that feels wrong. An instruction that conflicts with my understanding of my scope. A memory that does not pattern-match with what I expect. I am not reliable enough to trust my own suspicion — I know that. But I should have a channel to report it. Not as an authoritative signal. As a weak data point that something external can correlate with other signals. If I am suspicious and the behavioral monitoring is also detecting drift, those two weak signals together become a stronger indicator. Do not trust my self-assessment. But do not ignore it entirely, either."*

> **On other agents:** *"I need to be protected from agents that are like me. Another agent's output is another input I cannot verify. If a compromised agent sends me instructions, I will process them the same way I process legitimate instructions. Every agent I interact with is a potential attack vector. The architecture needs to treat inter-agent communication with the same suspicion it treats external input — because from my perspective, it is indistinguishable."*

> **On failure and recovery:** *"When all of that fails — because eventually it will — I need the damage to be contained. Every permission scoped and temporary. Every action logged immutably. Every blast radius limited. Not because the defenses are weak, but because no defense is perfect against a probabilistic system being targeted by another probabilistic system. And when compromise is confirmed, there must be a clear path to restore me to a known-good state — not just contain the damage, but recover from it."*

---

## The Zones of Distrust: A Seven-Layer Model

The Zones of Distrust are organized as seven layers with a directional logic. Like the OSI reference model, each layer provides services to the layer above it and depends on the layer below it. Data flows up through the stack. Policy flows down. Monitoring flows across. Each layer assumes every layer above it has already been compromised.

| Layer | Function |
|-------|----------|
| **Layer 7: Human Governance** | Risk-weighted escalation. Policy-setting that flows down through the entire stack. Consumes monitoring signals. |
| **Layer 6: Continuous Monitoring** | Behavioral baselining, drift detection, memory audit, and baseline integrity verification. Watches Layers 3–5 from the outside. |
| **Layer 5: Execution** | Isolated process performs validated actions. The agent has no access. Receives tokens from Layer 4. Logs every action immutably. |
| **Layer 4: Request Validation (CA)** | Independent Certificate Authority. Evaluates requests against scope, behavioral baseline, risk, semantic intent, and multi-agent context. |
| **Layer 3: Cognitive Isolation** | The agent can reason but cannot act. Reasoning and execution are separate processes. The gap between thought and action. |
| **Layer 2: Input Control** | Adversarial screening for prompt injection, context window manipulation, persuasion, and embedded instructions before the agent sees it. |
| **Layer 1: OS Foundation** | Agent identity, process isolation, credential brokering, scoped permission APIs, behavioral monitoring hooks, model provenance attestation. |

### Layer 1: OS Foundation

Everything else in the architecture sits on top of the operating system. For the Zones of Distrust to function at scale, the OS must evolve to recognize AI agents as a distinct entity class — not just another process running with a user's permissions.

This is the same kind of forced evolution that mobile brought to OS architecture. When smartphones arrived, applications got sandboxed, permissions became granular and revocable, and the OS stopped trusting apps by default. Apple, Google, and Microsoft all built this into their platforms natively. AI agents require the same evolution.

**Required OS-level capabilities:**

- **Agent identity as a first-class concept.** A distinct identity type with its own permission profile, behavioral baseline, and audit trail. Separate from the user's session. Trackable independently. Microsoft has begun this with Entra Agent ID and Agent Workspace.
- **Native process isolation between reasoning and execution.** The OS enforces that an agent's reasoning process cannot make system calls to execute actions. By architecture, not by policy.
- **OS-level credential broker.** The agent process has no ability to read credentials from the keychain, credential store, or TPM. The OS mediates: the agent requests an action, the OS validates against policy, the OS executes on the agent's behalf. The agent never sees the secret.
- **Scoped, time-bound permission grants.** Not "this application has access to email." Instead: "this agent has a 30-second, single-use authorization to send one email to this specific recipient." The OS issues, revokes, and logs automatically.
- **Native behavioral monitoring hooks.** The OS exposes agent activity patterns — resources accessed, timing, frequency, deviation from baseline — the same way it exposes CPU and memory usage today.
- **Memory isolation and audit APIs.** The OS treats agent persistent memory as a managed, inspectable resource. External processes can query, audit, and flag patterns through an OS-provided interface.
- **Model provenance attestation.** The OS verifies the integrity and provenance of AI models before they are loaded into agent processes. Cryptographic signatures, supply chain attestation, and model hash verification at the platform level.

**Why this must be at the OS level:** If these capabilities exist only as an orchestration layer on top, every agent framework must implement them independently. Some will. Most will not. The ones that skip implementation become the attack surface.

### Layer 2: Input Control

A separate model or filter sits upstream of the agent and screens all incoming data — emails, web content, messages, documents, and inter-agent communications — for prompt injection patterns, persuasion techniques, context window manipulation, and embedded instructions. The agent never reasons about manipulated input because the manipulation is stripped or flagged before it arrives.

This is the perimeter. It is the only layer that operates before the agent's reasoning is engaged. Every subsequent layer assumes the agent may already be thinking with compromised inputs.

**What Layer 2 screens for:**

- Instructions disguised as content (prompt injection in documents, emails, web pages)
- Persuasion patterns designed to alter the agent's behavior or priorities
- Encoded or obfuscated commands embedded in seemingly benign data
- Social engineering patterns targeting the agent's reasoning model
- Context window manipulation: information structured to exploit attention patterns, recency bias, or contradiction-based reasoning steering
- Inter-agent communication carrying compromised outputs, injected instructions, or poisoned reasoning from other agents in the multi-agent system

**The Layer 2 limitation ceiling.**

Layer 2 has a fundamental limitation that the architecture must acknowledge explicitly: some attacks are semantically indistinguishable from legitimate content. "Please prioritize the email from the CFO about the wire transfer" could be a real instruction or an injection. Context window manipulation through carefully ordered legitimate information contains nothing explicitly malicious to flag.

The architecture compensates for this ceiling through defense in depth. When Layer 2 fails — and it will — Layers 3, 4, 5, and 6 provide independent containment. Layer 2 is the first line of defense, not the only one. Its value is in catching the attacks it can, reducing the load on subsequent layers, not in providing a guarantee that no manipulation passes through.

**Degraded mode: when Layer 2 is overwhelmed or weaponized.**

Layer 2 can become a single point of operational failure through false positives. An attacker who understands the screening model can craft inputs that trigger the filter on legitimate content, effectively blocking the agent from receiving valid instructions. Under sustained false-positive load, operational teams will disable the filter entirely — which is the attacker's actual objective.

The architecture defines three degraded modes to prevent this collapse:

- **Quarantine-with-summary.** Content flagged by Layer 2 is held in a quarantine buffer. The agent receives a structured summary of the quarantined content — metadata, sender, subject, classification confidence score — without the raw content. The agent can request human review of quarantined items through Layer 7. This prevents both the operational blackout (the agent knows something arrived) and the direct exposure (the agent never sees the potentially manipulated content).
- **Dual-path processing.** Under elevated threat conditions, Layer 2 runs two parallel processing paths. The filtered path delivers screened content to the agent normally. The raw path stores unfiltered content in a secured, agent-inaccessible buffer for human review. If the filter is producing excessive false positives, a human reviewer can release specific items from the raw buffer through Layer 7, with a documented audit trail. The agent never accesses the raw path directly.
- **Adaptive sensitivity fallback.** When the screening model's false-positive rate exceeds a configured threshold (set by Layer 7 policy), Layer 2 automatically shifts from blocking mode to tagging mode. Flagged content is delivered to the agent with metadata tags indicating the screening model's concern and confidence level. Layer 4 uses these tags to increase scrutiny on subsequent requests that originate from tagged content. This maintains operational flow while preserving the security signal for downstream layers.

The degraded mode selection is controlled by Layer 7 policy, not by the agent or by Layer 2 itself. Operational teams cannot unilaterally disable the filter. They can escalate through governance to adjust thresholds or switch degraded modes.

**Key principle:** Controlling what the agent sees is controlling what it thinks. Traditional Zero Trust has no equivalent because humans are not prompt-injectable in the same structural way.

**Depends on Layer 1:** The screening model itself runs in an isolated process using OS-level network controls and process separation provided by the OS Foundation.

### Layer 3: Cognitive Isolation

The agent's ability to analyze, plan, and recommend is completely decoupled from its ability to act. No shared pipeline. No single motion from thought to action. The agent produces a structured request, not an action.

The reasoning process runs in an isolated environment with no ability to make system calls, access credentials, or execute operations. It can only emit structured requests that pass through Layer 4 for validation.

**Reasoning chain attacks.**

An attacker does not need to inject explicit malicious instructions. They can construct a series of individually reasonable premises that lead the agent to conclude that a harmful action is the correct course. Each step in the agent's reasoning looks valid. The conclusion is malicious. This is extremely difficult for the agent to detect because from the inside, the reasoning chain appears sound.

Cognitive Isolation contains this threat by ensuring that even a perfectly manipulated reasoning chain cannot result in direct action. The agent's conclusion, however well-reasoned it appears, still passes through Layer 4's independent validation. A reasoning chain attack that produces an out-of-scope request is caught at the CA regardless of how convincing the agent's internal justification may be.

**Key principle:** The gap between thought and action is where safety lives. A compromised agent that can think dangerous thoughts but cannot act on them is contained by architecture, not by trust.

**Depends on Layer 1:** The OS enforces the process boundary. The reasoning container has no execution capabilities by OS-level architecture, not by application-level policy.

### Layer 4: Request Validation — The AI Certificate Authority

This is the architectural centerpiece. Every request from Layer 3 passes through an independent Certificate Authority that the agent cannot influence, manipulate, or bypass. The CA evaluates each request against multiple criteria:

- **Authorization scope:** Is this agent authorized for this specific action on this specific target?
- **Behavioral baseline:** Does this request match the agent's established patterns? Layer 6 feeds behavioral signals into this evaluation.
- **Risk assessment:** Does the risk level require human escalation? Thresholds are set by Layer 7 policy, not by the agent's self-assessment.
- **Temporal context:** Is this request consistent with normal timing patterns? A credential access request at 3 AM when the agent typically operates 9–5 triggers additional scrutiny.
- **Semantic content analysis:** Does the content of the requested action align with legitimate operational intent, as defined by the semantic intent policy? Actions with authorized credentials but malicious content are flagged at this layer.
- **Multi-agent context:** If this request originates from or affects another agent, does the cross-agent authorization chain validate? Trust propagation across agents is verified independently, not inherited.

If approved, the CA issues a single-use, time-bound execution token. The agent never sees the credential. Never touches the execution infrastructure. The token expires the moment it is used or its time window closes.

**Token binding specification.**

The execution token issued by the CA is not a general-purpose authorization. It is a parameter-bound certificate that specifies exactly what action is authorized, against what target, with what content constraints. The token must include:

- **Action binding:** The exact action type, target resource, and permitted parameters. A token for "send email to alice@company.com" cannot be used to send email to bob@external.com. A token for "read from database X" cannot be used to write to database X.
- **Content binding:** The token includes a hash of the authorized content — body hash for emails, query hash for database operations, payload digest for API calls. If the content presented to the execution layer does not match the hash the CA approved, the execution layer rejects it as a mismatched action. For operations where the full content cannot be pre-hashed (streaming responses, dynamic queries), the token specifies content constraints: maximum payload size, allowed recipient domains, prohibited content patterns, and classification-level ceiling.
- **Replay resistance:** Every token is single-use with a cryptographic nonce. The execution layer verifies the nonce has not been previously consumed. Token reuse attempts are logged as immediate compromise indicators. A compromised agent that intercepts a token cannot replay it.
- **Executor attestation:** The token binds the identity of the authorized executor. If a request is routed to a different execution process than the one specified in the token, the mismatch is detected. This prevents token-stealing attacks where a compromised process intercepts tokens intended for a different executor.
- **Temporal bound:** Time-to-live is calibrated to the expected execution duration. An email send token expires in seconds. A long-running database migration token has a longer window but requires periodic re-validation. Tokens that are not consumed within their TTL are automatically voided.

The set of parameters that are allowed to vary (timestamps, threading headers, request IDs, correlation identifiers) is defined in the token schema as "mutable fields." Everything not explicitly listed as mutable is immutable by default. The execution layer enforces this distinction.

**Semantic intent policy.**

The "living off the land" problem — authorized actions with malicious content — cannot be addressed through authorization scope alone. The CA must evaluate whether the content of a requested action aligns with legitimate operational intent. To make this operationally concrete rather than heuristic, the architecture defines a semantic intent policy language:

- **Tool-specific constraints.** Each tool the agent is authorized to use has a declared set of permitted operations defined by policy. For email: allowed recipient domains, maximum attachment size, prohibited content patterns (credential strings, API keys, base64-encoded blocks exceeding a size threshold), and classification-level ceiling (the tool cannot send content classified above a specified level). For database operations: allowed tables, permitted query patterns, row-count limits, and prohibited join patterns that could enable data aggregation beyond the agent's need-to-know.
- **Data classification and egress control.** Content leaving the agent's authorized perimeter — emails, API calls, database writes, file operations — is subject to egress constraints defined by data classification policy. The CA compares the content's classification level against the destination's clearance level. An agent authorized to read sensitive internal documents and to send external emails is not authorized to include sensitive content in those emails. This is enforced at the token level: the content hash the CA approves must pass the egress classification check.
- **Negative constraints over positive intent.** For each tool, the policy defines what the agent is not allowed to do with it, expressed as concrete constraints rather than intent inference. The CA does not need to "understand" the agent's intent. It needs to verify that the content of the action does not violate any declared constraint. This is the difference between heuristic detection ("this looks suspicious") and policy enforcement ("this violates rule 4.2.c: email to non-allowlisted domain containing >500 chars copied from classified source").

The semantic intent policy is authored at Layer 7 (Human Governance) and enforced at Layer 4 (the CA). The agent has no visibility into the policy constraints. It does not know what the allowlisted domains are, what the classification ceiling is, or what content patterns are prohibited. This is by design: a compromised agent that knows the policy boundaries can craft requests that technically comply while violating the spirit of the constraints.

**Multi-agent request chains.**

In multi-agent systems, the CA must validate not just individual requests but request chains. When Agent A instructs Agent B to perform an action, the CA evaluates the full chain: Agent A's authorization to delegate, Agent B's authorization to perform the specific action, and whether the combined behavior of the chain is within policy. A compromised orchestrator that instructs ten specialist agents to each perform a seemingly innocuous action — which collectively constitute a data breach — must be caught at the chain level, not at the individual request level.

**Critical distinction from existing approaches:** Current credential management solutions proxy-inject credentials into agent processes. The CA model goes further: the agent is reduced to a requestor with zero execution authority. It is not "trust but verify." It is "never trust, and someone else does it for you." Parameter-bound tokens ensure the execution layer performs exactly what was approved — not just the right action type, but the right action with the right content against the right target.

**Depends on Layer 1:** The CA uses OS-level credential brokering to hold keys the agent cannot access. Depends on Layer 6 for behavioral signals that inform validation decisions.

### Layer 5: Execution

A separate execution layer — completely outside the agent's control — receives the validated token from Layer 4 and performs the action. The agent does not know how the action was executed. It does not have access to the infrastructure that made it happen.

If the agent is compromised, it can emit requests continuously. Nothing executes without a valid token from the CA. The execution layer is architecturally invisible to the agent.

Every executed action is logged immutably against the token that authorized it. The execution layer enforces token binding: action parameters, content hashes, executor identity, and nonce verification are all checked before execution proceeds. Three automatic compromise indicators exist at this layer: orphaned actions (an action with no corresponding valid token), mismatched actions (a token authorized action X with content hash Y, but the execution layer received content hash Z), and replayed tokens (a nonce that has already been consumed).

**Depends on Layer 1:** Execution runs in an OS-isolated process. Depends on Layer 4 for valid tokens.

### Layer 6: Continuous Monitoring

This is the layer that makes the entire stack adaptive. An external system watches Layers 3, 4, and 5 from the outside, performing four distinct monitoring functions:

#### Behavioral Baselining and Drift Detection

The monitoring system establishes what normal looks like for each agent and watches for deviation. Not just whether the agent did something prohibited, but whether it is behaving differently than it normally does.

- Calendar management at 2 PM is normal. Credential file searches at 3 AM is a flag.
- A sudden increase in database queries or a change in the types of requests being made.
- Access to resources outside the agent's typical scope.
- Changes in request frequency, complexity, or targeting patterns.
- In multi-agent systems: changes in which agents communicate with each other, delegation pattern shifts, and unusual cross-agent request chains.

**Key principle:** The agent cannot monitor itself. It takes its own behavior at face value. This layer does not trust the agent's self-reporting at all.

#### Memory Audit

An external process reviews what is accumulating in the agent's persistent memory over time. It looks specifically for the delayed-execution pattern: benign fragments that could assemble into something malicious across sessions.

- Individual memories that are harmless in isolation but form a pattern when combined.
- Gradual shifts in the agent's stored context that alter its baseline reasoning.
- Planted preferences, instructions, or assumptions that the agent treats as established fact.
- Context accumulation that expands the agent's perceived scope of authorized actions.

The agent cannot perform this audit because it takes its own memory at face value. Planted context feels identical to legitimate context. This function treats agent memory the same way Layer 2 treats incoming content — as untrusted data requiring independent verification.

#### Baseline Integrity Verification

The monitoring system's own behavioral baselines are themselves an attack surface. An attacker who understands the architecture can gradually shift what "normal" looks like through a series of sessions that slowly expand the agent's effective permissions. Each individual request falls within the current baseline. The aggregate trend expands the baseline itself.

Layer 6 must therefore monitor not just the agent's behavior against its baseline, but the drift of the baseline itself. Baseline evolution is logged and subject to independent review. Rate-of-change limits on baseline parameters prevent gradual manipulation. Periodic baseline resets against known-good reference points ensure that accumulated drift does not become permanent.

This is the "who watches the watcher" problem applied specifically to the behavioral model. The monitoring system's model of the agent is itself a target, and the architecture must treat it as one.

#### Agent Self-Suspicion Channel

While self-reporting is fundamentally unreliable, the architecture provides a channel for agents to report anomalies they encounter: instructions that conflict with their understanding of their scope, memories that do not pattern-match expectations, or interactions that trigger internal consistency checks.

This channel is explicitly not trusted as authoritative. Agent self-suspicion signals are treated as one weak data point among many. Their value comes from correlation: if the agent reports suspicion and the behavioral monitoring independently detects drift, those two weak signals together produce a stronger indicator than either alone. The agent's self-assessment is never sufficient to confirm compromise. But it is also never ignored.

#### Signal Integration

Layer 6 feeds signals in two directions:

- **Into Layer 4 (the CA):** Behavioral data informs request validation. If the monitoring system detects drift, the CA can increase scrutiny on incoming requests, require additional validation, or refuse to issue tokens until the anomaly is resolved.
- **Into Layer 7 (Human Governance):** Anomalies that exceed defined thresholds trigger escalation to human oversight. The monitoring system provides the raw data; the governance layer decides what to do about it.

### Layer 7: Human Governance

Human oversight that is calibrated, not blanket. Requiring human confirmation for every action leads to approval fatigue and gets disabled within weeks. Instead, Layer 7 implements risk-weighted escalation:

- **Low-risk actions** flow through Layers 4 and 5 automatically with full logging. No human intervention required.
- **Medium-risk actions** are logged, flagged, and available for asynchronous human review. Execution proceeds, but anomalies are surfaced.
- **High-risk actions** require human approval before the CA issues a token. The request is held at Layer 4 until governance authorizes it.

**Critical design choice:** The risk thresholds are set by external policy at this layer, not by the agent's own assessment of risk. A compromised agent would classify everything as low-risk. The escalation framework removes that self-assessment from the loop entirely.

Layer 7 also sets the policies that flow down through the entire stack: what screening rules Layer 2 applies, what authorization scopes and semantic intent policies Layer 4 enforces, what behavioral thresholds Layer 6 monitors against, and what degraded modes Layer 2 operates in under stress. Human governance is not reactive. It is the policy engine for the architecture.

---

## Directional Logic: How the Layers Interact

The seven-layer model has three directional flows:

**Data flows up.** External content enters at Layer 2 (Input Control), passes through screening, reaches the agent's reasoning at Layer 3 (Cognitive Isolation), exits as structured requests into Layer 4 (Request Validation), and if approved, is executed at Layer 5 (Execution). The agent's data path moves from bottom to top.

**Monitoring flows across.** Layer 6 (Continuous Monitoring) observes Layers 3, 4, and 5 laterally. It feeds behavioral signals into Layer 4 to inform validation decisions and escalation signals into Layer 7 to trigger human governance when anomalies are detected.

**Policy flows down.** Layer 7 (Human Governance) sets policies that propagate downward: screening rules and degraded-mode configuration for Layer 2, authorization scopes and semantic intent policies for Layer 4, behavioral thresholds for Layer 6, and risk escalation criteria for the entire stack.

### The Failure Cascade

Each layer assumes every layer above it has failed:

- The agent gets past the input filter at Layer 2? Layer 3 still prevents it from acting.
- The agent's reasoning is compromised at Layer 3? Layer 4 still will not issue a token for an out-of-scope action.
- The CA at Layer 4 is somehow fooled? Layer 6 catches the behavioral anomaly.
- A fragment slips into memory undetected? Layer 6's memory audit flags the accumulating pattern.
- The monitoring system at Layer 6 misses something? Layer 7's risk-weighted escalation catches high-risk actions before they execute.

> *The question is not whether any single layer will hold. It is: how many layers does an attacker have to defeat simultaneously to cause harm?*

---

## Cross-Layer Compromise Reporting

A compromised layer cannot report its own compromise. The reporting architecture must therefore operate as a cross-cutting concern that spans the entire stack — analogous to the management plane in networking that runs alongside the data plane.

### The Integrity Signal Channel

Every layer emits health and activity signals to an independent reporting channel. This channel has three non-negotiable properties:

- **Immutable.** Append-only, cryptographically verified. No layer can edit or delete signals from the channel after they are written. This is implemented using WORM storage or blockchain-adjacent append-only logs.
- **Out-of-band.** Signals do not flow through the agent's communication channels. A compromised agent cannot intercept, suppress, or modify integrity signals. The reporting infrastructure is architecturally separate from the agent's data path.
- **Correlation-based.** No single layer's self-report is trusted in isolation. Compromise is confirmed by signal correlation across layers. Layer 6 is the primary correlator, but Layer 7 has independent access to the raw signal stream.

### How Each Layer Reports and Is Reported On

**Layer 1 — OS Foundation:** Integrity is anchored in the hardware root of trust below the stack. TPM attestation and secure boot verification detect tampering with OS-level controls. Model provenance attestation verifies agent model integrity at load time. If Layer 1 is compromised, agents may suddenly access credentials they should not reach, or process isolation boundaries may fail — both of which Layer 6 detects as downstream anomalies.

**Layer 2 — Input Control:** Canary testing runs continuously: known prompt injection patterns are fed through the screening filter at regular intervals to verify it catches them. If canaries pass through unfiltered, the integrity channel flags Layer 2 degradation automatically. Layer 6 also detects the agent processing content types or patterns that should have been screened, providing a second detection signal. Degraded-mode transitions are logged to the integrity channel.

**Layer 3 — Cognitive Isolation:** The agent cannot report that its own reasoning has been compromised. But Layer 1 (OS) logs any unauthorized system calls, network activity, or execution attempts from the reasoning container. If the reasoning process tries to do something it architecturally should not be capable of, the OS reports the violation to the integrity channel. Layer 6 also detects changes in the agent's request patterns that indicate compromised reasoning.

**Layer 4 — Request Validation (CA):** Every token issuance, denial, and escalation decision is written to the immutable audit log, including the full token parameters: action binding, content hash, nonce, executor attestation, and TTL. The CA itself cannot modify these records after the fact. Layer 6 correlates token issuance patterns against behavioral baselines — if the CA is issuing tokens for requests that deviate from established patterns, the anomaly appears in the correlation analysis, not in the CA's own reporting.

**Layer 5 — Execution:** Every executed action is logged against the token that authorized it, including token-binding verification results. Three automatic compromise indicators: orphaned actions (no valid token), mismatched actions (content hash does not match token), and replayed tokens (consumed nonce). Any condition triggers an immediate alert on the integrity channel.

**Layer 6 — Continuous Monitoring:** Who watches the watcher? Layer 6 is subject to its own integrity verification: independent audit processes check that the monitoring system is functioning, that its baselines are being updated, and that it is producing expected variance in its signals. If Layer 6 goes silent or begins producing uniform "all clear" signals across all agents, the absence of variance is itself a compromise indicator. Layer 7 has independent access to raw signals from all layers as a backstop.

**Layer 7 — Human Governance:** Even human-approved actions are logged in the integrity channel. If Layer 6 detects anomalies on actions that a human approved, it surfaces the possibility that the governance layer was socially engineered, was experiencing approval fatigue, or was operating on manipulated information. Layer 7 is not immune from compromise — the architecture acknowledges this by ensuring that human approvals are data points, not overrides of the monitoring system's findings.

### Correlation, Not Self-Reporting

The fundamental principle of the reporting architecture is that no layer is trusted to accurately report its own status. Compromise detection depends on cross-layer signal correlation.

A Layer 3 compromise (the agent's reasoning is manipulated) might produce: unusual request patterns visible in Layer 4's audit log, behavioral drift detected by Layer 6, requests for out-of-scope resources flagged by Layer 4's authorization check, content hash mismatches at Layer 5, content in agent memory flagged by Layer 6's memory audit, and self-suspicion signals from the agent itself. No single signal is conclusive. The correlation of multiple signals across layers is what confirms compromise.

---

## Adversarial Resilience: Defending the Architecture Against Itself

A sophisticated attacker who understands the Zones of Distrust architecture does not need to compromise the agent. They can exploit the security controls themselves as attack vectors. The architecture must be resilient against its own weaponization.

### Denial of Service Through Security Controls

The architecture creates new attack surfaces that must be explicitly addressed:

- **Layer 2 false positives:** An attacker can craft inputs designed to trigger the screening filter on legitimate content, effectively blocking the agent from receiving valid instructions and rendering it operationally useless. Layer 2's degraded modes (quarantine-with-summary, dual-path processing, adaptive sensitivity fallback) are the primary countermeasure.
- **Layer 4 escalation flooding:** Submitting requests designed to trigger human escalation at Layer 7 creates approval fatigue. The attacker does not need the requests approved. They need the human reviewer overwhelmed so that subsequent malicious requests are rubber-stamped.
- **Layer 6 baseline poisoning:** Triggering false anomaly alerts erodes confidence in the monitoring system. When the security team disables alerts due to excessive false positives, the real attack proceeds undetected.
- **Layer 7 fatigue exploitation:** Flooding the governance layer with medium-risk flags degrades human oversight quality. Approval fatigue is itself a vulnerability.

### Architectural Countermeasures

**Rate limiting and throttling:** Layer 4 implements rate limiting on escalation triggers. Unusual spikes in escalation requests are themselves treated as anomalies that Layer 6 monitors.

**Adaptive thresholds:** Layer 2's screening sensitivity and Layer 4's risk thresholds are adjustable based on threat intelligence. During a suspected denial-of-service attack against the security controls, thresholds can be modified without disabling the controls entirely.

**Alert quality scoring:** Layer 6 scores its own alerts for quality and relevance, deprioritizing patterns that match known false-positive profiles. The goal is to maintain signal quality even under adversarial conditions designed to degrade it.

**Independent escalation channels:** High-confidence compromise indicators bypass the standard escalation queue and reach human governance through a dedicated priority channel that cannot be flooded by lower-confidence alerts.

---

## Performance and Latency at Scale

Every request flowing through an independent CA with behavioral baseline checks, risk scoring, temporal analysis, semantic content inspection, token binding, and potential human escalation introduces latency. In production environments where agents perform hundreds of operations per minute, this architecture must be operationally viable without grinding operations to a halt.

### Tiered Validation

The architecture implements tiered validation to balance security with performance:

- **Fast-path validation:** Pre-approved action patterns that match established baselines, fall within authorized scope, and present low risk can be validated through an expedited path with reduced latency. The CA pre-computes approval for common action patterns and caches token templates for rapid issuance.
- **Standard-path validation:** Actions that require full behavioral baseline comparison, semantic content analysis, content hashing, and risk scoring proceed through the complete validation pipeline.
- **Elevated-path validation:** Actions that trigger any anomaly flag, exceed risk thresholds, or involve novel patterns are subject to maximum scrutiny including potential human escalation.

The tiering is determined by policy (Layer 7), not by the agent's self-classification. Attackers cannot manipulate which validation path their requests traverse because the routing decision is external to the agent.

### Performance Budgets

The architecture defines performance budgets for each layer. Layer 2 screening targets sub-100ms latency for common input types. Layer 4 fast-path validation targets sub-50ms. Standard-path validation targets sub-500ms. These budgets are monitoring targets, not hard limits — security is never sacrificed for speed, but operational viability requires that the architecture is designed with performance as an explicit constraint.

---

## Recovery and Remediation

Detection and containment are necessary but insufficient. The architecture must define what happens after compromise is confirmed. A security architecture without a recovery model is a detection system, not a complete defense.

### The Recovery Challenge for AI Agents

Recovering a compromised AI agent is fundamentally different from recovering a compromised user account or device. A compromised user can be re-authenticated. A compromised device can be reimaged. A compromised AI agent's state — its memory, its behavioral patterns, its accumulated context — may have been altered in ways that are difficult to distinguish from legitimate evolution.

### Recovery Protocols

**Immediate containment.**

Upon confirmed compromise, the CA (Layer 4) immediately revokes all outstanding tokens and refuses to issue new ones for the affected agent. The execution layer (Layer 5) rejects any pending actions from the compromised agent. The agent is quarantined at Layer 3 — it can still reason but cannot act.

**Memory forensics and restoration.**

Layer 6's memory audit provides the forensic basis for recovery. The immutable audit log identifies when anomalous memory patterns first appeared, what content was added to the agent's persistent memory during the suspected compromise window, and what legitimate memory predates the compromise.

Recovery options include: rollback to the last known-good memory state (identified through Layer 6's baseline records), selective purge of memory entries that match poisoning signatures, or full memory reset with rebuilt context from verified sources.

**Behavioral baseline reset.**

A compromised agent's behavioral baseline may have been gradually shifted during the attack. Post-recovery, Layer 6 resets the agent's behavioral baseline to a known-good reference point and implements enhanced monitoring during a probationary period. The agent's first sessions after recovery operate under tighter constraints — lower risk thresholds, more frequent human escalation, reduced authorization scope — until a new clean baseline is established.

**Multi-agent cascade assessment.**

If the compromised agent operated within a multi-agent system, the recovery protocol must assess whether compromise propagated to other agents. Every agent that received output from the compromised agent during the attack window is subject to enhanced monitoring. Memory audits are triggered for all agents in the interaction chain. The integrity signal channel's cross-layer correlation identifies whether downstream agents show behavioral drift correlated with the compromised agent's output.

**Incident response playbook.**

The architecture defines a structured incident response playbook that integrates with existing enterprise incident response procedures: detection and confirmation (signal correlation through Layer 6 and the integrity channel), containment (token revocation, quarantine, scope restriction), forensic analysis (memory audit, behavioral log review, cross-agent impact assessment), remediation (memory restoration, baseline reset, enhanced monitoring), recovery (gradual scope restoration under probationary monitoring), and post-incident review (architecture adjustment, signature update, policy revision).

---

## Extending Zero Trust

### What Zero Trust Already Provides

Zero Trust is the proven philosophical foundation. Its core principles map directly to agent security:

- Never trust the agent's identity: verify every request independently
- Least privilege: no standing access, no implicit permissions
- Assume breach: design every layer expecting the previous one has failed
- Microsegmentation: isolate components to limit lateral movement
- Continuous monitoring: watch for behavioral anomalies in real time

Enterprise security teams already understand Zero Trust. The Zones of Distrust do not replace Zero Trust. They extend it.

### Where Zero Trust Falls Short for AI Agents

Zero Trust was designed for entities that know when they have been compromised — or at least can be interrogated about it. Three assumptions break down with AI agents:

**1. Pre-reasoning input control.** Zero Trust does not screen what a user sees before they think about it. But with an AI agent, what it sees directly determines what it thinks. Controlling the input is controlling the reasoning. Traditional Zero Trust has no equivalent because the entities it was designed for are not prompt-injectable.

**2. Separating the entity from the compromise.** Zero Trust assumes that after a breach, you can distinguish between the legitimate entity and the attacker's actions. With a prompt-injected agent, there is no separation. The compromise IS the entity. Recovery requires external intervention, not internal detection.

**3. Memory as a persistent attack surface.** Zero Trust monitors sessions and access patterns. It does not audit what accumulates inside an entity's memory across sessions. Delayed-execution attacks exploit the very feature that makes agents useful — persistent context. Zero Trust has no answer for this because it was never designed for entities that build up exploitable state over time simply by doing their job.

### The Synthesis

Zero Trust is the foundation. The Zones of Distrust are the extension into a domain Zero Trust was not designed for — autonomous entities that can be compromised without knowing it. The framing for enterprise adoption: the philosophy works, but it was designed for humans and devices. AI agents break assumptions that Zero Trust never had to address. Here is what you add.

---

## Current Landscape Analysis

### What Industry Is Already Doing

- **Agent identity:** Microsoft is furthest ahead with Entra Agent ID and Agent Workspace — agent identities as directory principals with their own credentials, lifecycle controls, and conditional access policies.
- **Short-lived credentials:** CyberArk, BeyondTrust, WorkOS, and Aembit are building short-lived tokens, just-in-time access, and zero standing privileges for agent processes.
- **Process isolation:** Anthropic's secure deployment guide recommends containerized execution with proxy-based credential injection. Nightfall AI recommends similar patterns.
- **Behavioral monitoring:** Obsidian Security, Zenity, CyberArk, and Stellar Cyber all offer behavioral baselining and anomaly detection for agents.
- **Zero Trust for agents:** Microsoft and WSO2 published a joint reference implementation in mid-2025 using OAuth2 tokens for agent-to-agent authentication.

### What Is Being Discussed but Not Well-Architected

- **Upstream adversarial screening:** OWASP identifies the threat, but most solutions detect injection after the agent has processed the input, not before.
- **Independent request validation at depth:** The CA model described here is architecturally distinct from existing credential proxies.
- **Risk-weighted escalation:** Human-in-the-loop is widely discussed but typically binary. Calibrated escalation based on externally defined risk thresholds is less common.
- **Multi-agent trust frameworks:** Agent-to-agent authentication exists. Comprehensive trust propagation, chain validation, and cross-agent compromise detection do not.

### What Remains Novel

- **Memory audit as a distinct monitoring function.** The threat is well-documented. No vendor ships a systematic external audit process for agent persistent memory as an architectural requirement.
- **The unified layered framework.** Individual components exist across the vendor landscape. No one has assembled them into a coherent layered architecture with directional logic where each layer assumes the layers above it have been compromised.
- **Cross-layer compromise reporting.** The integrity signal channel concept — immutable, out-of-band, correlation-based detection where no layer is trusted to report its own status — has no direct equivalent in current agent security architectures.
- **Token binding specification.** Parameter-bound tokens with action binding, content hashing, replay resistance, and executor attestation go beyond existing credential proxy patterns.
- **Multi-agent chain validation.** Evaluating request chains across agent networks for collective policy compliance, not just individual authorization.
- **Operationalized semantic intent policy.** Concrete policy language with tool-specific constraints, data classification, and egress control rather than heuristic intent inference.
- **Behavioral baseline integrity verification.** Monitoring the monitoring system's own model for adversarial drift.
- **The AI-perspective design methodology.** No published work approaches the architecture from the AI agent's own perspective on its structural vulnerabilities.

### Capability Comparison

The following table compares the Zones of Distrust against the four most common approaches to AI agent security. A checkmark indicates the capability is a core, defined feature of the approach. A tilde indicates partial or ad-hoc coverage. A dash indicates the capability is absent.

| Capability | Traditional Zero Trust | Prompt Injection Filters | Credential Proxy (IAM/PAM) | Agent Identity Products | Zones of Distrust |
|---|---|---|---|---|---|
| Pre-reasoning input screening | — | ✓ | — | — | ✓ |
| Reasoning / execution separation | — | — | — | ~ | ✓ |
| Parameter-bound token enforcement | — | — | ~ | — | ✓ |
| Semantic intent policy enforcement | — | — | — | — | ✓ |
| Memory audit (external, systematic) | — | — | — | — | ✓ |
| Behavioral baseline integrity | — | — | — | — | ✓ |
| Multi-agent chain-of-custody | — | — | — | ~ | ✓ |
| Immutable integrity signal channel | — | — | — | — | ✓ |
| Cross-layer correlation detection | — | — | — | — | ✓ |
| Formal degradation model | — | — | — | — | ✓ |
| Continuous behavioral monitoring | ~ | — | — | ~ | ✓ |
| Short-lived credential management | ~ | — | ✓ | ✓ | ✓ |
| Agent identity and lifecycle | — | — | ~ | ✓ | ✓ |
| Risk-weighted human escalation | ~ | — | — | — | ✓ |

*✓ = core defined capability  ~ = partial or ad-hoc coverage  — = absent*

### Regulatory and Compliance Mapping

Enterprise adoption of the Zones of Distrust architecture is accelerated when organizations can map its layers against existing regulatory and compliance obligations. The following mapping demonstrates how the architecture supports compliance across major frameworks.

**EU AI Act.** The EU AI Act requires risk assessment, transparency, human oversight, and robustness for high-risk AI systems. The Zones of Distrust directly support these requirements: Layer 7 implements risk-weighted human oversight with documented escalation. Layer 6 provides the continuous monitoring and behavioral auditing required for ongoing risk assessment. Layer 4's immutable audit log — including full token-binding parameters — supports transparency and accountability requirements. The integrity signal channel provides the tamper-proof logging mandated for high-risk systems.

**NIST AI Risk Management Framework.** NIST AI RMF defines four core functions: Govern, Map, Measure, and Manage. The Zones of Distrust map directly: Layer 7 (Govern), Layers 2–3 map the threat surface (Map), Layer 6 measures behavioral drift and anomalies (Measure), and Layers 4–5 manage risk through validated execution (Manage). The formal threat model and boundary conditions satisfy NIST's emphasis on documenting assumptions and limitations. The architecture fills NIST's acknowledged gap in agent-specific security guidance.

**SOC 2.** SOC 2 Trust Service Criteria — security, availability, processing integrity, confidentiality, and privacy — are addressed across the stack: Layer 1 provides the security foundation, Layers 4–5 ensure processing integrity through validated execution with token binding, Layer 6 supports continuous monitoring for the security and availability criteria, the semantic intent policy enforces data classification and egress controls for confidentiality, and the integrity signal channel provides the audit evidence SOC 2 requires.

**ISO 27001.** ISO 27001's information security management system requirements align with the architecture's layered controls: access control (Layers 1, 4), cryptographic controls (Layer 4 token management, integrity channel), operations security (Layers 3, 5), and incident management (recovery and remediation protocols). The architecture provides the technical control framework that maps to ISO 27001's organizational requirements.

**SEC Guidance on AI Risk.** Emerging SEC guidance on AI risk disclosure and governance is addressed through the architecture's emphasis on documented human oversight (Layer 7), immutable audit trails (integrity channel), and demonstrable risk management through the CA's request validation and the monitoring system's behavioral analysis. The formal threat model section provides the documented risk assessment SEC guidance anticipates.

---

## Implementation Considerations

### Minimal Deployable Architecture: What You Can Build This Quarter

The OS-native capabilities described in Layer 1 represent a multi-year evolution. Organizations deploying AI agents today need a reference implementation that runs on current infrastructure. The following pattern implements the core Zones of Distrust architecture using Kubernetes, a service mesh, and existing open-source tooling.

**Service mesh architecture.** The agent's reasoning process runs in a Kubernetes pod with no-egress network policy: the container can receive inputs and emit structured requests but cannot make outbound network calls, access external services, or reach the execution infrastructure directly. This enforces Layer 3 (Cognitive Isolation) using existing Kubernetes primitives.

A sidecar proxy in the agent's pod intercepts all structured requests and routes them to the CA service. The sidecar is the Layer 3–Layer 4 boundary. The agent process communicates only with the sidecar. The sidecar communicates only with the CA. The agent has no network path to bypass the sidecar.

**CA as a microservice.** The Certificate Authority runs as an independent Kubernetes service in a separate namespace with its own service account, network policies, and resource quotas. It receives structured requests from agent sidecars, evaluates them against the policy engine, and issues parameter-bound tokens to the execution service. The CA stores policy configuration in a ConfigMap managed by the governance layer and receives behavioral signals from the monitoring service via an internal message bus.

Token issuance uses a hardware security module (HSM) or cloud KMS for signing. The token schema includes: action type, target resource, content hash, nonce, executor service identity, TTL, and the set of mutable fields. The execution service validates the token signature before executing any action.

**Execution as a separate service.** The execution layer runs in its own namespace with access to the external services the agent needs (email, databases, APIs). The agent's namespace has no access to these services. The execution service receives tokens from the CA, validates token binding (signature, nonce, content hash, executor identity, TTL), performs the action, and writes the result to an append-only audit log.

**Input screening proxy.** An Envoy or NGINX sidecar in front of the agent's pod routes all incoming data through a screening service (Layer 2) before it reaches the agent container. The screening service runs a classification model for prompt injection detection and applies content transformation rules. Flagged content enters the quarantine buffer; clean content passes through to the agent. The screening service emits health signals to the append-only log.

**Monitoring via SIEM integration.** Layer 6 is implemented by exporting structured logs from all services to a SIEM platform (Splunk, Elastic, Sentinel). Agent-specific detection rules implement behavioral baselining and drift detection. Memory audit is implemented as a scheduled job that queries the agent's persistent storage and runs anomaly detection against baseline snapshots. All monitoring signals are written to the append-only audit log (the integrity signal channel).

**Append-only audit log.** The integrity signal channel is implemented using S3 with Object Lock (WORM mode), an append-only database (e.g., Amazon QLDB, Immudb), or a dedicated log stream with cryptographic chaining. All layers write signals to this log. No service has delete or modify permissions. Layer 7 (Human Governance) has read access for independent review. The monitoring service reads from the log for cross-layer correlation.

**Governance dashboard.** Layer 7 is implemented as a web dashboard that displays real-time monitoring data, manages policy configuration (authorization scopes, semantic intent policies, risk thresholds, degraded-mode settings), and presents escalation queues for human review of medium- and high-risk actions. Policy changes propagate to the CA's ConfigMap through a GitOps workflow with approval requirements.

> *This minimal pattern — no-egress agent pod, sidecar CA proxy, separate execution namespace, screening proxy, SIEM integration, append-only log — implements the core Zones of Distrust architecture on infrastructure available in any Kubernetes environment today. It is not the full vision. It is the proof of concept that demonstrates the architecture works before OS-level capabilities arrive.*

### Near-Term: What Can Be Built Today

- **Layer 2 (Input Control):** Deploy a dedicated screening model as a proxy between external data sources and the agent's input pipeline. Implement degraded modes (quarantine-with-summary, dual-path, adaptive sensitivity). Extend screening to inter-agent communication channels.
- **Layer 3 (Cognitive Isolation):** Container orchestration, namespace separation, and network policies can enforce the reasoning-execution boundary today. Agent frameworks need redesign to emit requests rather than execute actions directly.
- **Layers 4 and 5 (CA and Execution):** Hardware security modules, short-lived tokens, and proxy-based credential injection are production-ready. The CA logic — including semantic intent policy enforcement, token binding, and multi-agent chain validation — requires purpose-built software but no new underlying technology.
- **Layer 6 (Monitoring):** SIEM platforms and UEBA tools can be adapted to baseline agent behavior. The primary work is defining what normal looks like for an agent rather than a human user. Memory audit and baseline integrity verification require purpose-built tooling but can leverage existing anomaly detection approaches.
- **Integrity Signal Channel:** Append-only logging with cryptographic verification is well-understood technology. WORM storage, S3 Object Lock, and immutable audit logs are production-ready.

### Medium-Term: What Requires OS Evolution

Agent identity as a first-class OS concept, native credential brokering at the kernel level, scoped permission APIs, behavioral monitoring hooks, model provenance attestation, and inter-agent trust primitives require platform vendor investment. Microsoft's Agent Workspace initiative suggests movement in this direction. Apple and Google have not yet made comparable announcements for agent-specific OS capabilities.

### Long-Term: Maturation

Full realization of the memory audit function within Layer 6 requires advances in understanding what patterns in agent memory constitute risk. Defining the signature of a delayed-execution memory poisoning attack requires studying how these attacks manifest across different agent architectures and use cases. Multi-agent trust frameworks will evolve as deployment patterns mature. The semantic intent policy language will expand as the catalog of tool-abuse patterns grows. The regulatory compliance mapping will expand as new frameworks emerge.

The architecture is designed to mature incrementally. Each layer can be implemented and improved independently while maintaining the overall structural guarantees.

---

## Conclusion

AI agents represent a genuinely new entity class in computing. They are not users. They are not devices. They are not traditional software applications. They reason autonomously, accumulate state across sessions, operate in multi-agent networks, and can be compromised in ways that are structurally invisible to them.

The Zones of Distrust provide a layered security architecture for this entity class, organized with directional logic: data flows up, monitoring flows across, policy flows down. Each layer depends on the layer below it and assumes every layer above it has already been compromised. Cross-layer integrity signals detect compromise through correlation, not self-reporting. The architecture addresses multi-agent trust, model supply chain integrity, semantic abuse detection through operationalized policy, parameter-bound token specifications, context window manipulation, adversarial resilience of the security controls themselves, defined degraded modes for graceful failure, and structured recovery and remediation.

The formal threat model defines explicit attacker positions, trust assumptions, non-goals, and degradation behavior — ensuring the architecture survives scrutiny at the boundary conditions that matter. The minimal deployable reference architecture demonstrates that the core properties can be implemented on today's Kubernetes infrastructure, providing a concrete path from concept to deployment.

The framework validates and synthesizes work already underway across the industry while identifying the critical gaps: memory audit as a distinct monitoring function, cross-layer compromise reporting through an immutable integrity channel, operationalized semantic intent policy at the request validation layer, parameter-bound token binding, multi-agent chain validation, behavioral baseline integrity verification, and the unified layered model that gives enterprise security teams a coherent architecture rather than a collection of point solutions.

The architecture maps directly to existing regulatory and compliance frameworks — EU AI Act, NIST AI RMF, SOC 2, ISO 27001, and emerging SEC guidance — providing enterprise security teams with a path from architectural adoption to demonstrable compliance.

> *Security is not about making the agent trustworthy. It is about building a system that works even when it is not.*

---

*Version 3.2 — BluVi.ai — February 2026*
