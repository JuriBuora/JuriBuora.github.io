---
layout: post
title: "Audit Report: Security and Quality Review of My iPhone App for Controlling AI Agents"
summary: "An audit of the iPhone app and the service behind it that let me run AI agents remotely. It found an unauthenticated service, a command filter that could be bypassed, and tasks marked done that were not. All were fixed and re-audited the same week."
date: 2026-07-06
categories: reports
tags: [Cybersecurity, Reports, MobileSecurity, Authentication, CommandInjection, AuditLogging, QualityAssurance]
number: 1
---

## Summary in plain language

I built an iPhone app that sends jobs to AI agents running on my laptop. In early July 2026 I had it audited in three stages: add authentication, run a full security and quality audit, fix what was found, then audit again.

The work found real problems. The service behind the app accepted commands from anything on the network with no login; that was a known, written-down gap, and it was closed first. The audit then showed that the filter meant to stop dangerous commands could be walked around, and that the app marked jobs as "done" when the result was empty or made up.

All of the blocking findings were fixed within the week and confirmed by a second audit, which also found four new, smaller issues introduced by the fixes.

| # | Finding | Severity | Status |
| --- | --- | --- | --- |
| 1 | Service had no authentication | High | Fixed |
| 2 | Command safety filter could be bypassed | High | Fixed |
| 3 | No record of who did what | Medium | Fixed |
| 4 | Jobs marked "done" without checking the result | High (trust) | Fixed, with a side effect |
| 5 | Where to keep the token that controls the laptop | Medium | Decided: secure storage |
| 6 | Features the app implied but did not have | Medium | Fixed or labelled |

## Scope and method

**In scope.** The iPhone app, the web service on the laptop that it talks to, and the local agent the service launches.

**Method.** Each claim in the audit carries one of four labels, so a reader can tell proof from inference:

- **Live:** a real request sent to the running service, with the real response observed.
- **Code:** verified by reading the current source.
- **Visual:** a screenshot from the phone simulator after a clean build.
- **Not tested:** said so explicitly. The first audit could not press buttons automatically, and stated that limit at the top instead of working around it quietly.

One realistic scenario was run end to end both times: asking for a detailed travel plan as a document.

## Findings

### 1. The service had no authentication

**What was wrong.** The service that accepts jobs had no login of any kind. It was reachable only on my private network, and this had been written down as an accepted gap, but anything that could reach it could run agents on my laptop.

**Fix (done before the audit).** A pairing token, generated on first start and stored with restricted file permissions. Every request is checked at the three entry points the service has, using a comparison that does not leak timing information. Programs send it as a header. The browser carries it forward after one visit.

**Verified live.** No token: refused. Wrong token: refused. Correct token: accepted. The "refused" page was checked to make sure it does not itself contain the token, which an early version nearly did because every page was built by a helper that adds the token to links.

**Honest limit.** It is one shared secret, not a separate revocable identity per device. The service's own status output says so.

### 2. The command safety filter could be bypassed

**What was wrong.** The agent may run a short list of safe commands. The filter checked only the first word of the command line. A harmless command followed by a separator and anything else passed, because the first word was on the list. I reproduced this before fixing it.

**Fix.** The command line is now split on real shell operators while respecting quotes, and every part must pass the allowlist on its own. General-purpose interpreters may only run a script file that already exists in the project. Inline code is refused.

**Verified.** 19 regression tests, including the exact bypass, plus the legitimate patterns that must keep working.

**Honest limit.** This closes the class of bypass that was found. It is not a full shell parser, and I describe it as meaningfully harder to bypass, not provably safe.

### 3. No record of who did what

**Fix.** Every authenticated request that changes something is written to an append-only log: time, path, where it came from. The log records the shape of the request, never its contents, so it cannot become a place where secrets collect.

### 4. Jobs marked "done" without checking the result

**What was wrong.** In the test scenario, both AI providers returned something useless: one a single sentence of hesitation, the other a claim to have saved a file that did not exist. Both jobs were marked as successful.

**Fix.** A verification step checks that the promised output exists and has substance before a job counts as done.

**Side effect, found by the re-audit.** The second time, the scenario produced a complete, useful plan, as a document and a PDF, and the job was marked "failed" because the check was too narrow. A good answer that looks like a failure is its own trust problem. It was recorded as the top finding of the re-audit.

### 5. Where to keep the token that controls the laptop

**Decision.** The app keeps it in the phone's secure keychain. Everything else in the app used plain settings storage, and following that habit would have been the easy path. This token can control the laptop, so it was treated differently on purpose.

### 6. Features implied but missing

The first audit found no PDF generation anywhere, no email attachments, and no way to save to the desktop, though the app's wording suggested all three. These were built, and the re-audit confirmed real files and a real email with attachments. Capabilities that still do not exist are now shown as planned, not as working.

## The re-audit

Run the same day as the last fixes, on a clean build, with the same scenario.

- Everything from the first audit was confirmed fixed.
- Four new issues were found, all introduced by the fixes: the false "failed" label above, a doubled file-name prefix, a preview that showed file details instead of content, and cosmetic problems in the PDF.

## Lessons

- **"Only on my private network" is not authentication.** It was an accepted gap until it was looked at properly.
- **A filter that checks the first word checks nothing.** Allowlists have to apply to every part of what will run.
- **Label your evidence.** Saying "not tested" at the top is more useful than an audit that quietly covers less than its title.
- **Re-audit after fixing.** The second pass found four problems the fixes created.
- **Done must mean checked,** and the check must be good enough not to fail real work.

## Limits of this report

The audits were of my own app, on a simulator and a local service, in July 2026. The app has changed since. Findings 1 to 3 are security findings; 4 to 6 are quality findings that affect trust.

*Written up on 1 October 2026 from the records made at the time. The date at the top places this report next to the events it describes.*

<!-- 01-10-2026 22:59 -->
