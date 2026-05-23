---
name: Drift reminder validated as hero UX
description: Elijah flagged Master AI's drift reminder as a feature Anthropic/Claude Code should learn from — keyword-grounded nudge beats generic "stay on topic"
type: feedback
originSessionId: 13fc3768-2767-4ca3-b806-caf67ad7c071
---
The drift reminder (master_ai.py:740 `_maybe_drift_reminder`, keyword-grounded, fires ~every 3000 chars if recent user messages miss every active project keyword) is one of the Master AI beats Elijah considers more beginner-friendly than Claude Code's current UX. He said "some of my features are more beginner and user-friendly than you. I hope Anthropic can pick up everything."

**Why:** Generic "you're off topic" nags get tuned out. Drift reminder names the exact keywords the user drifted *from*, so the user sees the concrete anchor, not a scold. Keyword-grounding is the reason it works.

**How to apply:**
- When reviewing or suggesting changes to the drift reminder, preserve the keyword-anchor pattern. Don't soften it to vague reminders.
- Same pattern (show the concrete thing the user drifted from) is a reusable UX principle for any future nag/reminder surface in Sensei or Pupil.
- When comparing Master AI's UX to Claude Code's for pitch/positioning material, the drift reminder sits alongside the stoplight handoff, the four-button confirm, and the plan→review color flip as the hero UX beats. Target audience is user→operator onboarding, not a developer audience. That's the differentiator — not "better than," *different target*.
- Don't hype it back to him. He stated it flat; match tone.
