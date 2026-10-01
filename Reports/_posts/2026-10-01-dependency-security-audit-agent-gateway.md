---
layout: post
title: "Audit Report: Dependency Vulnerabilities in My Self-Hosted AI Agent Gateway, From 76 Findings to Zero"
summary: "A dependency security audit of the agent gateway I run on my own machine. 76 known-vulnerability findings were reduced to 2 in a first pass and to 0 in a second, without hiding any behind a version number."
date: 2026-10-01
categories: reports
tags: [Cybersecurity, Reports, SupplyChainSecurity, DependencyManagement, VulnerabilityManagement, PatchManagement]
number: 2
---

## Summary in plain language

I run a customised copy of an open-source AI agent gateway on my own machine. Like any software, it is built on top of other people's packages, and those packages get security advisories.

On 1 August 2026, while repairing the gateway after a crash, I audited its dependencies. The scanner listed **76 findings** in the Python packages and **6** in the JavaScript ones.

- A first pass brought Python down to **2 moderate findings** and JavaScript to **1 advisory that did not apply** to how the gateway is built.
- A second pass, the same day, closed those properly. The final state was **0 findings across 133 components**, with all npm workspaces at 0 vulnerabilities.
- Nothing was cleared by editing a version number to make a scanner quiet, and one "fix" the tooling suggested was refused because it would have made things worse.

This report covers the work done on that date. It is written up here from the records made at the time.

| Measure | Start | After pass 1 | After pass 2 |
| --- | --- | --- | --- |
| Python findings | 76 | 2 (moderate) | 0 |
| JavaScript findings | 6 | 2 entries, 1 advisory | 0 |
| Gateway's own security audit | not run | not run | 0 findings, 133 components |
| Targeted Python tests passing | not run | 245 | 394 |
| Web interface tests passing | not run | builds pass | 97 of 97 |
| Desktop app tests passing | not run | not run | 3,076 of 3,076 |

## Scope and method

**In scope.** The gateway's Python dependencies, its JavaScript dependencies (the web dashboard, the terminal interface, the desktop app and a browser tool), and whether the service still worked after each change.

**Method.**

1. Run the dependency scanners and record every finding, not just the count.
2. Compare each vulnerable package with the version the upstream project had already patched to, and move to that exact version.
3. After every change: run the tests, check that no package requirements were broken, build the interfaces, and confirm the live service still answered.
4. For anything left over, decide from evidence whether it applied, and write the reason down.

**Out of scope.** The gateway's own source code was not reviewed line by line in this audit. No penetration testing was done.

## Findings and what was done

### 1. Python: 76 findings from outdated packages

**Cause.** My copy of the gateway had drifted behind the upstream project, which had already moved to patched versions.

**What was done.** More than a dozen packages were moved to the exact versions upstream uses, including the cryptography library, the image library, the web framework's request parser and the packaging tools.

**Result.** 76 findings became 2, both moderate. 245 targeted tests passed and the requirements check reported nothing broken.

### 2. Two findings that a version bump could have hidden

The two remaining Python findings were advisories against the gateway package itself, at the version my copy declared: one for uncontrolled resource consumption, one for injection.

**The tempting shortcut.** Change the declared version number. The scanner matches on version, so it would have gone quiet, and the vulnerable code would still have been there.

**What was done instead.** In the second pass the actual upstream fixes were brought into my copy, among them authenticated admission for a webhook endpoint. Only then was the version changed, to a value that truthfully marks it as upstream's patched release plus my own changes.

**Result.** Both findings closed by the code changing, not by the label changing.

### 3. JavaScript: a suggested fix that would have made things worse

After refreshing the JavaScript lock file, four of six findings were gone. The remaining two entries were one advisory in the routing library, reported twice.

**The suggested fix.** The package manager proposed downgrading the routing library.

**Why it was refused.** The downgrade target reintroduced older, more serious advisories, including cross-site scripting and remote code execution. The remaining advisory affected a server-rendering mode the gateway does not use. Staying on the newer line was the safer position, and the reason was recorded instead of the finding being ignored.

**How it was closed.** In the second pass the web dashboard and the desktop app were migrated to the library's next major version, which carries no advisory. Five other JavaScript packages were refreshed at the same time.

**Result.** Every npm workspace at 0 vulnerabilities, 97 of 97 web tests and 3,076 of 3,076 desktop tests passing, and the dashboard checked in a real browser at desktop and phone widths.

### 4. A diagnostic that reported a failure that was not there

During the same repair, the gateway's health check kept reporting the model route as broken. The route was fine. The check only knew about two older ways of reaching the model and rejected a newer, legitimate one.

**Why it belongs in a security report.** A monitor that raises false alarms trains you to ignore it. It was fixed, with fourteen tests, and three full live probes passed.

## What I would tell another team

- **A lower count is not the goal.** The first pass ended at "2 findings". It would have been easy to stop there or to bump a version. The rule I wrote down afterwards: dependency remediation must improve the whole advisory picture, not merely lower a number.
- **Read what a suggested fix does.** An automatic downgrade removed one advisory and brought back several worse ones.
- **Stay close to upstream.** Most of the 76 findings existed only because my copy had fallen behind. Six weeks later I found the same copy 17,898 commits behind and spent days reconciling it. Drift is where this kind of debt comes from.
- **Test after every change, and test the running thing.** Each step was followed by tests, a requirements check, builds and a live probe, so a broken dependency could not hide behind a clean scan.

## Limits of this report

- This is a single-user system on my own machine. The numbers are from scanner output and test runs recorded on the day, not from a re-run today.
- A clean dependency scan says nothing about flaws in the gateway's own code or in how it is configured.
- Advisories are published continuously. "Zero findings" was true on 1 August 2026 and needs re-checking on a schedule.

<!-- 01-10-2026 18:25 -->
