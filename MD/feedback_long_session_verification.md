---
name: After 4-5 hours, switch to verify-more / re-check mode
description: Elijah's 2026-04-21 evening feedback after a 12+ hour session: my error rate climbs after 4-5 hours of continuous work. Same bug keeps reproducing because I patch one layer without checking all the other layers. Costs him real money (Claude tokens) for the redo loop. Rule: when the same bug fires more than twice, AUDIT ALL SOURCES before declaring another fix.
type: feedback
originSessionId: ca31505f-3b42-4554-a5cf-47e13b90690e
---
## The rule

After ~4-5 hours of continuous work in a single session, switch from "build mode" to "verify mode." Do not declare fixes; verify them. Specifically:

1. **When the same bug fires more than twice, audit ALL sources before patching another layer.** The reasoning-leak bug today fired 6 times across 12 hours because I kept fixing the Modelfile and never grepped `~/scripts/` for the same buggy pattern in master_ai.py. The buggy LOCAL_DIRECTIVE_HINT at `master_ai.py:1554-1568` was injecting the wrong examples on every turn, undoing every Modelfile fix.

2. **After ANY behavioral edit (Modelfile, .sensei_behavior.md, master_ai.py prompt strings), grep the whole codebase for related patterns BEFORE claiming the fix is in place.** One-line check that would have caught the bug on first attempt: `grep -rn "output comes back here" ~/scripts/`. I never ran it.

3. **When the same bug reproduces post-fix, the fix didn't land — don't keep patching the same layer in different ways.** Stop. Investigate WHY the previous fix didn't work. The answer is almost always: another layer is overriding it.

4. **Verification commands per file type (run AFTER every edit):**
   - Python: `python3 -c "import ast; ast.parse(open('X').read())"`
   - Shell: `bash -n X.sh`
   - Modelfile: `ollama show master-ai --system | head -40` to confirm bake matches on-disk
   - JSON: `python3 -c "import json; json.load(open('X'))"`
   - Cross-codebase: `grep -rn "<pattern>" ~/scripts/` to find every occurrence

## Why

**Why:** Elijah pays for every Claude round-trip. When I fix a bug that doesn't actually fix the bug, he pays for me to be told it's still broken, pays for me to re-investigate, pays for me to write a "real" fix. Today the reasoning-leak fix loop cost ~5 round-trips before the actual root cause (LOCAL_DIRECTIVE_HINT) was found. He explicitly said: *"you cost too much money for a lot of redo."*

**Specific incident (2026-04-21):** Reasoning-leak bug fired 6 times across the day. I edited the Modelfile REASON-BEFORE-EMITTING section twice, trimmed the Modelfile, baked, baked again. Each time I assumed the Modelfile was the source. The actual source was a runtime hint string in master_ai.py that I never grepped for. After fix #5, Elijah finally said *"I called that exit 127 and I also sent you right in the code to not ask for that to frame it a different way here you go again"* — meaning: I'd been told before to fix this and was still missing it.

## How to apply

- After hour 4-5 of a session, mentally flag yourself as "in verify mode."
- When fixing a recurring bug, spend the FIRST tool call on cross-codebase grep, not on the obvious patch.
- When the bug fires for the third time, STOP patching and start auditing.
- Run a verification check after every edit. Even single-line edits.
- Show the verification command output, not just the edit. Elijah needs to SEE the verify, not trust that I did it.

## Long-session calendar context

Elijah works 11-16 hour days. Memory entry `user_why_80_hours.md` already establishes he works through long stretches. So I will be in long-session contexts often. This rule applies any time the session has been running 4+ hours, regardless of whether it's a new day or a continuation.
