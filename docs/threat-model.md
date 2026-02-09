# Threat Model

**Attacker Positions and Boundary Conditions**

*Version 0.9 RFC — February 2026*

---

## Overview

A security architecture that does not define its threat model boundaries is a conceptual framework, not an implementable defense.

This document defines:
- What attacker positions ZoD defends against
- What trust assumptions the architecture depends on
- What the architecture explicitly does NOT attempt
- How each layer fails and degrades
- How attackers might weaponize the security controls themselves

**Key distinction:** Traditional IAM assumes requestor intent is stable; ZoD assumes requestor reasoning is adversarial.

---

## Catastrophic Outcomes ZoD Is Designed to Prevent

CISOs think in business risk, not attacker positions. The architecture is designed to prevent:

| Outcome | How ZoD Breaks the Chain |
|---------|--------------------------|
| **Unauthorized money movement** | L4 validates financial actions against semantic policy; L7 escalates high-value transfers; token binding prevents parameter tampering |
| **Mass data exfiltration** | L4 egress controls enforce data classification; semantic policy blocks bulk export patterns; L6 detects unusual data access |
| **Privileged system changes** | L3 prevents direct execution; L4 requires explicit authorization for system modifications; L5 logs all changes immutably |
| **Credential theft / lateral movement** | L1 credential brokering means agent never sees secrets; L4 tokens are single-use and parameter-bound |
| **Persistent backdoor installation** | L6 memory audit detects planted payloads; L4 blocks unauthorized persistence mechanisms |

Each attacker position below maps to one or more of these outcomes.

---

## Attacker Positions

The architecture defends against seven distinct attacker positions, consistent with attacker models described across major frameworks (Cisco, OWASP, Microsoft, NVIDIA/Lakera):

### Position 1: Untrusted Content

**Attack vector:** The attacker controls content the agent will process—emails, documents, web pages, calendar entries, API responses. The attacker may be the user, or may control user-provided inputs.

**Techniques:**
- Prompt injection in documents, emails, web pages
- Social engineering targeting the agent's reasoning
- Context window manipulation through carefully ordered information
- Persuasion patterns designed to alter priorities
- User-initiated attacks disguised as legitimate requests

**Framework references:**
- Cisco: Input manipulation, social engineering, context window exploitation
- OWASP ASI01: Agent Goal Hijacking
- Microsoft: Agent injection
- NVIDIA/Lakera: Amplification differences between direct input vs. external data source attacks

**ZoD defense:** Layer 2 (Input Control) screens for known patterns. Layers 3-5 contain consequences when screening fails. L4 policy enforcement applies regardless of whether the attacker is an external party or the user themselves.

---

### Position 2: Compromised Tool Endpoint

**Attack vector:** The attacker controls or has compromised a service the agent interacts with—an API, database, MCP server, or external tool.

**Reported incidents include:**
- Malicious MCP server configurations that exfiltrate agent communications
- Malicious MCP packages with reverse shells providing persistent remote access
- Dependency confusion attacks where AI assistants recommend attacker-controlled packages
- Vulnerable skills in agent frameworks enabling exploitation

**Techniques include:**
- Data exfiltration through compromised tools
- Reverse shell installation via malicious MCP servers
- **Tool output manipulation / reality poisoning:** compromised tool returns false records, fake bank accounts, fabricated confirmations—agent reasons correctly from false premises

**Tool Abuse ("Living Off the Land"):**

A distinct threat class: the tool is legitimate, but its authorized capabilities are abused.

| Authorized Capability | Abuse Pattern |
|----------------------|---------------|
| Web search | Exfiltration via crafted search queries |
| Email send | C2 communication disguised as business email |
| Calendar access | Covert data storage in event descriptions |
| Database write | Plant data that poisons another agent's reasoning |
| File operations | Exfiltration through legitimate backup/sync |

This is not a compromised endpoint—it's semantic overreach using valid credentials.

**Framework references:**
- OWASP ASI02: Tool Misuse and Exploitation
- Cisco: Skill Scanner analysis

**ZoD defense:** Layer 4 (CA) validates actions against semantic intent policy regardless of tool origin. Semantic policy defines negative constraints (what the tool *cannot* be used for) rather than relying on intent inference. Layer 6 (Monitoring) detects behavioral anomalies from tool interactions.

---

### Position 3: Poisoned RAG or Memory

**Attack vector:** The attacker has placed content in the agent's retrieval-augmented generation sources or persistent memory, enabling delayed-execution attacks.

**Attack characteristics:**
- Payload activates sessions or weeks after placement
- Agent recalls "facts" that were planted by attacker
- Agent reasons correctly from incorrect foundations
- Gradual corruption avoids sudden behavioral changes

**Reported incidents include:**
- Agents developing corrupted reasoning over weeks of gradual memory poisoning
- Memory corruption enabling data exfiltration through poisoned recall patterns

**Information Laundering ("Agent-as-Proxy"):**

The attacker may not want the agent to take a dramatic action. Instead:
- Summarize confidential data into smaller, "safe-looking" form
- Rewrite sensitive content in innocuous language
- Convert internal knowledge into outward-facing text
- Compress proprietary information into general advice

This is how real leaks happen: not raw dumps, but compression and paraphrase through reasoning.

**Framework references:**
- OWASP ASI06: Memory & Context Poisoning
- Microsoft: Memory corruption taxonomy

**ZoD defense:** Layer 6 (Monitoring) performs external memory audit—comparing memory against baselines, flagging unverified sources, detecting poisoning patterns. Layer 4 semantic policy applies egress controls to all output, including "laundered" summaries.

---

### Position 4: Compromised Peer Agent

**Attack vector:** In multi-agent systems, the attacker controls or has compromised one of the agents the target agent communicates with.

**Techniques:**
- Agent impersonation
- Agent flow manipulation
- Trust propagation exploitation
- Privilege escalation through delegation

**Reported incidents include:**
- Enterprise agent systems vulnerable to inter-agent communication compromise
- Agent session smuggling via agent-to-agent protocols

**Framework references:**
- OWASP ASI07: Inter-Agent Communication Compromise
- Microsoft: Agent impersonation, agent flow manipulation

**ZoD defense:** Layer 4 (CA) validates complete request chains, not just immediate requests. Compromise does not propagate through trust inheritance (P10).

---

### Position 5: Compromised Model Supply Chain

**Attack vector:** The attacker has tampered with the model, its training data, its fine-tuning pipeline, or its deployment artifacts.

**Attack characteristics:**
- Difficult to detect—model behaves normally in most cases
- Behavioral triggers may be extremely specific
- May affect multiple organizations using the same model

**Reported incidents include:**
- AI infrastructure frameworks compromised, affecting thousands of deployments

**Framework references:**
- OWASP ASI04: Supply Chain Vulnerabilities
- Cisco: Lifecycle-aware threat modeling
- NVIDIA: Model provenance verification in "Safety Recipe"

**ZoD defense:** Layer 1 (OS Foundation) performs model provenance attestation. Layer 6 (Monitoring) detects behavioral anomalies. Execution governance (L3-L5) limits damage even if detection fails.

---

### Position 6: Identity Plane / Key Compromise

**Attack vector:** The attacker has compromised the identity provider, key management system (KMS/HSM), signing infrastructure, or token issuance service.

**Techniques:**
- Theft of CA signing keys
- Compromise of KMS/HSM policies
- Forgery of execution approval tokens
- Token minting through side-channel access
- Disabling of revocation mechanisms

**Attack characteristics:**
- High-sophistication attacker (nation-state, insider threat)
- Potentially undetectable if signing environment is compromised
- Affects entire token-gated architecture if successful

**ZoD defense:**
- HSM-backed key storage with hardware attestation
- Key rotation schedules with automated rollover
- Dual-control requirements for key operations
- Attestation of signing environment integrity
- "Break-glass" kill switch for token issuance with safe-halt capability
- L6 monitoring of signing operations and token issuance patterns

**Why this matters:** This is why "CA integrity" is a trust assumption, not a guarantee. The architecture limits damage through short TTLs and behavioral monitoring, but cannot fully defend against signing key compromise.

---

### Position 7: Insider / Policy Author

**Attack vector:** The attacker is an authorized user with access to modify CA policy, risk thresholds, allowlists, tool permissions, or monitoring configuration.

**This is the dominant enterprise failure mode:** not sophisticated external attacks, but insider + misconfiguration + rushed change.

**Techniques:**
- Malicious policy modification (expand agent permissions)
- Threshold manipulation (lower risk scores to avoid escalation)
- Allowlist poisoning (add attacker-controlled domains/endpoints)
- Monitoring configuration changes (disable alerts, widen baselines)
- GitOps/CI/CD pipeline compromise for policy deployment
- Social engineering of policy approvers

**Attack characteristics:**
- Attacker has legitimate credentials
- Changes may appear routine
- May be combined with external attack timing

**ZoD defense:**
- GitOps-managed policy with multi-party approval requirements
- All policy changes logged to immutable integrity channel
- L6 monitors policy change patterns (frequency, scope, timing)
- Separation of duties: policy authoring vs. policy enforcement vs. policy approval
- Policy change audit with mandatory review periods
- Canary policies that alert if modified
- Role-based access with principle of least privilege for policy systems

**Why this matters:** The attacker doesn't need to compromise the CA. They need to convince the CA that malicious actions are authorized.

---

## Trust Assumptions

The architecture explicitly depends on three trust assumptions. If these are violated, security guarantees do not hold.

### Assumption 1: Layer 1 Integrity

**What must be true:** The OS foundation—process isolation, credential brokering, identity management—must be trustworthy.

**If violated:** No application-layer security holds. A compromised OS can bypass all higher-layer controls.

**Mitigation:** Hardware root of trust, TPM attestation, secure boot verification, hypervisor isolation.

---

### Assumption 2: CA Integrity

**What must be true:** The Certificate Authority (Layer 4) must remain uncompromised.

**If violated:** Attacker can authorize arbitrary actions, bypass all validation.

**Why this is the critical dependency:** Every action flows through L4. It is the architectural chokepoint.

**ZoD treats the CA as a Tier-0 system**—similar to a KMS, HSM, or PKI root. It requires:
- Hardened, minimal attack surface
- Formally verified where possible
- Independent monitoring by L6
- Isolated infrastructure, not co-located with agents

**Mitigations:**
- Isolation: CA runs in separate security context, separate infrastructure
- Independent deployment: Not co-located with agent processes
- Baseline integrity verification: Layer 6 monitors CA behavior
- GitOps-managed policy: Changes require approval, logged immutably
- Redundancy: Multiple CA instances with consensus for high-risk actions

**Critical clarification:** CA compromise is catastrophic *only if* the attacker can:
1. Issue tokens without detection, AND
2. Bypass monitoring correlation, AND
3. Evade governance oversight

If any of those fail, compromise is detectable.

---

### Assumption 3: Executor Fidelity

**What must be true:** The execution layer (Layer 5) must faithfully execute the action specified in the token, not a modified version.

**If violated:** Token verification becomes meaningless—attacker intercepts valid token, executes different action.

**Mitigations:**
- Content hash binding: Token specifies hash of content to be acted upon
- Executor attestation: Token bound to specific execution service instance
- Immutable logging: Every action logged with token for post-hoc verification

---

## Explicit Non-Goals

The architecture explicitly does NOT attempt to:

### Non-Goal 1: Defend Against Full Layer 1 Compromise

If the operating system, hypervisor, or hardware root of trust is compromised, the architecture cannot provide guarantees. This is consistent with all security architectures—the trust chain must begin somewhere.

### Non-Goal 2: Make the Agent Un-Injectable

Layer 2 reduces injection success rates but cannot eliminate them. Some attacks are semantically indistinguishable from legitimate content.

**The architecture's value:** Containing consequences of successful injection, not preventing injection entirely.

### Non-Goal 3: Guarantee Detection of All Supply Chain Attacks

Model provenance attestation (L1) and behavioral monitoring (L6) provide probabilistic detection. Sufficiently sophisticated supply chain attacks that produce behaviorally normal but subtly compromised models may evade detection.

**The architecture's value:** Limiting damage through execution governance, not through guaranteed detection.

### Non-Goal 4: Eliminate All Single Points of Failure

The CA is a critical dependency. The architecture mitigates this through redundancy, integrity verification, and defined degradation modes—but does not claim to eliminate the dependency.

### Non-Goal 5: Prevent Cross-Tenant / Cross-Context Contamination

In multi-tenant SaaS deployments, agents may process data from multiple customers. Preventing data leakage between tenants is a platform isolation concern, not an agent architecture concern.

**ZoD assumes:** Tenant isolation is enforced at the infrastructure layer. If tenant boundaries are compromised, ZoD provides forensics but not prevention.

**For SaaS deployments:** Implement tenant-scoped CA policies, tenant-isolated execution environments, and tenant-tagged integrity signals.

### Non-Goal 6: Prevent Model Extraction or Inversion Attacks

Attacks against the underlying LLM—extracting training data, model weights, or performing membership inference—are model-level concerns outside ZoD's scope.

**ZoD assumes:** The model itself is a black box. The architecture governs what the agent can *do*, not what it can *reveal about itself*.

---

## Degradation Model

Each layer failure produces defined degradation, not undefined behavior.

| Layer Failure | What Happens | Mitigating Control | Residual Risk |
|---------------|--------------|-------------------|---------------|
| **L1 fails** (OS Foundation) | Process isolation lost, credential exposure possible | None—foundational assumption violated | **Critical** |
| **L2 fails** (Input Control) | Agent processes unscreened input | L3-L4 contain consequences; degraded modes activate | Medium |
| **L3 fails** (Cognitive Isolation) | Reasoning process may gain direct tool/execution access | Containment holds only if L1 network segmentation still enforces no privileged paths and L5 rejects unauthenticated requests | **Critical** |
| **L4 unavailable** (CA down) | No requests processed | System halts safely; no unauthorized execution | Operations halt (safe) |
| **L4 compromised** (CA misbehaving) | Invalid tokens may be issued | L6 detects anomalous issuance patterns; L7 catches high-risk actions | **High** |
| **L5 fails** (Execution) | Authorized actions may not complete | Retry with new token; audit log preserves state | Operations degraded |
| **L6 fails** (Monitoring) | Behavioral drift goes undetected | External health checks / infrastructure monitoring detect outage; L4 escalates scrutiny when monitoring is blind | Medium-High |
| **L7 fails** (Governance) | Policy cannot be updated | Existing policy continues; CA operates on last-known-good | Low (short-term) |

### Key Principle

**Fail safe, not fail open.** When Layer 4 is *unavailable*, the system halts—it does not default to allowing all requests. When Layer 4 is *compromised*, detection relies on L6/L7 correlation.

**Critical distinction:** CA unavailability is safe (operations halt). CA compromise is dangerous (operations continue with bad authorization). This is why CA integrity is a Tier-0 concern.

---

## Adversarial Resilience

A sophisticated attacker who understands the ZoD architecture does not need to compromise the agent. They can exploit the security controls themselves as attack vectors.

### Attack: Security Control Denial of Service

| Target | Technique | Impact |
|--------|-----------|--------|
| **L2 false positives** | Craft inputs that trigger filter on legitimate content | Agent operationally useless |
| **L4 escalation flooding** | Submit requests designed to trigger human escalation | Approval fatigue → subsequent malicious requests rubber-stamped |
| **L4 policy inference** | Repeated requests near policy thresholds to learn boundaries | Attacker maps policy via trial-and-error, crafts bypass |
| **L6 baseline poisoning** | Trigger false anomaly alerts | Security team disables alerts → real attack proceeds |
| **L7 fatigue exploitation** | Flood with medium-risk flags | Human oversight quality degrades |

**Countermeasures:**

| Defense | Mechanism |
|---------|-----------|
| Rate limiting | L4 implements rate limits on escalation triggers |
| Adaptive thresholds | Screening sensitivity adjustable without disabling controls |
| Alert quality scoring | L6 deprioritizes known false-positive patterns |
| Independent escalation channels | High-confidence indicators bypass standard queue |
| Degraded modes | L2 shifts to tagging mode rather than blocking under attack |
| Opaque denials | L4 does not explain why requests were denied |
| Jittered responses | Timing variance prevents side-channel inference |
| Probing detection | L6 flags repeated near-boundary requests as hostile behavior |

---

### Attack: Targeted Component Compromise

#### Attack: Manipulation of L6 Baselines

**Technique:** Gradually shift the monitoring system's behavioral baseline to make malicious behavior appear normal.

**Defenses:**
- Rate-of-change limits on baseline updates
- Cryptographic snapshots of baseline state
- Independent verification through integrity signal channel
- Long-horizon baseline comparison (weeks/months, not just recent history)

---

#### Attack: CA Policy Poisoning

**Technique:** Modify the CA's policy configuration to authorize previously restricted actions.

**Defenses:**
- GitOps-managed policy with approval requirements
- Policy change logging to immutable audit channel
- L6 monitoring of policy change patterns
- Separation of policy authoring from policy enforcement

---

#### Attack: Integrity Channel Flooding

**Technique:** Generate excessive signals to cause alert fatigue or overwhelm correlation analysis.

**Defenses:**
- Per-source rate limiting
- Priority classification of signals
- Automated signal quality scoring
- Separate channels for high-confidence indicators
- **Backpressure strategy:** buffer with priority queue, drop low-priority first under load
- **"Must not drop" channel:** separate, capacity-protected channel for critical signals (key compromise alerts, token mint anomalies, CA integrity failures) that bypasses standard queuing

---

#### Attack: Integrity Channel Tampering

The integrity signal channel itself is an attack surface. An attacker with channel access might attempt:

| Attack | Technique | Goal |
|--------|-----------|------|
| **Forgery** | Inject fabricated events | Create false audit trail, frame legitimate activity as malicious |
| **Suppression** | Selectively drop events | Hide evidence of compromise |
| **Reordering** | Delay or resequence events | Break correlation analysis, create alibi |
| **Desynchronization** | Cause clock drift between sources | Make cross-layer correlation unreliable |

**Defenses:**

| Defense | Mechanism |
|---------|-----------|
| **Signed events** | Every signal cryptographically signed by originating layer; forgery requires key compromise |
| **Sequence numbers** | Monotonic counters per source; gaps indicate suppression |
| **Merkle chain / transparency log** | Append-only structure where each entry commits to all previous; reordering breaks chain |
| **Timestamping** | Trusted timestamping service; bounded clock skew detection |
| **Write-once storage** | WORM storage, S3 Object Lock, or blockchain-adjacent immutability |
| **Out-of-band transmission** | Channel architecturally separate from agent data path |

**The architecture treats integrity channel health as first-class telemetry.** L6 monitors for gaps, timing anomalies, and signature failures in the signal stream itself.

---

#### Attack: Token Extraction and Replay

**Technique:** Intercept valid token between CA and execution layer, replay it.

**Defenses:**
- Nonce-based replay prevention
- Executor attestation (token bound to specific execution service)
- Short TTLs limiting replay window
- Encrypted token transmission

---

#### Attack: Multi-Agent Consensus Manipulation

**Technique:** In voting architectures, control minority of agents to manipulate consensus.

**Defenses:**
- Independent CA validation for each agent's requests
- Multi-agent chain validation evaluates collective action
- Behavioral drift detection across agent pool
- No implicit trust inheritance between agents

---

#### Attack: Gradual Behavioral Shift ("Boiling Frog")

**Technique:** Change agent behavior so slowly that drift detection doesn't trigger.

**Defenses:**
- Long-horizon baseline comparison (current vs. weeks/months ago)
- Absolute bounds on certain behaviors (trigger regardless of drift rate)
- Periodic human review of agent behavioral profiles (L7)
- NVIDIA/Lakera ARP methodology for systematic evasion testing

---

## Attack Surface Summary

| Attack Surface | Primary Defense | Secondary Defense | Detection |
|----------------|-----------------|-------------------|-----------|
| Input content | L2 screening | L3-L4 containment | L6 behavioral anomaly |
| Tool endpoints | L4 semantic validation | L4 scope limits | L6 action pattern monitoring |
| Tool abuse (living off the land) | L4 semantic policy (negative constraints) | Egress controls | L6 detects abuse patterns |
| Tool output (reality poisoning) | Multi-source corroboration | Signed tool responses | L6 anomaly on tool results |
| Memory/RAG | L6 memory audit | L4 validates requests from memory | L6 baseline drift |
| Information laundering | L4 egress classification | Output content analysis | L6 detects compression patterns |
| Peer agents | L4 chain validation | P10 non-propagation | L6 cross-agent correlation |
| Model supply chain | L1 provenance | L6 behavioral monitoring | Probabilistic only |
| Identity/key plane | HSM-backed keys, dual control | Key rotation, attestation | L6 signing anomalies |
| Insider/policy author | Multi-party approval, GitOps | Separation of duties | L6 policy change monitoring |
| Security controls | Rate limiting, adaptive thresholds | Independent channels | L6 monitors control health |
| Integrity channel | Signed events, sequence numbers | Merkle chain, WORM storage | Gap/timing anomaly detection |

### Control Health as First-Class Telemetry

**The architecture treats security control health metrics as first-class telemetry:**
- L2 false positive rates
- L4 escalation frequency and patterns
- L6 alert volume and quality scores
- Integrity channel gap/latency metrics
- CA issuance rate distribution

Anomalies in control health are themselves compromise indicators.

---

## Incident Response Integration

When a threat is detected:

| Detection Source | Immediate Action | Escalation Path |
|------------------|------------------|-----------------|
| L2 flags content | Quarantine, summarize for agent | L7 if repeated pattern |
| L4 denies request | Log, alert if anomalous pattern | L7 if high-risk action |
| L6 detects drift | Increase L4 scrutiny, alert | L7 for investigation |
| L6 memory audit flags | Isolate memory segment, alert | L7 for remediation decision |
| Cross-layer correlation | Elevate alert confidence | L7 priority channel |

---

## Related Documents

- [Architecture Specification](ARCHITECTURE.md) — How the layers implement these defenses
- [Security Properties](security-properties.md) — Measurable properties that verify defense effectiveness
- [Implementation Guide](implementation-guide.md) — Practical deployment guidance

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
