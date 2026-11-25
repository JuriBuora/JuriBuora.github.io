# Days 287–288: content delivered, publication blocked by GitHub outage

Juri authorized writing and immediate publication when distinct evidence exists.
Two posts were written, validated, mirrored and pushed in content commit
bf3d88445f35b3752f6659a69ad24e7216469c55 on my-blog. No internal editorial
comments or appended timestamp notes appear in either public article.

- Day 287 (22 November): preserving Telegram photo attachments across machine
  boundaries and delayed confirmation. Source: ai-agent-laptop commits e8f7de4,
  eb95766 and 7bf504e; handoff/22-11-2026-telegram-agent-photo-forwarding.md.
  Implementation and tests were inspected. Recorded installed-path synthetic
  transport evidence is distinguished from actual model interpretation, which
  was not exercised. No runtime restart, message or paid provider call occurred.
- Day 288 (25 November): missing helper files in the deployed service release.
  Source: ai-agent-laptop commits 0db8c45 and dcdc2dc;
  scripts/hermes-live-drift-check.py and its focused unit tests; bounded relevant
  section of handoff/25-11-2026-hermes-telegram-controls-drift-check.md.
  Eleven unit tests were rerun successfully. The recorded 48-file live dry-run
  is distinguished from unattended scheduled execution and owner receipt.

Deduplication included the whole existing queue, notably Day 257 deployed drift
and Day 280 forum sessions. These two posts cover distinct attachment transport
and release completeness. No lab was added merely to pad the batch.

Validation: ordinary rbenv exec bundle exec jekyll build passed in 2.698 seconds;
git diff --check passed. Create-only atomic mirroring placed both exact files
in the resolved Obsidian Vault November daily archive. Full reconciliation
verified all 337 canonical Blog/Lab files have a byte-identical mirror; two
historical differing lab duplicates were preserved.

Deployment is NOT complete. Canonical build succeeded, but deploy returned HTTP
500 creating the Pages deployment. Companion workflow dispatch and canonical
failed-job retry also returned HTTP 500. GitHub Status independently reported a
partial system outage affecting Actions and degraded Pages. Both new canonical
URLs and both companion article JSON URLs returned 404 at the live check.

Failed canonical run:
https://github.com/JuriBuora/JuriBuora.github.io/actions/runs/45651273276
Status source: https://www.githubstatus.com/

Exact continuation after service recovery:
1. Rerun the failed canonical deployment (or let this handoff commit's push build).
2. gh workflow run deploy.yml -R JuriBuora/juribuora-cyber-site --ref main
3. Wait for completed successful runs; verify both canonical article URLs and
   companion /generated/posts/blog/287.json and 288.json. Compare content bytes
   with canonical Markdown bodies and check /blog/287 and /blog/288 return 200.
4. Record actual live success and finish foresight scope blog-nov25 shape 29.

Canonical addresses:
https://juribuora.github.io/blog/2026/11/22/day-287.html
https://juribuora.github.io/blog/2026/11/25/day-288.html
Companion addresses: https://juribuora.com/blog/287 and /blog/288.

Do not recreate posts, rerun source runtime experiments, modify recovery policy,
or overwrite historical mirrors. Source ai-agent-laptop remains read-only.
Inherited skill-observations/log.md changes and older branch were preserved and
excluded from content commits. No new worktree or stash was created.


Companion follow-through: npm run build completed, generating 400 route shells;
all 82 tests across 21 files passed. Both new generated article bodies matched
canonical Markdown exactly and contained no editorial comments. Initial companion
checkout was clean. Normal content sync artifacts were committed and pushed as
90559383014323064c2ded26500245deb90a93cb, triggering deployment run
https://github.com/JuriBuora/juribuora-cyber-site/actions/runs/45651654391
The handoff push also triggered canonical run
https://github.com/JuriBuora/JuriBuora.github.io/actions/runs/45651578785
Final outcome: canonical retry failed creating Pages deployment with HTTP 502.
Companion build and tests succeeded remotely, but its deploy also failed.
Both workloads completed; neither site has verified publication success. All owned content
commits are pushed; companion checkout is clean.


25-11-2026 17:14
