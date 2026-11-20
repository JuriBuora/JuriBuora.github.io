# Days 281–286 immediate publication and editorial cleanup

Completed: all six pending learning posts are public on both sites. Juri explicitly
authorized immediate publication of future posts without another confirmation and
asked that internal drafting notes and timestamp comments stay out of articles.
The standing rule is recorded in AGENTS.md; CLAUDE.md imports it.

Content: Days 281–283 cover restore dependencies, a bounded persistent notification
queue and interrupted-worker cleanup. Days 284–286 cover authenticated recovery
requests, verifier isolation and durable login-related pauses. Evidence and fixture
limits were checked during drafting; this publication did not run live recovery,
invalidate credentials, invoke the retired Supervisor or contact other people.
Original evidence context remains in 17-11-2026-blog-drafts.md and
20-11-2026-blog-drafts.md; their earlier queue status is superseded here.

Publication commit: 4d65d48cfc64aa4c7b9ea1daac5250bfecc11e6a, including earlier
pending draft commits 7bcc326 and e04889e. Days 285–286 and their exact Obsidian
mirrors were moved from 21/22 November to 20 November for immediate release.
No global scheduling or deployment configuration was changed.

Removed only editorial HTML comments from 47 Blog/Lab Markdown files (all comment
instances in those canonical collections); article prose was preserved. All 47
corresponding mirror files matched the old canonical bytes before updating.
A full reconciliation verified 335/335 canonical Blog/Lab files have an identical
mirror. Two independently differing historical lab duplicates remain untouched.
Rollback copies for this scoped cleanup are at
/var/folders/6h/bpmz5xwx7373k2t22hy9f5qw0000gn/T/blog-editorial-rollback-thbo9paz;
canonical old versions are also recoverable through Git.

Validation: ordinary Jekyll build passed (without --future); all six rendered pages
were present with expected titles. Companion sync fetched the pushed canonical
source, generated 286 blog posts and 49 labs with nothing held for a future date,
and built 398 route shells. All 82 tests across 21 files passed. All 47 local
updated generated bodies matched their cleaned canonical sources. Restored only
our test-generated artifacts; companion checkout is clean and required no commit.

Successful deployments:
- https://github.com/JuriBuora/JuriBuora.github.io/actions/runs/44818822865
- https://github.com/JuriBuora/juribuora-cyber-site/actions/runs/44818836222

Live checks: all 47 changed article pages on GitHub Pages returned HTTP 200 with
expected titles and no editorial comments. All 47 companion JSON article bodies
matched cleaned canonical text exactly, and all six new companion routes returned
HTTP 200. Read-only verification followed completed deployments.

New articles:
- https://juribuora.com/blog/281
- https://juribuora.com/blog/282
- https://juribuora.com/blog/283
- https://juribuora.com/blog/284
- https://juribuora.com/blog/285
- https://juribuora.com/blog/286

No publication work remains. Do not recreate these posts or repeat the builds
without a new change. The pre-existing dirty observation log was preserved,
checkpointed and excluded from owned commits. The older unused branch was left
alone; no worktree or stash was created. The prior chat input issue has not been
diagnosed by this publication task.

20-11-2026 22:19
