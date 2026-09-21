---
layout: post
title: "Defense in Depth for a Physical-Machine Bridge: Home Assistant and the M4 Sleep Control"
date: 2026-09-05
categories: portfolio-material
tags: [Cybersecurity, LeastPrivilege, DefenseInDepth, NetworkSecurity, HomeAutomation]
---

## Summary

Connecting a third-party home-automation platform (Home Assistant) to real power control over a personal machine (an M4 Mac) is a capability-creep risk by default: the obvious path is reusing an existing broad credential. Instead, wake and sleep were treated as two separate trust decisions. Wake got the absolute minimum — a single boolean action with zero new authority added anywhere. Sleep, deliberately deferred to its own build, got a purpose-built, loopback-only bridge service with a fixed command surface, no shell or SSH capability, non-root systemd sandboxing, root-owned secrets, and negative-path verification proving the boundary actually holds under attack conditions, not just under normal use.

## Design Decisions

- Wake and sleep were scoped as two independent capabilities rather than one combined "power control" feature, because they carry different consequences and don't need the same trust level.
- The wake path (`script.wake_m4_mac`) does exactly one thing — `switch.turn_on` on an existing wake-on-LAN entity — with no sleep, shutdown, shell, SSH key, or credential added to Home Assistant at any point.
- The sleep path was explicitly deferred at wake-implementation time with a documented boundary: do not copy the constrained M4 SSH key into the Home Assistant container; build a separate, narrowly allowlisted bridge instead.
- The sleep bridge is bound to `127.0.0.1` only; Home Assistant's container reaches it via host networking, while the Tailnet and LAN cannot reach it at all — an architectural boundary, not a firewall policy that could later be misconfigured.
- The bridge invokes only the existing constrained `/usr/local/bin/mac sleep` argv — no shell, no SSH key, no general command capability — identical in spirit to the wake path's minimalism, just extended to a second, more consequential action.
- The bridge service runs as a non-root user, is systemd-sandboxed, and its token plus the Home Assistant secret are root-owned at mode `0600`, never tracked in version control or logged.

## Evidence

- Deployed Python and unit files were verified by SHA-256 hash against their committed source blobs, and the live `ExecStart` was confirmed to point at the installed release path rather than the working checkout.
- Five targeted tests passed, including mocked timeout/nonzero subprocess handling, HTTP health/rejection controls, and fixed subprocess argv verification (confirming the command invoked can't be influenced by request data).
- Negative-path verification, not just happy-path: a request with no token returned `401`; a request with a non-empty/malformed body returned `400`; a request from a real Tailnet address was refused outright, while Home Assistant's actual loopback health check succeeded.
- The bridge's own service journal was scanned for its authentication token, with no match found — confirming the secret doesn't leak into its own logs.
- The service was restarted with an observed PID change (not just a non-error exit code) and remained enabled and active, with Home Assistant's live API confirming `script.sleep_m4_mac` present and correctly wired.
- No real sleep command was sent during verification, since the M4 was in active use — the first real invocation was deliberately left for a genuinely safe moment rather than forced during testing.

## Security Relevance

- **Capability scoping over convenience:** the easy path — reusing the wake automation's existing credential for sleep too — was explicitly rejected in favor of a narrower, purpose-built path, even though it took longer to build.
- **Architectural network boundaries over policy boundaries:** binding to loopback only makes external reachability structurally impossible, rather than relying on a firewall rule that has to be correctly configured and stay that way.
- **Complete mediation with a minimal action surface:** the bridge exposes exactly one fixed, non-shell command — there is no general execution capability anywhere in the chain from Home Assistant to the machine.
- **Negative-path verification as the real test of a boundary:** confirming `401`/`400`/refused-external-origin behavior is what actually proves a security boundary holds; a passing happy-path test alone proves only that the feature works when used correctly.
- **Deliberate incompleteness over forced completeness:** declining to send a real sleep command during testing, because the machine was in active use, prioritized not disrupting real work over checking a box marked "fully tested."

## What I Learned

The real security decision in this project wasn't any single technical control — it was the choice, made explicitly and in writing before the sleep feature existed, to treat it as needing its own scoped trust rather than inheriting the credential already sitting there from the wake feature. Every technical control that followed (loopback binding, fixed argv, non-root sandboxing, negative-path tests) was downstream of that one decision to say no to the convenient path. Defense in depth is often described as layering technical controls; this project was a reminder that the first and most important layer is often just refusing to reuse more trust than a new capability actually needs.
