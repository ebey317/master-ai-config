---
name: Plan→Review Handoff Color Flip Validated
description: The visible frame-color flip when a plan is accepted and execution starts is load-bearing — Elijah called it "beautiful" 2026-04-21. Under the 04-21 stoplight remap the flip is red→amber (Plan red → Review amber). Preserve the behavior; do not land any future change that breaks the chrome transition.
type: feedback
originSessionId: 01013ad7-2ca6-4c2c-a21f-0331a77353a0
---
The Plan→Review handoff with live frame color flip is validated. When Elijah presses `1` or Enter on a committed plan, the TUI chrome should visibly change color AT THE SAME MOMENT the "handoff: Plan → Review — executing plan..." message prints. That visual IS the product's "handoff moment."

**Color values under the 2026-04-21 remap:**
- Plan (before press) = DEEP RED `#8b1a1a` (stop / drafting)
- Review (after press) = AMBER `#c7761a` (caution / per-step)

So the flip at dispatch is **red → amber**. (Earlier 2026-04-20 build had it as amber → red when the mapping was inverted; the direction inverted with the remap but the "flip happens at dispatch" property is what Elijah validated as beautiful.)

**His exact words 2026-04-21:** *"the plan didn't just move all the way to execute it made me press one in the handoff was beautiful. You did correct that was right. Save that."*

**Two properties that must be preserved together:**
1. **Plan does NOT auto-execute.** The user must press `1` / Enter to dispatch. Never shortcut this into auto-flow from Plan mode — the explicit approval is half the feeling.
2. **Color flip happens at the moment of dispatch.** The frame/header/status/legend all repaint in one frame. The mechanism is `_SENSEI_APP.set_mode("review")` called before `handle(PENDING_PLAN_REQUEST, history)` in the accept branch at `master_ai.py:~5294`. That call is load-bearing.

**How to apply:**
- Never remove the explicit `1`-press step from Plan mode. Even if a future feature wants "one-click plan + execute," don't collapse the handoff moment.
- If future refactors touch `confirm_run()`, the plan-accept branch, or `sensei_tui.py:_build_style()`, preserve: (a) chrome-follows-accent styling (frame/header pick up the mode color), (b) `set_mode()` called BEFORE execution, (c) the "handoff: Plan → Review" message printing alongside the color change.
- Stoplight mapping (`feedback_mode_stoplight_colors.md`) locks the hex values. This memory locks the TRANSITION experience.
- Related: `feedback_plan_mode_reasoning.md` (the reasoning gate that produces the plan being handed off).
