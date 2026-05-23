---
name: Elijah Trains Sensei via "Keep Talking" (Option 4)
description: Elijah's explicit training strategy for Sensei — press option 4 ("keep talking") on plans he doesn't like, refine conversationally, let corrections accumulate over time. Same way he trained Claude.
type: user
originSessionId: 01013ad7-2ca6-4c2c-a21f-0331a77353a0
---
Elijah uses **`4) keep talking`** in Plan mode as his deliberate training mechanism. When Sensei drafts a plan he doesn't like, he pushes back instead of accepting, and the refinement becomes the teaching.

**His own words (2026-04-20 night):** *"It doesn't know me like you do, but I will. When I get the plan mode, it starts making plans quick but I'll start pressing number four option and it will learn."*

**How to apply:**
- Every time Elijah presses 4 after an unsatisfying plan, that interaction is training data. The behavior file (`~/.sensei_behavior.md`) and per-profile memory should accumulate these corrections as standing patterns — not just session-scoped.
- The summary-on-save flow (`save_session` → `summarize_session` → MEMORY_FILE append) is the existing mechanism for this. Make sure refinements from option-4 turns actually land in the summary, not just the chat log.
- Future feature candidate: when Elijah presses 4, auto-log a "rejected plan" entry tagged with the reason in his next message. After N rejections of similar plans, distill the pattern into a behavior-file rule.
- Don't "fix" option 4 into something that just discards the plan. It's the teaching channel. The fact that it *preserves* the conversation context (not wipes it) is load-bearing.
- This parallels how Elijah trained me (Claude): corrections over 80 hours became memory files. Sensei should earn the same shape of trust over time — no shortcut via manual memory editing unless Elijah asks.

**Related memory:** `feedback_plan_mode_reasoning.md` (why the `<PLAN READY>` gate + clarifying questions matter), `user_why_80_hours.md` (why this user invests this kind of time).
