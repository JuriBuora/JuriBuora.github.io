# Days 278–280 publication handoff

Completed: three new cybersecurity learning posts, published on both sites.

- Day 278: validating JPEG inputs before an isolated worker uses them.
- Day 279: preserving image history while bounding each inference request.
- Day 280: testing separate forum-topic sessions and reply routes.

Canonical files: `Blog/_posts/2026-11-12-day-278.md`,
`Blog/_posts/2026-11-13-day-279.md`, `Blog/_posts/2026-11-14-day-280.md`.
Content commit: `628b1c90b1392da91f2402192090c0f05b3a4f13`.

Evidence basis: recorded 12 November native-image, bounded-history, and forum-photo
candidate exercises, checked against implementation/probe code. The posts state
synthetic transport/model limits and do not claim production activation.

Validation: YAML, numbering, dates, privacy scan, whitespace and Jekyll build passed.
A local source-backed companion-site sync built 392 static route shells; all 82
existing tests across 21 files passed. Temporary generated snapshots were restored;
no companion-site source change was required. Its normal workflow refreshed from
published canonical Markdown.

Deployments verified successful:
- https://github.com/JuriBuora/JuriBuora.github.io/actions/runs/43613669303
- https://github.com/JuriBuora/juribuora-cyber-site/actions/runs/43613679766

All three article bodies returned HTTP 200 on both domains, and the companion JSON
snapshots matched the expected day, title and body. Main URLs:
https://juribuora.com/blog/278
https://juribuora.com/blog/279
https://juribuora.com/blog/280

Obsidian: three new byte-identical November Blog mirrors. Full Blog/Lab comparison
found 324 unambiguous identical mirrors; two historical labs each have one canonical
copy and one differing legacy copy. Both legacy cases were preserved. No missing
Lab basename remains. No Lab or portfolio item was created.

The prior ongoing blog chat was readable through the chat-history tool and marked
not loaded; no evidence established the cause of its reported disabled input.
Publishing continued in the new chat. Do not repeat the content or renumber it.

Open: no publication work. The pre-existing modified observation log was preserved
and given a no-new-observations checkpoint; it remains excluded from owned commits.
The old chat's editor issue and historical legacy mirror variants remain separate.

14-11-2026 17:08
