---
name: Dual-agent sessions use one shared handoff doc in ~/MD
description: In two-agent sessions (Claude + Codex), keep one canonical handoff file in ~/MD named handoff_<topic>.md. Both agents update the same file, use one shared checkoff table, and only move their own rows.
type: feedback
---
When two agents are active in the same workstream, coordination must live in one file in `~/MD/`:

`/home/elijah/MD/handoff_<topic>.md`

**Rule: one doc, both agents, same table**
- Plan-mode artifacts (like `~/.claude/plans/*.md`) are seed drafts only.
- As soon as Plan mode exits, copy the seed into `~/MD/handoff_<topic>.md`.
- From that moment onward, Claude and Codex both update only the same handoff file in `~/MD/`.
- Keep one status/checkoff table in that file for all shared work.

**Ownership discipline**
- Each row has one owner.
- Owner moves only their own row state (`planned -> in-progress -> landed -> verified`).
- One agent does not move the other agent's row.

**Coordination fences**
- Keep restart fences explicit in the same doc (for example: "service restart only after both commits land").
- Log proof checkpoints in the same doc (verification commands, observed outputs, commit IDs when applicable).

**Why this exists**
- Prevents split-brain handoffs across multiple docs.
- Gives Elijah one source of truth for progress and acceptance.
- Preserves clean cross-agent audit history in long sessions.
