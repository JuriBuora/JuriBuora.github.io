# Cybersecurity Blog Mirror Policy

`Blog/_posts/` and `Labs/_posts/` are canonical. Every new or revised
canonical Markdown file must be mirrored into the vault named exactly
`Obsidian Vault`, resolved each run from
`/Users/juribuora/ai-agent-laptop/config/obsidian-vaults.json`.

- Reconcile before reporting content work complete; include untracked local
  drafts in the source inventory.
- Keep Obsidian copies byte-identical to their canonical source files.
- Reuse the established monthly `z DAILY` Blog archive and the per-lab
  `LABS/.../notes/` structure.
- Create missing mirrors atomically. Never overwrite, move, or delete an
  existing Obsidian note: report a byte-different file as a conflict.
- Do not substitute `Juri Personale`, `RAG`, or `AI Agent Inbox` for the main
  vault.
- Pushing the website remains an explicit, separate action.

15-09-2026 06:49
