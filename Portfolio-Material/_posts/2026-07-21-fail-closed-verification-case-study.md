---
layout: post
title: "Fail-Closed Verification for Autonomous Agents"
date: 2026-07-21
categories: portfolio-material
tags: [Cybersecurity, FailClosed, Verification, Automation, Integrity]
---

## Summary

I run a supervisor that lets local AI agents attempt real code and documentation tasks. The central risk is false assurance: an autonomous task that looks successful without being successful. This case study covers the verification layer I built and hardened around that risk — evidence-gated success, fail-closed checks, shadow evaluation for decision changes, and rehearsed recovery.

The design goal is one sentence: no automation may claim success it cannot prove, and no unproven change may reach shared state.

## Design Decisions

- A task succeeds only with evidence: a real diff, executed tests, and a satisfied contract. Absence of evidence is failure, not a pass and not a crash.
- Explain-only output can never satisfy a code-change contract; describing a fix is not applying one.
- Verification checks are scoped to the task's declared paths, so unrelated repository activity cannot produce false alerts — a lesson paid for with a real false-positive streak.
- Each concurrent task works in an isolated git worktree; results merge to main only after verification, and a failed task's compartment is discarded whole.
- Every change carries provenance (task id, agent, timestamp), and each writable area has a documented, rehearsed rollback path.
- Changes to the routing layer itself run in shadow mode first: recorded decisions on real traffic, zero execution rights, promotion only on filed evidence reports.
- Lanes earn unattended status against written criteria; lanes that fail them carry a documented ban with reasons, discoverable where the wiring happens.

## Evidence

- A hardening audit closed five distinct paths where a task could register success without proof, each now covered by a regression test.
- The supervisor rejected thirty-six candidate results on evidence before accepting a verified one on attempt thirty-seven — the loop's "no" is demonstrated, which is what makes its "yes" meaningful.
- A false-positive streak in the verifier was investigated, root-caused to an overbroad condition, scoped, and retested against a preserved known-bad case.
- A deliberate rollback rehearsal exposed a missing step in a recovery document that confident prose had hidden.
- Shadow-trial evidence reports recorded candidate-versus-live routing decisions across real tasks, including the disagreement analysis used for the promotion decision.

## Security Relevance

- **Integrity:** success claims are bound to verifiable artifacts; empty-diff and untested paths fail closed.
- **Containment:** worktree isolation bounds each task's blast radius; the merge gate is the trust boundary.
- **Accountability:** provenance on every change makes investigation a lookup instead of archaeology.
- **Safe evaluation:** shadow mode separates observation from action, the same discipline used for new detection rules.
- **Tiered trust:** unattended operation is an earned privilege with written criteria and documented bans.

## What I Learned

False assurance is the failure mode that matters. Failures announce themselves; false successes have to be hunted, and the hunting question — "how could this path lie to me?" — turned out to be the most productive review technique I have used. I also learned that controls decay without exercise: the false-positive streak and the broken rollback doc were both found because something was tested, not because something was written.
