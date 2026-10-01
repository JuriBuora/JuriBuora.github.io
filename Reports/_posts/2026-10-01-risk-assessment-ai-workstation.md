---
layout: post
title: "Risk Assessment of My AI Agent Workstation Using NIST SP 800-30"
summary: "A qualitative risk assessment of the system I run: twelve risks identified, scored for likelihood and impact, ranked, and paired with a treatment. Likelihood is grounded in incidents that actually happened here."
date: 2026-10-01
categories: reports
tags: [Cybersecurity, Reports, RiskAssessment, NIST, ThreatModeling, RiskRegister, AISecurity]
number: 9
---

## Summary in plain language

I run a set of AI agents on my own computer. They can read and write files, use a calendar, browse the web and send messages. This report asks a simple question in a structured way: **what could go wrong, how likely is it, how bad would it be, and what am I doing about it?**

I used the method in NIST SP 800-30 Rev. 1, the United States standard guide for risk assessments. It is qualitative: likelihood and impact are rated from Very Low to Very High and combined into a risk level.

The result is twelve risks. Before controls, one is rated High, eight Moderate and three Low. After the controls already in place, none is High, four are Moderate and eight are Low.

**The three I would act on first:**

1. **An agent is tricked by text it reads** (a message or a web page) into doing something I did not ask for.
2. **Loss of the device or account I use to reach the system remotely.** One shared access token protects it, and it cannot be revoked per device.
3. **A credential ends up somewhere it should not be.** This has already happened once.

## 1. Purpose, scope and tier

- **Purpose.** Decide where to spend effort next, and have a written, defensible basis for it.
- **Tier.** Information-system level (Tier 3). There is no wider organisation: I am the owner, the operator and the only user.
- **In scope.** The laptop running the agents, the agent gateway and its plugins, the messaging channels it listens on, the tools the agents can use, the local memory store, remote access from my phone, and the backup target.
- **Out of scope.** The outside AI providers' own security, my email and cloud accounts beyond how the agents reach them, and physical security of my home.
- **Time horizon.** The next twelve months.

## 2. Assumptions, constraints and risk model

**Assumptions.**

- The most plausible adversary is opportunistic: someone who can send the system a message or put content on a web page it reads. I am not assuming a well-resourced attacker targeting me personally.
- I make mistakes. Accidental and structural causes are treated as seriously as adversarial ones, because in my own incident history they are far more common.

**Constraints.** This is a self-assessment. No external penetration test or vulnerability scan was done for it. Vulnerability information comes from my own reviews and incident records.

**Information sources.** My incident and audit records from June to September 2026, the system's configuration, and MITRE ATT&CK and ATLAS for naming techniques.

**Risk model.** For each threat event: who or what causes it, what weakness it uses, how likely it is to happen and cause harm, and how large the harm would be. Likelihood and impact combine as in the standard's own table:

| Likelihood \ Impact | Very Low | Low | Moderate | High | Very High |
| --- | --- | --- | --- | --- | --- |
| **Very High** | Very Low | Low | Moderate | High | Very High |
| **High** | Very Low | Low | Moderate | High | Very High |
| **Moderate** | Very Low | Low | Moderate | Moderate | High |
| **Low** | Very Low | Low | Low | Low | Moderate |
| **Very Low** | Very Low | Very Low | Very Low | Low | Low |

**Impact in my terms.** High means private information exposed, a message sent to a real person that should not have been, or a destructive action I cannot undo. Very High means losing control of the system or of accounts it can reach.

## 3. Threat sources

| Type | Source | Notes |
| --- | --- | --- |
| Adversarial | Someone who can message the system or publish content it reads | Low to moderate capability, opportunistic |
| Adversarial | A malicious or compromised third-party tool or skill | Supply chain |
| Accidental | Me | Wrong configuration, a secret left in a file, a rushed merge |
| Structural | The agents themselves | Reporting success that did not happen; acting on stale information |
| Structural | Software and hardware | Outdated packages, disk failure, a service that never restarted |
| Environmental | Loss or theft of the laptop or phone | |

## 4. Predisposing conditions

These raise or lower every risk below.

- **Raises risk:** agents hold real tools. Coding agents run with broad local permissions by design, so that work is not interrupted by constant prompts.
- **Raises risk:** one person runs everything. There is no second reviewer by default.
- **Raises risk:** the gateway is a customised copy of an open-source project and can fall behind it.
- **Lowers risk:** nothing is exposed to the public internet. Remote access goes through a private network.
- **Lowers risk:** hard-stop boundaries are written down and enforced for credentials, payments, personal documents and anything a backup cannot undo.
- **Lowers risk:** work is version-controlled and checkpointed, so most mistakes are reversible.

## 5. Risk register

Likelihood and impact are before controls. Residual is after the controls that exist today.

| ID | Threat event | Source | Likelihood | Impact | Risk | Controls in place | Residual |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R1 | Agent follows instructions hidden in a message or web page (prompt injection, ATLAS AML.T0051) | Adversarial | Moderate | High | Moderate | Minimal tools on messaging channels, credential tools removed there, a filter at the delivery step, replies tied to a recent incoming message | Moderate |
| R2 | A credential is left in a file, workflow or log (ATT&CK T1552) | Accidental | High | High | **High** | Secret scan before push, tested rotation procedure, one owner per credential | Moderate |
| R3 | Agent reports work as done when it is not | Structural | Very High | Moderate | Moderate | Independent verification for important work, evidence required | Moderate |
| R4 | A destructive action is triggered with no identifiable caller | Adversarial or accidental | Moderate | High | Moderate | Default-deny on shutdown with caller logging; one operating-system path still open | Low |
| R5 | Laptop lost, stolen or its disk fails | Environmental or structural | Low | Very High | Moderate | Encrypted off-machine backup, restore tested | Low |
| R6 | A wrong or internal message reaches a real person | Structural | High | Moderate | Moderate | Filter and rewrite step before delivery, silence when unsure, per-conversation switch | Low |
| R7 | Known vulnerabilities in the gateway's dependencies | Structural | Moderate | Moderate | Moderate | Audited to zero findings in August; no scheduled re-scan | Low |
| R8 | The gateway copy falls far behind upstream, so fixes cannot be applied quickly | Structural | High | Moderate | Moderate | A drift check whenever I work in the repository | Low |
| R9 | The web-reading tool is used to reach internal addresses (SSRF) | Adversarial | Low | High | Low | Five bypasses fixed and tested; tool not connected to any unattended path | Low |
| R10 | The phone, or the private-network account, is compromised and used to control the agents | Adversarial or environmental | Low | Very High | Moderate | Private network only, pairing token, sender checks on chat commands | Moderate |
| R11 | Email is forged in my domain's name | Adversarial | Moderate | Low | Low | SPF, and a DMARC record in monitoring mode | Low |
| R12 | A third-party skill or tool does more than it claims | Adversarial | Low | High | Low | Source review, pinned versions, a small allowlist | Low |

### How the likelihoods were set

Where I could, I used what has actually happened here instead of guessing:

- **R2** is High because it happened: a bot token was found in a workflow in September, and the first rotation attempt reported success while the service was down.
- **R3** is Very High because it is routine: an overnight run claimed six jobs done and most were not, and in one week I fixed the same "claimed success without proof" pattern four times.
- **R4** is Moderate because it happened once, in August, and the caller was never identified.
- **R6** is High because it happened repeatedly while a messaging feature was being rolled out.
- **R7** and **R8** both happened: 76 dependency findings in August, and a copy 17,898 commits behind in September.
- **R1**, **R10** and **R12** have not happened here. They are rated on what the system exposes.

## 6. Top risks, in plain terms

**R1. An agent tricked by what it reads. Residual: Moderate.**
This is the least solved problem in the whole field, not only here. My controls limit what a tricked agent can do, which is the right place to put them, but they do not prevent the trick. Coding agents with broad permissions reading untrusted content are the sharpest edge.
*Treatment: mitigate.* Keep untrusted content away from agents that hold broad permissions. Extend the delivery-step filter idea to actions, not only messages.

**R10. Losing the way in. Residual: Moderate.**
The pairing token is a single shared secret. If the phone is lost I cannot revoke that one device; I have to replace the token everywhere.
*Treatment: mitigate.* Per-device tokens that can be revoked individually, and a written, rehearsed "phone lost" procedure.

**R2. A credential in the wrong place. Residual: Moderate.**
Scanning catches secrets before they are pushed. It does not catch one pasted into a workflow tool or printed in a log.
*Treatment: mitigate.* Extend scanning to workflow exports and logs, and keep reducing the number of places each secret is stored.

**R3. False success. Residual: Moderate.**
The supervision layer that enforced independent checks is currently switched off while I rework it, so this depends more on my own review than I would like.
*Treatment: mitigate.* Bring verification back in a lighter form for the work that matters most.

## 7. Treatment plan

| Risk | Treatment | Action | Effort |
| --- | --- | --- | --- |
| R11 | Mitigate | DMARC record added in monitoring mode on the day of this assessment; tighten it after two weeks | Minutes |
| R7 | Mitigate | Schedule a monthly dependency scan with an alert | Small |
| R10 | Mitigate | Per-device revocable tokens; lost-phone procedure | Medium |
| R2 | Mitigate | Scan workflow exports and logs for secrets | Medium |
| R4 | Mitigate | Close the remaining operating-system shutdown path | Small, needs my hands |
| R1 | Mitigate | Separate untrusted-content reading from broad-permission agents | Large |
| R3 | Mitigate | Re-enable lighter independent verification | Medium |
| R5 | Accept, then improve | Add a backup copy in a different location | Small |
| R6, R8, R9, R12 | Accept | Controls in place are adequate for a single-user system. Review at next refresh | None now |

## 8. Maintenance

- **Refresh.** Every three months, and immediately after any of the triggers below.
- **Triggers.** A new channel or tool given to an agent. Connecting the web-reading tool to anything unattended. A security incident. A major upgrade of the gateway. Any change to how I reach the system remotely.
- **Owner.** Me, for every risk. That concentration is itself a limit of this assessment.

## Limits of this assessment

- It is a self-assessment by the person who built the system. I know it well and I am also the person most likely to be blind to its weak points.
- No external test was performed. Unknown vulnerabilities are, by definition, not in the register.
- The ratings are qualitative judgements on a standard scale. They rank the risks against each other. They are not measurements.

<!-- 01-10-2026 22:59 -->
