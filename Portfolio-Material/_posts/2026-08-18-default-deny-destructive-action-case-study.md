---
layout: post
title: "Default-Deny for Destructive Actions: Closing an Unattributed Shutdown Path"
date: 2026-08-18
categories: portfolio-material
tags: [Cybersecurity, AccessControl, DefaultDeny, Authentication, ChangeManagement]
---

## Summary

A Mac in my personal AI-agent infrastructure was powered off by a WhatsApp message, and the proximate cause could not be identified — the agent's own turn provably had not executed anything that could have triggered it. Rather than patch the visible symptom, I traced the enabling condition to a tier-proxy code path reachable from an impersonation surface, closed it structurally, added default-deny source gating with logged caller attribution to the shutdown script itself, repaired an already-degraded halt escalation ladder, and — a day later — caught both fixes nearly being dropped during a large branch merge back into main.

## Design Decisions

- The shutdown script (`power-off.sh`) now requires an explicit `--source=<name>` argument matched against an allowlist; there is no default-allow path for an unrecognized or unspecified source.
- Every invocation attempt records full caller process ancestry, so an unattributed call becomes attributable on its next occurrence even if the current one can't be fully traced.
- `HERMES_CONTEXT.md` was scoped to Telegram/CLI only, narrowing the legitimate surfaces from which a shutdown-capable context can originate in the first place.
- The tier-proxy behavior that made the shutdown path reachable from an impersonation surface was fixed at its source, not patched downstream in the shutdown script alone — closing the actual enabling condition rather than only the symptom.
- The fix explicitly documents what it does **not** close: the sudoers configuration still permits a direct call to `/sbin/shutdown`, which requires a root-owned wrapper and interactive password entry to fully close — stated as open work, not implied as resolved.
- The halt ladder itself was repaired independently: a dead AppleScript fallback (3/3 recorded failures, Apple Events not permitted, 25s wasted) was removed; fixed 20-second waits that caused premature, needless app-killing escalation were replaced with polled 45s/45s/60s waits; sudoers was scoped to exactly `/sbin/shutdown -h now`; and the script was versioned for the first time so a stale or missing checkout can't silently mean "shutdown capability doesn't work."

## Evidence

- Root-cause investigation confirmed the agent's own turn could not have triggered the shutdown: no model response had been generated yet, and no tool had executed — ruling out the most obvious explanation and forcing the investigation to the actual enabling condition instead of a plausible-looking symptom.
- The default-deny source gate and caller-ancestry logging were implemented and verified in the codebase (`power-off.sh`), confirmed present via direct commit inspection.
- One day after the initial fix, a 70-commit-diverged branch merge back into `main` was found to contain both the power-off source gate and the tier-proxy impersonation fix **only on the branch** — `main`'s copy of the tier proxy still had the pre-fix, exploitable behavior. Redeploying from `main` as it stood would have silently re-armed shutdown-by-impersonation.
- The merge was resolved per-file based on which side had actually continued development (verified by commit timestamps and diffs), rather than a blanket "main wins" or "branch wins" policy that could have discarded either the safety fix or unrelated newer work on either side.
- Post-merge verification: 788 tests passing (up from 743 pre-merge, reflecting main's own test suite arriving with it), zero conflict markers remaining in the tree, and explicit confirmation that the impersonation veto was intact in the merged codebase before trusting the result.
- The halt-ladder repair was validated against the specific failure evidence that motivated it: the AppleScript rung's 3/3 recorded failures, and a real halt sequence that had taken longer than the old fixed 20-second wait, triggering unnecessary escalation.

## Security Relevance

- **Default-deny authorization for destructive actions:** rather than trusting that only legitimate contexts would ever call the shutdown script, the fix requires explicit, allow-listed attribution before the action is permitted at all — the correct posture for any irreversible, high-consequence capability.
- **Complete mediation, not point patching:** the fix addressed both the enabling condition (the tier-proxy impersonation gap) and the terminal control point (the shutdown script's own source gate), rather than relying on a single layer to hold.
- **Honest scoping of a fix's limits:** documenting that the sudoers path remains open, rather than allowing the allowlist fix to imply complete closure, keeps the security posture accurately represented rather than overstated.
- **Deployment verification as a distinct claim from "fixed":** the branch-merge near-miss demonstrates that a security fix existing in a commit is not the same claim as a security fix being reachable from what's actually deployed — the two have to be verified separately, and this case shows exactly how a fix can be silently lost between them.
- **Resilience of the safety mechanism itself:** the halt ladder's own fallback path had been silently broken (3/3 failures) prior to this investigation — a safety mechanism that has never been exercised under real failure conditions can degrade without anyone noticing until the moment it's actually needed.

## What I Learned

The most important finding here wasn't the vulnerability itself — it was that "the agent's own turn didn't do this" is a result that should widen an investigation's scope, not narrow it into a quick patch. Fixing the enabling condition at its source, being explicit about what remains unclosed, and then catching the fix nearly disappearing in an unrelated merge the very next day reinforced the same lesson from three different angles in 48 hours: a security control is only as real as its weakest verification step, whether that's attribution at the point of action, honest scope documentation, or confirming the fix actually ships with what's deployed.
