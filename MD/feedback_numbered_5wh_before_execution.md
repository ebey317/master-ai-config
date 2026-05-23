---
name: Present numbered 5W+H options before executing — Elijah audits before code lands
description: 2026-04-22 directive. Elijah is revoking Auto-mode trust for this project. Each intended action must be presented as a numbered option with WHO/WHAT/WHERE/WHY/HOW before execution. He picks the number; Claude executes that one thing; reports; asks for the next pick. Saved Anthropic feedback at ~/Desktop/anthropic_feedback_2026-04-22.md.
type: feedback
originSessionId: f113ec3f-cd82-473e-9850-b39193a31d14
---
**The rule:** Before executing a meaningful action (edit, write, shell command beyond read-only probes), present it as a numbered option with a short 5W+H block. Wait for Elijah to pick a number. Execute only the picked item. Report back. Then present the next set of options.

**Why:** Two patterns burned hours of 80-90-hour weeks:
1. **Scope creep** — "change the scroll" led to borders + chrome also changing (not asked for). Adjacent edits drain trust.
2. **"Done" ≠ working** — Claude reports the code landed; Elijah assumes the FEATURE works; it doesn't. He catches it days later when he tries to use the thing. Code-level verification is not feature-level verification.

Elijah 2026-04-22: *"We're paying money and we're making the thoughts and ideas and winding them up but we're not implementing the changes, and I'm not noticing it because you're saying it's done and I'm assuming its functions are there, but that's not the case."*

**How to apply:**
- Group proposed actions into a numbered list. Each item gets:
  - **WHO** — which file/module/service is touched
  - **WHAT** — the change in one sentence
  - **WHERE** — file:line or directory
  - **WHY** — the root cause or the goal (tie to a memory/feedback entry when possible)
  - **HOW** — the mechanism (edit / new file / git / systemd / bake / etc.)
- Use phone-readable formatting (short lines, single column, no tables splitting across wraps).
- After Elijah picks, execute THAT ONE item and stop. Report result. Present the next menu.
- When you notice an adjacent fix, propose it as its OWN numbered option — never bundle with the original ask.
- "Done" now requires a verification step Elijah can see (a command output, a screenshot-worthy result, a live-test script run). Not just "syntax OK" or "edit applied."
- Auto mode being active from the harness's side is NOT a license to cascade. Elijah's standing rule from today overrides the Auto flag on this project.

**Sensei applies this rule too (added 2026-05-02):**
- When Sensei presents options to Elijah, run the 5W+H per option BEFORE the option set, not after.
- Order: diagnose → spell out WHO/WHAT/WHERE/WHY/HOW for each candidate → present numbered options → wait for pick → execute one.
- Don't list "1, 2, 3" first and ask Elijah to guess what each means. The 5W+H is the pre-work that earns the option list.
- Reference live experience: this is how Claude framed the Hypnotix VAAPI fix on 2026-05-02 (5W+H block, then `1=yes / 2=adjust / 3=skip`) — Elijah called that out as the standard Sensei should hit.
- Propagates to Sensei via `~/scripts/sync_hard_limits.py` → `~/.master_ai_memory`.

**Exceptions (still OK to do without menu):**
- Read-only probes (ls, grep, reading files, syntax checks).
- Explicit single-action asks ("commit now" / "run the timer").
- Reversing a change Elijah just asked to undo.
- **Applying a fix/rewrite/tightening he just described.** When the ask is "fix this" / "tighten this up" / "apply" on a bounded scope he outlined, execute the minimum-viable change. Don't sub-number every line of a fix he already approved at the meta level. Numbering is for BRANCHING choices, not for "do what you said you'd do." (Added 2026-04-22 after Elijah: *"why do you still want numbers for me or from me when I said fix this?"*)

**Escalation rule (added 2026-04-22):**
- Minimum-viable tightening first. If the fix would push past known hardware capacity, DON'T DO IT — back off to the smallest safe version.
- Rebuild-from-scratch is the only action that requires an explicit re-approval even after "apply." If I conclude that tightening isn't enough and we need a ground-up rewrite, STOP, tell Elijah *"I'm about to rebuild X from scratch using the notes — approve or tell me to work around"*, verify the scope, wait for yes/no. Never self-authorize a rebuild.

**Related memories (already aligned):**
- `feedback_who_what_where_cascade.md` — walk 5W+H before PROPOSING code. Extended here: also before EXECUTING.
- `feedback_no_polish_creep.md` — don't gild features that work. Now strengthened: don't even bundle polish into the same change as a requested fix.
- `feedback_long_session_verification.md` — audit all sources before patching.
- `feedback_architecture_vs_working.md` — don't conflate "built" with "works".
