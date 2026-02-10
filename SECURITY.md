# Security Policy

Zones of Distrust (ZoD) is a security-focused project. We welcome responsible vulnerability research and adversarial review.

---

## Supported Versions

This project is under active development.

| Version | Supported |
|--------|-----------|
| `main` | Yes |
| Latest tagged release (if available) | Yes |
| Older tagged releases | No |

---

## Reporting a Vulnerability

**Please do not report security issues via GitHub Issues or Discussions.**

Report vulnerabilities privately by emailing:

**security@bluvi.ai**

If your report contains highly sensitive details, request an encrypted reporting channel.

Include the following when possible:

- Clear description of the issue
- Steps to reproduce (PoC encouraged)
- Expected vs actual behavior
- Affected component(s) and ZoD layer(s)
- Relevant logs, screenshots, or traces
- Impact assessment (confidentiality, integrity, availability)
- Whether the issue enables:
  - policy bypass
  - token replay
  - token forgery
  - executor abuse
  - integrity logging bypass
  - monitoring evasion
  - memory poisoning / provenance corruption

If you believe the issue is actively exploitable, indicate urgency in the subject line.

---

## Response Timeline

We will acknowledge receipt as soon as reasonably possible.

Where possible, we aim to respond within:

- **Critical severity:** 48 hours  
- **High severity:** 5 business days  
- **Medium/Low severity:** as capacity permits  

We will coordinate a fix and disclosure timeline based on severity and reproducibility.

---

## Safe Harbor (Good Faith Research)

We support responsible disclosure and good-faith security research.

If you:

- act in good faith
- avoid privacy violations and data exfiltration
- avoid disruption of services or infrastructure
- do not exploit beyond proof-of-concept
- do not attempt persistence, lateral movement, or destructive actions
- do not test against production systems or third-party systems without explicit authorization

then we will not pursue legal action related to your report.

---

## Coordinated Disclosure

If you plan to publish details of a vulnerability, please coordinate with us first so we can:

- validate the issue
- develop a fix or mitigation
- publish an advisory
- credit the reporter appropriately

---

## Out of Scope

The following are generally out of scope unless they result in a clear security impact:

- denial-of-service via excessive traffic
- vulnerabilities in third-party dependencies (unless exploitable through ZoD design or default configuration)
- theoretical attacks without a reproducible demonstration
- social engineering of humans unless it results in compromise of ZoD artifacts (code, policies, releases, CI/CD)

---

## Security Philosophy

ZoD is built on the assumption that reasoning systems (LLMs/agents) can be manipulated, misled, or prompt-injected.

Because of that, general model misbehavior is expected and not always a ZoD vulnerability by itself.

High-priority security issues are those that demonstrate a bypass of ZoD’s enforcement boundaries, including:

- Policy enforcement bypass (actions executed outside allowed scope)
- Execution token bypass (tokens can be replayed, forged, reused, or used with modified parameters)
- Executor validation failure (executor performs actions without correct signature/TTL/nonce/binding checks)
- Integrity logging / monitoring bypass (actions occur without reliable audit signals or correlation visibility)
- Memory integrity / provenance failure (poisoned or tampered memory can influence execution without detection)

In short: ZoD assumes reasoning can fail. Vulnerabilities that enable unauthorized execution despite ZoD controls are treated as highest severity.
