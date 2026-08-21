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
