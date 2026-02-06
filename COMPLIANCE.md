# Zones of Distrust Compliance Levels

This document defines testable compliance levels for implementations of the Zones of Distrust (ZoD) architecture.

Compliance levels are cumulative: ZoD-L3 implies L1 + L2 + L3.

## ZoD-L1 — Tokenized Execution Enforcement (Minimum Viable ZoD)
**Required**
- Reasoning/execution separation: reasoning component cannot directly execute privileged actions.
- A Certificate Authority (CA) issues execution tokens.
- Executors validate tokens before execution.
- Tokens are:
  - single-use (nonce enforced),
  - time-bound (TTL enforced),
  - cryptographically signed,
  - parameter-bound to an action + target.

**Pass criteria**
- No privileged action can execute without a valid CA-issued token.
- Token replay attempts are rejected and logged.

## ZoD-L2 — Semantic Intent Policy
**Required**
- CA enforces tool-specific semantic intent constraints (negative constraints) such as:
  - allowlisted domains (email),
  - payload size ceilings,
  - prohibited content patterns,
  - table/query constraints (databases),
  - egress destinations.
- Policy is machine-evaluable (e.g., OPA/Rego or equivalent).

**Pass criteria**
- Authorized credentials cannot be used to perform disallowed semantic actions (e.g., exfil via allowed tool).

## ZoD-L3 — Monitoring and Memory Audit
**Required**
- Behavioral baselining and drift detection per agent identity.
- External memory audit for any persistent memory store or RAG store.
- Baseline integrity verification (detect baseline poisoning / drift-of-baseline).

**Pass criteria**
- Drift produces alerts within configured thresholds.
- Memory poisoning patterns are detectable probabilistically (alert + evidence).

## ZoD-L4 — Integrity Signal Channel and Correlation
**Required**
- Append-only Integrity Signal Channel (ISC) that logs:
  - token issuance/denial,
  - token usage,
  - execution outcomes,
  - mismatches,
  - replay attempts,
  - monitoring alerts,
  - policy changes.
- Out-of-band separation: agent runtime cannot alter or suppress ISC events.
- Correlation capability between CA issuance and executor execution.

**Pass criteria**
- Orphaned actions, mismatched actions, and token replay attempts are detectable via ISC correlation.

## ZoD-L5 — Multi-Agent Chain Validation
**Required**
- Delegation chain representation for cross-agent requests.
- Chain-level validation: delegator authority + delegate authority + chain policy.
- Aggregate/collective abuse detection attempts (many low-risk actions => high-risk outcome).

**Pass criteria**
- Chain abuse scenarios are blocked or escalated even when each individual step appears benign.

## Claiming Compliance
Projects claiming compliance SHOULD:
- state their ZoD level,
- link to the specific version of the ZoD spec they implement,
- provide an automated test report (see attacks/ or test harness).
