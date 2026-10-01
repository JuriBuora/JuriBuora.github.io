---
layout: post
title: "Vulnerability Report: DNS-Rebinding SSRF in a Web-Reading Tool for an AI Agent"
summary: "A formal write-up of a server-side request forgery flaw in a tool I built so an AI agent could read web pages. Four independent review rounds found five ways past its protections. All five are fixed and regression-tested; one transport remains untested."
date: 2026-10-01
categories: reports
tags: [Cybersecurity, Reports, SSRF, DNSRebinding, AppSec, VulnerabilityReport, IndependentReview]
number: 3
---

## Summary in plain language

I built a tool that lets an AI agent open a web page in a real browser and bring back the text, with a citation. A tool like that is a network client that goes wherever it is told. If someone can choose the address, they can point it at things that should never be reachable from outside: other machines on the same network, or a cloud provider's internal metadata service that hands out credentials. That class of flaw is called server-side request forgery (SSRF).

The tool had protections against this from the start. Independent reviews, run in four rounds over two days in July 2026, found **five ways around them**. Each was demonstrated, fixed and given a permanent regression test. The tool was never connected to anything that accepts untrusted input while these were open.

| # | Flaw | Found in | Status |
| --- | --- | --- | --- |
| 1 | Browser resolves the address again after the safety check (DNS rebinding) | Round 1 | Fixed |
| 2 | A trailing dot on a hostname slipped past the text-based checks | Round 1 | Fixed |
| 3 | The first hostname was not re-checked when the browser was pinned | Round 2 | Fixed |
| 4 | Addresses written as numbers skipped the connection-time check entirely | Round 2 | Fixed |
| 5 | WebRTC inside a page could reach the network outside the controls | Round 3 | Fixed |

## Affected component

A Node.js tool that fetches a page in two steps: a preflight request made by Node, which follows redirects and validates every address, then a navigation by a Chromium browser driven through Playwright, so that pages needing JavaScript can be read.

**Exposure at the time.** The tool was not wired into any agent, scheduler or chat channel. It could only be run by hand. This limits the real-world impact of every finding below, and it is the reason the reviews were done first.

## Severity

I rate the issue **high where the tool runs on a server or in a cloud environment**, because a successful attack reads internal services, including credential-bearing metadata endpoints. For the deployment that actually existed, a single-user laptop with the tool unconnected, I rate it **medium**: the flaw was real and demonstrated, but nothing untrusted could reach it.

I have not assigned a numeric score. The rating above is my own reasoning, stated so it can be disagreed with.

## Finding 1: the browser resolves the address a second time

**What was wrong.** The preflight checked which address the hostname resolved to at the moment Node connected. Chromium then looked the hostname up again, independently, when it navigated. Nothing tied the two together.

**How it is exploited.** An attacker controls a domain and its DNS server and answers with a very short lifetime. The first lookup, the one that is checked, returns a harmless public address. The second, moments later, returns an internal address. The check passed; the browser goes somewhere else. Launching a browser takes hundreds of milliseconds, which is ample time.

**Why it was missed.** The implementation's own notes described a "narrow, theoretical" window affecting only secondary requests, and said the main page was safe. The review showed the opposite: the main page was the exposed path, and requests to the same hostname skipped the extra check by design.

**Fix.** The browser is launched with its DNS pinned: each hostname the preflight validated is mapped to the exact address that was checked, and every other hostname resolves to nothing. WebSockets and service workers are blocked. A decision record weighs this against the alternative of proxying all browser traffic through Node, and explains why that was not made the default.

## Finding 2: a trailing dot

**What was wrong.** `localhost.` with a dot at the end is the same host as `localhost`, but the text-based checks compared strings exactly and did not treat them as equal.

**Fix.** Corrected in the address guard during the same review, with a test.

## Finding 3: the fix for finding 1 trusted the first hostname

**What was wrong.** When pinning addresses, the code re-validated every redirect hop but skipped the original hostname, on the reasoning that an earlier step had already approved it. That earlier step had approved the name, not what a fresh lookup of the name returns.

**Demonstration.** A test DNS answered the preflight with an allowed address, then answered the pin-time lookup with a private one. Chromium was launched pinned to a private address, and no block was raised. From the outside it looked like an ordinary timeout. The proof was the captured launch arguments, not the failed connection, because a connection that fails only because a test server is absent proves nothing.

**Fix.** The exception was removed. Every pinned hostname is validated the same way, including the first.

## Finding 4: numeric addresses skipped the check

**What was wrong.** The connection-time safety check was implemented as a custom DNS lookup function. Node only calls a lookup function when there is a name to look up. If the address is already numeric, there is nothing to resolve, so the function, and the check inside it, never ran. The text-based checks did not cover every internal range either.

**Demonstration.** A request to a numeric loopback address, pointed at a local test server standing in for an internal service, succeeded and returned that server's content.

**Why it matters most.** This code had been described in every earlier handoff as accepted and not to be weakened. Its comments said there was no gap between check and use. It predated the change under review, and was found only because the reviewer's brief was the whole network path, not the latest diff.

**Fix.** Numeric addresses are validated explicitly before any connection is made.

## Finding 5: WebRTC

**What was wrong.** A loaded page could use WebRTC to open network connections that did not pass through the pinned DNS or the request filter.

**Fix.** WebRTC is restricted so it cannot make those connections, with a regression test. A follow-up review confirmed that one further variant, relay over TCP, is already stopped by the catch-all DNS rule from finding 1.

## Verification

- Each of the five was reproduced before the fix and shown blocked after it.
- The test suite grew from 144 to 158 tests at the end of round 2, all passing, with further tests added in round 3.
- Reviews were done from a fresh context each time, by a reviewer that had not written the code and was instructed not to trust the implementer's report, the commit messages, or the existing passing tests.

## What is still open

- **Relay over TLS** was not tested. It is a residual risk, not a known flaw.
- **Unattended use is not approved.** A fifth review approved running the tool by hand, by one operator, on trusted input. Connecting it to an agent, a scheduler or a chat channel is a separate decision that needs its own review.

## Timeline

| Date | Event |
| --- | --- |
| 27 Jul 2026 | Tool integrated. Round 1 review: verdict fail. Findings 1 and 2. Finding 2 fixed. |
| 28 Jul 2026 | Finding 1 fixed by pinning the browser's DNS. Round 2 review finds 3 and 4, both fixed. |
| 28 Jul 2026 | Round 3 finds 5, fixed. Round 4 confirms the TCP relay variant is blocked. |
| 28 Jul 2026 | Manual command-line use approved. Unattended use not approved. |

## Lessons

- **A check and the action it protects must use the same resolved address.** Checking a name and then letting something else resolve it again is not a check.
- **"Smaller window" is not "smaller risk".** Rebinding attacks are built for sub-second windows.
- **Twice, work reported as ready for review still had a live bypass in it.** The second reviewer's verdict was "pass, with limitations", worded on purpose as a recommendation and not a closed question.
- **Review the whole path, not the diff.** The most serious finding was in code nobody had touched.
- **A function that makes network requests should validate its own inputs,** not rely on the caller having done so in the right order.

Related reading on this site: the case study in the portfolio, and the lab where I rebuild a small version of the guard and attack it.

<!-- 01-10-2026 18:40 -->
