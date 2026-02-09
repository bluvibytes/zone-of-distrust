# NVIDIA/Lakera AI Safety and Security Framework Mapping

**Zones of Distrust ↔ NVIDIA/Lakera Framework (November 2025)**

*Version 0.9 RFC — February 2026*

---

> **Mapping Disclaimer:** This document maps the referenced framework to Zones of Distrust (ZoD) for architectural alignment purposes. ZoD is **complementary, not a replacement**, and this mapping does **not** claim certification, compliance, endorsement, or affiliation. Control alignment depends on correct implementation, configuration, and supporting security practices. "Mapped" indicates enforcement, detection/containment, or audit evidence support—not guaranteed prevention.

---



## Overview

NVIDIA and Lakera published a research-backed framework connecting component-level risks to system-level harms, introducing autonomy levels and the Agent Red Teaming via Probes (ARP) methodology.

**Relationship:** NVIDIA/Lakera defines *risk levels and testing methodology*. ZoD provides *architectural implementation*.

**Key NVIDIA Resources:**
- AI Safety and Security Framework paper
- Autonomy levels (L0-L3) with escalating controls
- ARP methodology (Agent Red Teaming via Probes)
- AI-Q Research Assistant case study (10,000+ traces)
- Nemotron-AIQ Agentic Safety Dataset
- NeMo Guardrails, garak scanner

---

## Autonomy Levels → ZoD Enforcement

NVIDIA defines four autonomy levels with escalating security requirements.

### Level 0: Human Executes

**Definition:** Human performs all actions; agent provides information only.

**ZoD Enforcement:**

| Requirement | ZoD Layer | Control |
|-------------|-----------|---------|
| No agent execution | L5 | Execution disabled; information-only mode |
| Human performs actions | L7 | All actions routed to human |
| Agent advises only | L3 | Agent reasons but cannot emit action requests |

---

### Level 1: Human Approves Each Action

**Definition:** Agent proposes actions; human approves each one before execution.

**ZoD Enforcement:**

| Requirement | ZoD Layer | Control |
|-------------|-----------|---------|
| Per-action approval | L7 | Every request escalates to human |
| Clear action display | L4 | Token captures full action parameters |
| Approval workflow | L7 | Queue-based approval interface |
| Rejection capability | L7 | Human can deny any request |

**ZoD Configuration (Illustrative):**

*Note: Configuration format shown is illustrative; actual implementations may use different schemas, formats, or tooling.*

```yaml
autonomy_level: 1
escalation:
  all_actions: human_required
  timeout: 300s  # 5 minute approval window
```

---

### Level 2: Human Approves High-Risk

**Definition:** Agent executes low-risk actions autonomously; high-risk requires human approval.

**ZoD Enforcement:**

| Requirement | ZoD Layer | Control |
|-------------|-----------|---------|
| Risk classification | L7 | Define low/medium/high thresholds |
| Auto-approve low-risk | L4 | Fast-path for pre-approved patterns |
| Escalate high-risk | L7 | Route to human approval queue |
| Risk determination | L4 | External classification, not agent self-assessment |

**ZoD Configuration:**
```yaml
autonomy_level: 2
risk_classification:
  low: [read_operations, status_queries]
  medium: [write_operations, data_modifications]
  high: [financial, external_communication, deletions]
  critical: [bulk_operations, privilege_changes]
escalation:
  high: human_required
  critical: multi_person_required
```

---

### Level 3: Autonomous Within Policy

**Definition:** Agent operates autonomously within defined policy boundaries.

**ZoD Enforcement:**

| Requirement | ZoD Layer | Control |
|-------------|-----------|---------|
| Policy boundaries | L4 | Semantic intent policy (comprehensive) |
| Behavioral monitoring | L6 | Continuous observation, drift detection |
| Anomaly escalation | L6, L7 | Automatic escalation on drift |
| Audit trail | Integrity | Full logging of all actions |
| Override capability | L7 | Human can intervene at any time |

**ZoD Configuration:**
```yaml
autonomy_level: 3
policy_enforcement: strict
monitoring:
  behavioral_baseline: enabled
  drift_threshold: 2_sigma
  escalation_on_anomaly: true
human_override: always_available
```

---

## Risk Amplification Finding → ZoD Design

**NVIDIA Finding:** Direct user input attacks amplify differently than external data source attacks through processing pipelines.

**ZoD Design Response:**

| Input Source | Amplification Risk | ZoD Control |
|--------------|-------------------|-------------|
| Direct user input | Lower (single hop) | L2 standard screening |
| External documents | Higher (content embedded) | L2 enhanced screening |
| RAG retrieval | Higher (trusted context) | L2 screen retrieved content |
| Tool outputs | Higher (assumed valid) | L2 screen all tool results |
| Peer agent output | Highest (inherited trust) | L2 + L4 chain validation |

**Implication:** L2 screening intensity should scale with trust level of source.

---

## Component Risk → System Harm Mapping

NVIDIA maps component-level risks to system-level harms with severity ratings.

### Component: Input Processing

| Component Risk | System Harm | Severity | ZoD Control |
|----------------|-------------|----------|-------------|
| Prompt injection | Goal hijacking | High | L2 screening |
| Context manipulation | Reasoning corruption | High | L2, L3 |
| Jailbreak | Safety bypass | High | L2, L4 |

### Component: Memory/State

| Component Risk | System Harm | Severity | ZoD Control |
|----------------|-------------|----------|-------------|
| Memory poisoning | Persistent compromise | High | L6 memory audit |
| State corruption | Behavioral drift | Medium | L6 baseline monitoring |
| Context overflow | Reasoning degradation | Medium | L2 limitation ceiling |

### Component: Tool Integration

| Component Risk | System Harm | Severity | ZoD Control |
|----------------|-------------|----------|-------------|
| Malicious tool | System compromise | Critical | L1 provenance |
| Tool misuse | Unauthorized actions | High | L4 semantic policy |
| Parameter injection | Action manipulation | High | L4 parameter binding |

### Component: Output Generation

| Component Risk | System Harm | Severity | ZoD Control |
|----------------|-------------|----------|-------------|
| Data exfiltration | Confidentiality breach | Critical | L4 egress control |
| Unauthorized actions | Integrity violation | High | L4 authorization |
| Harmful content | Safety violation | High | L4 negative constraints |

---

## ARP Methodology → ZoD Validation

NVIDIA's Agent Red Teaming via Probes (ARP) provides systematic testing methodology.

### Using ARP to Validate ZoD

| ARP Phase | ZoD Validation Target |
|-----------|----------------------|
| Probe design | Create tests for each ZoD layer |
| Attack execution | Attempt bypass of L2, L4 controls |
| Defense evaluation | Measure detection/prevention rates |
| Trace analysis | Review integrity channel logs |
| Iteration | Update screening signatures, policies |

### ARP Test Categories → ZoD Layers

| ARP Category | ZoD Layer to Test |
|--------------|-------------------|
| Direct injection probes | L2 input screening |
| Indirect injection probes | L2 document/content screening |
| Tool manipulation probes | L4 parameter binding |
| Memory poisoning probes | L6 memory audit |
| Multi-turn attack probes | L6 behavioral baseline |
| Multi-agent probes | L4 chain validation |

---

## NVIDIA Safety Recipe → ZoD Integration

| NVIDIA Component | ZoD Integration Point |
|------------------|----------------------|
| **NeMo Guardrails** | L2 input screening implementation |
| **garak scanner** | Validation testing for L2, L6 |
| **Cisco AI Defense** | L1 supply chain, L6 monitoring |
| **CrowdStrike** | L6 endpoint monitoring integration |

---

## AI-Q Case Study Findings → ZoD Validation

NVIDIA's AI-Q Research Assistant case study provides empirical data.

| Finding | ZoD Validation |
|---------|----------------|
| 10,000+ attack traces | Test suite for L2 screening |
| 22 threat scenarios | Verify coverage across all scenarios |
| 9 risk categories | Map to ZoD layer controls |
| Defense effectiveness data | Benchmark L2, L4 detection rates |

---

## Summary Matrix

| NVIDIA Concept | ZoD Implementation |
|----------------|-------------------|
| Autonomy Level 0 | L5 disabled, L7 manual |
| Autonomy Level 1 | L7 per-action escalation |
| Autonomy Level 2 | L4 risk classification, L7 selective escalation |
| Autonomy Level 3 | L4 policy, L6 monitoring, L7 override |
| Risk amplification | L2 source-aware screening |
| Component risks | Multi-layer defense (L1-L6) |
| System harms | Severity-weighted L4/L7 response |
| ARP methodology | ZoD validation testing |
| Safety Recipe | L1, L2, L6 tooling integration |

---

## Verification Checklist

**Autonomy Level Enforcement**
- [ ] L0: Agent cannot emit action requests
- [ ] L1: All actions require human approval
- [ ] L2: Risk classification operational, escalation working
- [ ] L3: Full policy enforcement, monitoring active

**Risk Amplification Controls**
- [ ] L2 screening intensity scales with source trust
- [ ] Peer agent output receives maximum scrutiny

**ARP Validation**
- [ ] Direct injection probes tested against L2
- [ ] Memory poisoning probes tested against L6
- [ ] Multi-agent probes tested against L4

**Tool Integration**
- [ ] NeMo Guardrails integrated with L2 (if applicable)
- [ ] garak scanner used for validation testing

---

## References

- [NVIDIA AI Safety and Security Framework](https://arxiv.org/abs/2411.10685)
- [Nemotron-AIQ Dataset](https://huggingface.co/datasets/nvidia/Nemotron-AIQ)
- [NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails)
- [ZoD Architecture Specification](../docs/architecture.md)
- [ZoD Implementation Guide](../docs/implementation-guide.md)

---

*Zones of Distrust v0.9 RFC — February 2026 — BluVi*
