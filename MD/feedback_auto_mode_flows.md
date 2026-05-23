---
name: Auto mode flows — hard rules only, queue dormant
description: Sensei/Pupil auto mode should flow like Claude Code's. Hard rules enforce safety (OS floor + password rule + destructive pause); approval queue exists but stays dormant until a real mistake forces it on.
type: feedback
originSessionId: 18640b43-2567-4b48-b53e-15ca1470a5c4
---
Elijah's explicit design call, 2026-04-19 evening.

**The rule:** Sensei's auto mode runs through everything low-risk the same way Claude Code does. Speed is the point. Only these pause:

- `sudo` → always hands off to a separate terminal (hard rule in `.sensei_behavior.md`)
- Blocked patterns (`rm -rf /`, `mkfs`, fork bombs) → always refused
- Destructive patterns in auto mode → pause for 5-button prompt:
  `rm`, `git reset --hard`, `git push --force`, `git clean -f`, `systemctl stop|disable|mask`,
  `drop table|database`, `chmod -R`, `chown -R`, `pkill -9`, `killall -9`, `mkswap`,
  package uninstall verbs (pip/npm/yarn/pnpm/snap/flatpak/apt/gem/cargo), `ollama rm`,
  redirects to `/etc | /usr | /var | /boot`

Everything else in auto mode runs + audits (as `RUN-AUTO` in `~/.master_ai_audit.log`).

**Why:** Elijah is "offline-focused" — usually in front of the machine while the AI works, watching output live. The usual "Sensei prompts on every RUN:" friction drags that flow. He accepts the speed-for-attention tradeoff because HE's watching. When he's away, he stops working with AI anyway. Not a cloud agent running unattended.

His exact line: *"I do want my auto mode to flow like yours. I know it's risky but it's productive. It's less risky with me because I'm gonna be offline focused."*

**How to apply:**

- When adding new agents (extractors, research runners, mesh commands, future auto-pulls) — bake them against THIS model first: destructive = pause, everything else = run.
- Do NOT proactively wire the approval queue (`~/scripts/approval_queue.py`) onto new agents. It's written and dormant. Lives on disk for the day a real mistake forces it on. Adding more friction before that day = over-engineering for a hypothetical.
- When building UX (confirms, banners, hints) — let it go. Show the result after the fact; don't ask before the fact. Exception: destructive patterns ALWAYS pause, always show the command, always wait for yes.
- Audit log is the escape hatch. If something auto-ran that shouldn't have, `~/.master_ai_audit.log` records it. Review after, tighten the destructive list if needed.

**When the queue DOES get turned on:**

- A specific agent caused a specific mistake (e.g., extractor wrote 40 files Elijah didn't want)
- Elijah says something like "that thing that just happened — queue it" or "I want to review before it fires"
- Then wire THAT specific agent's writes through `approval_queue.queue()` and register a handler. Don't blanket-activate.

**Don't argue with this policy.** Elijah named it as load-bearing. If a later session thinks "we should be more careful" and wants to activate the queue proactively, it's wrong. Ask him first.
