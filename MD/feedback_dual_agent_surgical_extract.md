---
name: Dual-agent surgical extract — Claude + Codex on the same scripts repo
description: When both Claude and Codex commit to ~/scripts on top of the in-progress 20-file rework pile, both must use the surgical-extract dance (cp /tmp → checkout HEAD → re-apply the focused edit → commit → restore /tmp) to avoid sweeping unrelated rework into commits.
type: feedback
originSessionId: 7834c79c-e693-4399-af54-dbf4f7da69b5
---
Both agents work on the same `~/scripts` repo concurrently. The repo carries a 20-file uncommitted rework pile (master_ai.py + 19 others) at any given time — not committable as a single bundle. Wholesale `git add master_ai.py` from either agent would sweep all the rework into a misleading commit.

**Rule:** every commit from either agent uses the surgical-extract dance:

```
cp /home/elijah/scripts/master_ai.py /tmp/master_ai_full_state.py
git -C /home/elijah/scripts checkout HEAD -- master_ai.py
# re-apply ONLY the focused edits via Edit tool calls
python3 -m py_compile /home/elijah/scripts/master_ai.py
git -C /home/elijah/scripts add master_ai.py
git -C /home/elijah/scripts commit -m "<focused message>"
cp /tmp/master_ai_full_state.py /home/elijah/scripts/master_ai.py
git -C /home/elijah/scripts status --short  # confirm rework files still there
```

**Why:** Elijah's rework is in-progress and not ready to ship as one. Each focused fix (vision misroute, BLOCKED feedback, slicer, weather route) is independently shippable and reviewable. Sweeping everything in violates surgical hygiene and leaves no audit trail.

**How to apply (consequences for design):**
- HEAD line numbers will differ from working-tree line numbers — anchor strings (function defs, comment markers, unique substrings) are stable; line numbers aren't. Always grep for the anchor in the reverted file before each Edit call.
- HEAD function signatures may differ from working tree (e.g. `auto_inject_context(user_text)` in HEAD vs `auto_inject_context(user_text, enabled=True)` in working tree after rework). Adapt the OLD anchor to the actual HEAD content; don't blindly reuse the working-tree anchor.
- The `_LAST_BLOCKED_ACTION` global was added in commit `45f6072` — when checking out HEAD for a NEW commit on top, it's now in HEAD. Order matters; later commits in the same session see earlier-commit additions on revert.

**Validated 2026-05-03:** five Claude commits (82d11d8, 45f6072, 5f75cb3, 94cd4f9, 88614d0) plus one Codex commit (bca5121) stacked cleanly with zero overlap, zero merge conflicts, rework preserved throughout. Codex used the same pattern independently — verified by `git diff --cached --stat` showing one-file commits with focused line counts.

**Trade-offs to know:**
- Parser test (`test_master_ai_parser.py`) sometimes fails on HEAD-only state because it depends on test-side updates that live in the rework. Working tree passes, HEAD-only fails — known artifact, not a fix regression. Re-run on full working tree after the surgical dance to confirm the change itself is clean.
- Per-commit time: ~5-10 minutes of audit + edits + verify + extract dance. Worth it; the alternative is unsurgical commits that can't ship and lose audit trail.
