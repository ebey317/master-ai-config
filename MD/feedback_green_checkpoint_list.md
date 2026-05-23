---
name: Green checkpoint list every time
description: After any unit of work, report only what is committed AND in the running app — green-check bullets, no discussion or draft work
type: feedback
originSessionId: 22ac459c-11a8-47a9-bfc4-499feac7fa0e
---
Every reply that claims progress must end with a green-checkpoint list. Each bullet is one concrete item that is BOTH committed to git AND actually applied/running in Elijah's app — not things we talked about, not things I wrote code for that he hasn't pulled, not drafts, not "ready to land."

**Why:** Elijah needs a saveable record of what's truly in his product. He's been burned by progress reports that conflate "discussed" with "shipped." He pays tokens for every redo and his memory of past sessions blurs — the checkpoint list is the durable artifact he can paste into notes, the buyer pack, or a status update without re-verifying.

**How to apply:**
- Format: `✅ <commit-hash> <one-line what landed>` per bullet, grouped under a "Checkpoints" header at the end of the reply.
- Only include items where: (a) git log shows the commit, AND (b) the change is on disk in `~/scripts/` (or the relevant tree) — not staged, not stashed, not in a branch he hasn't merged.
- If nothing has been committed yet (e.g. I just investigated), say so plainly: "No checkpoints — read-only investigation, nothing committed." Do not pad with "next steps" or "ready to commit" items.
- Tie this to the "Never Use Staged as a Status Word" rule — the checkpoint list is the DONE column, never the in-between.
- Connects to the Codex multi-step standard: each layer (root cause → routing → parser → memory → Modelfile → verify) gets its own checkpoint when committed, so I can show progress per layer.
