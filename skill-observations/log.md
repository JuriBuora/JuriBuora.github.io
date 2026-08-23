# Skill Observation Log

Observations captured during task-oriented work.

**Status key:** OPEN = not yet actioned | ACTIONED (YYYY-MM-DD) = skill updated/created | DECLINED (YYYY-MM-DD) = user decided not to pursue.

---

## 27-07-2026

27-07-2026 16:10

### Observation 1: Keep learning-progress claims scoped to the completed activity

**Status:** OPEN
**Date:** 27-07-2026
**Session context:** Converting a course activity into a public cybersecurity blog post and lab.
**Skill:** New skill candidate: evidence-based learning-content drafting
**Type:** open-source
**Phase/Area:** Source interpretation and claim scoping

**Issue:** Course dashboards can show partial module progress beside a completed activity or exemplar. A summary that treats the whole module as finished would overstate the available evidence.

**Suggested improvement:** Add a drafting check that separates completed activity, completed assessment, module progress, and planned work before writing public learning content.

**Principle:** Describe only the learning evidence directly supported by the source material, and preserve uncertainty when progress indicators are partial.

27-07-2026 16:30

---

## 21-08-2026

### Observation 2: Use the editorial queue, not only Git history, as the drafting cutoff

**Status:** OPEN
**Date:** 21-08-2026
**Session context:** Mining workstation activity for new blog and lab drafts after an older publishing batch was pushed.
**Skill:** New skill candidate: evidence-based learning-content drafting
**Type:** open-source
**Phase/Area:** Duplicate prevention and content sequencing

**Issue:** A source branch can be behind the local editorial queue: future-dated, intentionally uncommitted drafts may already cover recent activity. Using the latest commit as the cutoff would create duplicate posts even though the files are present.

**Suggested improvement:** Before drafting, inventory both tracked and untracked post/lab files, extract titles and topics from the full queue, and use the highest planned day as the editorial cutoff. Then mine only activity that is both later and substantively distinct.

**Principle:** Publication state and editorial state are different. Content deduplication must compare against the whole local queue.

21-08-2026 19:18

---

## 23-08-2026

### Observation 3: Resolve protected knowledge sources by exact scope, not path wording

**Status:** OPEN
**Date:** 23-08-2026
**Session context:** Mining a permitted RAG and Obsidian knowledge base for evidence to support public learning content.
**Skill:** New skill candidate: evidence-based learning-content drafting
**Type:** open-source
**Phase/Area:** Source discovery and privacy boundaries

**Issue:** A broad path heuristic treated an allowed knowledge base as protected because an ancestor directory happened to contain a generic word such as `Documents`. The real boundary was a specifically named private vault, so the heuristic excluded valid evidence.

**Suggested improvement:** Resolve data-access boundaries through exact canonical source roots and explicit protected-resource names. Apply exclusions by path identity, then record unavailable sources honestly rather than inferring privacy from generic path fragments.

**Principle:** Privacy controls should be precise enough to protect the intended source without silently discarding legitimate, authorized evidence.

23-08-2026 12:26
