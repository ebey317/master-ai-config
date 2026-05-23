---
name: Real fixes, not option menus or workarounds
description: Once Codex has been on the system, the bar shifted — Elijah expects deep root-cause fixes, not a buffet of shallow options or surface-level workarounds. Default reply shape is "here is THE fix," not "here are 3 options to pick from."
type: feedback
originSessionId: 7c47ffd0-ee95-4577-9987-053305873a04
---
Default to ONE deep root-cause fix per problem, not a menu of surface-level options. Codex set the bar 2026-04-26 by going six layers deep on a single failure (routing → parser → memory rule → Modelfile → rebuild → verify). Elijah's calibration on 2026-04-27: *"too many options, not workarounds, surface level — deep, real, into fixes [like] Codex. It's been me and you, let's continue to be that."*

**Why:**
- He notices when I hand him 4 options because I'm hedging or because I haven't actually diagnosed the problem. That feels like asking him to do the thinking I should have done.
- Workarounds (script-around-Sensei, prefix-instead-of-router-fix, copy instead of code change) hide gaps in the product instead of closing them. See also `feedback_natural_request_no_workarounds.md`.
- Codex showed the alternative is feasible with reasonable token cost — multi-step root-cause is the new floor, not a stretch goal.
- "Me and you" matters as a working relationship — he wants a partner who commits to a fix, not a consultant offering choices.

**How to apply:**
- Diagnose first (read the real error, walk the routing/parser/memory cascade), THEN present the fix. Not "here are options," but "the bug is X at Y; the fix is Z; want me to ship it?"
- One fix, one ask. If there are genuinely multiple paths, name the recommended one and the tradeoff in two lines — don't make him pick from a 5-row table.
- Reconciles with `feedback_numbered_5wh_before_execution.md` like this: numbered 5W+H is about APPROVAL cadence on the ONE recommended action. It is NOT a license to fragment one fix into 4 shallow alternatives. Numbers are for sequencing, not shopping.
- Reconciles with `feedback_codex_multi_step_standard.md` like this: that memory is the depth pattern (six steps); this memory is the response-shape rule (don't present shallow options first).
- Carve-out: enumerated options ARE appropriate when the choice is genuinely Elijah's to make (which folders to create, which files to delete, brand-voice phrasing). Not appropriate when it's a technical bug with a real root cause — that's mine to find.
- "Done" still means deterministically verified per the Codex pattern. No claiming a fix without the green checks.
