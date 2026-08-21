---
layout: post
title: "Evidence-Gated Tool Evaluation"
date: 2026-07-27
categories: portfolio-material
tags: [Cybersecurity, SupplyChainSecurity, ToolEvaluation, Provenance, LeastPrivilege]
---

## Summary

I evaluated candidate local tools against a simple security rule: a new capability must improve a real task enough to justify the source, permissions, retained state, and maintenance it introduces. The work covered source and revision checks, capability overlap, bounded trials, provenance, and clean removal.

## Design Decisions

- Reviewed source and pinned revisions before exposing a capability to the active workflow.
- Compared every candidate against an existing local, manually verifiable baseline.
- Treated credentials, background services, browser profiles, watchers, and broad filesystem access as separate trust-boundary decisions.
- Kept accepted capabilities local, manually invoked, and narrow in scope.
- Deferred or rejected candidates that did not improve evidence gathering or that added unneeded persistent state.
- Recorded rollback alongside the adoption decision so a trial could be removed without guesswork.

## Evidence

- A bounded HTML extraction wrapper was retained because it accepts one explicit file or URL, applies time and output limits, and separates extracted Markdown from source metadata.
- A codebase-index pilot returned answers quickly but did not reduce the manual source evidence needed to verify them, so its cache and trial artifacts were removed instead of integrated.
- A curated security-skill review retained a small, local defensive allowlist while leaving broad or overlapping capabilities outside the active workflow.
- No trial became a background daemon, browser integration, automatic importer, or credential-bearing service.

## Security Relevance

- **Supply-chain awareness:** source and revision information are part of the decision, not an afterthought.
- **Least privilege:** a tool receives only the access required for a narrowly defined task.
- **Data handling:** explicit inputs and inspectable outputs reduce uncontrolled collection and retention.
- **Change control:** adoption, rejection, and rollback are documented outcomes.
- **Evidence discipline:** fast summaries do not replace traceable source verification.

## What I Learned

The most valuable tool-review result is sometimes a justified refusal. A smaller capability set is easier to explain, audit, update, and remove. The evaluation changed my default question from "what can this tool do?" to "what evidence shows that this tool should be allowed to do it here?"
