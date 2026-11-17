# Blog drafts — Days 281–283

Target: canonical Jekyll repository, my-blog. Create mode; no push or deployment performed.

Created:
- Blog/_posts/2026-11-15-day-281.md — missing imported module discovered during encrypted memory restore; environment metadata fix; clean-machine rebuild unproven.
- Blog/_posts/2026-11-16-day-282.md — bounded persistent notices, destination binding, retry/watermark limits and uncertain sends.
- Blog/_posts/2026-11-17-day-283.md — interrupted snapshot update, temporary worker lifecycle, parent-death signal and separate cleanup checks.

Evidence inspected read-only in ai-agent-laptop: commits 5e98091e, 9f7e471e, 6a33c42b, 74ff2a7c; their source/tests; handoff/15-11-2026-ubuntu-migration-close.md and handoff/15-11-2026-hermes-ubuntu-closed-for-observation.md. No private message corpus, credentials, live sends, restart or crash drills accessed/performed. Linux semantics checked against https://man7.org/linux/man-pages/man2/PR_SET_PDEATHSIG.2const.html. No new labs: these are evidence-based write-ups, not freshly executed reusable labs.

Validation: full local queue had 280 blogs and 49 labs before changes. New identifiers/dates/privacy scan passed; Jekyll build passed; each of the three generated HTML pages contains its Day and expected sections. git diff --check passed. Build does not establish production publication.

Obsidian: resolved exact named vault from config/obsidian-vaults.json. Historical May witnesses and current November notes confirmed z STUDYING/CYBERSECURITY/z DAILY; copied three notes atomically into 11 November 2026 with no-overwrite links and byte checks. Full reconciliation: 283 Blog + 49 Lab canonical files, all 332 have identical mirrors, zero missing, zero divergent-only sources. Two pre-existing divergent Lab duplicates remain preserved (Lab04 and Lab05); neither was overwritten. No Lab mirrors created.

Git: stage only three new posts and this handoff. Existing modified skill-observations/log.md is excluded; its earlier changes are not owned by this task. No new branch/worktree/stash. Own content commit stays local, unpushed. No companion-site sync or deploy needed for this draft-only batch.

Next step: only if owner explicitly requests publication, verify current branch/state and companion build, push the owned canonical commit(s), then verify both live sites. Do not repeat successful runtime acceptance tests or mutate the source infrastructure merely to support these posts.

17-11-2026 19:43
