# Security Policy

Zones of Distrust (ZoD) is a security-focused project. We welcome responsible vulnerability research and adversarial review.

---

## Supported Versions

This project is under active development. Security issues should be reported privately.

At this stage, the `main` branch and the most recent tagged release (if available) are considered supported.

---

## Reporting a Vulnerability

Please report security vulnerabilities by emailing:

**security@bluvi.ai**

Include the following when possible:

- a clear description of the issue
- steps to reproduce (PoC encouraged)
- expected vs actual behavior
- affected component(s) and ZoD layer(s)
- any relevant logs, screenshots, or traces
- impact assessment (confidentiality, integrity, availability)
- whether the issue enables policy bypass, token replay, executor abuse, or memory poisoning

If you believe the issue is actively exploitable, please indicate urgency in the subject line.

---

## Response Timeline

We will acknowledge receipt within a reasonable time.

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

- social engineering attacks
- denial-of-service via excessive traffic
- vulnerabilities in third-party dependencies (unless exploitable through ZoD design or default configuration)
- theoretical attacks without a reproducible demonstration

---

## Security Philosophy

ZoD is designed under the assumption that reasoning systems can be manipulated. Security issues that demonstrate bypass of:

- policy enforcement
- token binding
- executor validation
- integrity signal correlation
- memory audit assumptions

are considered high priority.
