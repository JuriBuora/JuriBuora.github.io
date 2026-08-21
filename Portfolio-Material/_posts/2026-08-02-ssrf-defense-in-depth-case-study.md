---
layout: post
title: "SSRF Defense in Depth: Building It, Then Breaking It"
date: 2026-08-02
categories: portfolio-material
tags: [Cybersecurity, SSRF, DNSRebinding, AppSec, IndependentReview]
---

## Summary

I built the outbound-fetch guard for an agent tool that reads and cites real web pages, then ran an independent security review against my own fix and found two further, live-confirmed bypasses in it before either was patched. This case study covers the guard's layered design, the two defects the review found, and why "the tests pass" was never treated as the same claim as "the hole is closed."

## Design Decisions

- Policy is a pure, separate layer: allowlist, private/internal address denial, and content-type restriction are decided before any network connection opens, and every decision is recorded as a receipt.
- Address safety is checked against the resolved address, never the URL string — a hostname like `localhost` or a custom internal DNS entry is only safe to reject once it has actually been resolved.
- Redirect handling treats every hop identically, including the first one — an IP-literal original URL gets the same validation as any later, discovered redirect target.
- Chromium's DNS resolution is pinned for the actual fetch, so the address validated at policy-check time is the exact address connected to, closing the gap between "checked" and "connected."
- robots.txt parsing matches directives to their correct `User-agent` group only, with directives preceding any group applying to none — a small correctness detail with real compliance consequences.
- Fetches run in an isolated browser context, separate from any other automation session on the same machine.

## Evidence

- Independent acceptance review of the initial IP-pinning fix found two live-confirmed bypasses: a time-of-check/time-of-use gap where the *original* requested hostname's address was trusted without re-validation, and a redirect-handling gap where IP-literal URLs never triggered DNS-safety logic at all because no DNS lookup occurred for them.
- Both defects were reproduced live — against a real Chromium instance and a real local HTTP server — before either was patched, following the principle that a passing test suite does not prove a bypass is closed if the tests share the code's own trust assumptions.
- Both fixes were proven with dedicated regression tests using a shared, reusable loopback-fixture mock, so future changes to redirect or DNS handling are tested against real HTTP mechanics rather than a simplified stand-in.
- IPv6 pinning was verified live against a real production IPv6-only site, closing a limitation that had previously only been unit-tested.
- TLS certificate validation was confirmed intact throughout — expired, wrong-host, and self-signed certificates were all still correctly rejected, with no certificate-error bypass flag present anywhere in the fetch path.
- Full regression suite: 158/158 passing after the defect fixes (155 prior plus 3 new), with a clean diff-whitespace check.

## Security Relevance

- **Defense in depth:** address safety is enforced at policy-check time, at connection time via DNS pinning, and at every redirect hop — no single layer is the only thing standing between an untrusted URL and an internal address.
- **Time-of-check/time-of-use awareness:** the first defect is a textbook TOCTOU gap, closed by pinning the validated address rather than re-trusting a hostname across two separate resolutions.
- **Complete mediation:** the second defect existed because one code path (IP-literal URLs) never passed through the safety check at all — every reachable path needs the same gate, with no exceptions for inputs that "shouldn't" need it.
- **Adversarial self-review:** both defects were found by treating a fix as something to attack, not something to confirm, and by reproducing the attack live rather than trusting unit tests that encoded the same assumption the code did.

## What I Learned

A security fix is a claim, and claims need proof, not confidence. The most dangerous phrase in this whole exercise was "already validated earlier" — both defects hid behind a version of that reasoning, in two different parts of the same fetch path. Independent review earns its value specifically by refusing to accept that reasoning without reproducing the bypass it implies.
