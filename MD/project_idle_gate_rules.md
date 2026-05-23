---
name: Idle gate rules — Sensei and Pupil
description: Idle-state primitive used by both Sensei and Pupil. Two load-bearing rules: (1) Sensei and Pupil have INDEPENDENT idle settings that must never leak between the apps, (2) the idle timer DOES NOT accumulate while the app is actively thinking/processing. Affects maintenance window, idle tips, auto mode, any future idle-gated feature.
type: project
originSessionId: 39d12576-f270-4aa3-8989-c741ebeec446
---
## The two rules (Elijah, 2026-04-20)

### Rule 1 — Independent settings, never mixed

Sensei and Pupil each have their own idle settings (timeout, trigger behavior, what fires after idle). These settings are **independent**. Changing one must never silently change the other. If a config file is shared, the values for Sensei and Pupil must be distinct keys, not one shared "idle_timeout."

### Rule 2 — Idle does NOT count during thinking

When Sensei or Pupil is actively processing a user request (model call in flight, tool execution running, response streaming), the idle timer **does not accumulate**. Idle time only counts when the app is truly waiting on the user.

Without this rule:
- Maintenance window could fire mid-computation.
- Idle tips could rotate while the model is mid-thought.
- Auto-mode could interpret "still thinking" as "user idle" and do something stupid.

## How to apply

- Any new idle-gated feature (maintenance window, study-sessions review, background project queue, idle tips) must use the same idle primitive and respect both rules.
- When I touch Sensei or Pupil idle code, I must verify:
  1. The setting I'm changing doesn't share a key with the other app.
  2. The timer pauses when `thinking == true` / equivalent state flag.
- If we ever see a bug where idle fires during work, or where changing Sensei's timeout also moves Pupil's, this rule was violated — find and fix the leak, don't paper over it.

## Verify

Added as a line item in `project_tomorrow_sensei_test.md` section 5 — check current behavior before trusting it.
