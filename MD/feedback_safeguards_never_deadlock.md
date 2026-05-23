---
name: Safeguards must never deadlock — and never auto-answer
description: Every permission gate in Master AI needs a non-TTY refusal path plus a fail-closed default, AND Claude must never auto-approve on Elijah's behalf. Born from the 2026-04-19 freeze.
type: feedback
originSessionId: aed40948-dd62-4e0b-b731-3a2af0814dac
---
Every permission check, confirm prompt, fence, or gate inside Master AI must define THREE things before it ships:

1. **TTY refusal path** — if `sys.stdin.isatty()` is false, the gate refuses the action (returns None, logs `DENY-NO-TTY`) instead of calling `input()`. `input()` on a pane with no live stdin blocks forever. This is what actually hangs the machine.
2. **Fail-closed default state** — if a globals() / env / config lookup comes back unset, assume the MOST restrictive value (MODE → `"safe"`, permission → deny, mode → plan, etc.). Never treat "unknown" as "permitted."
3. **No auto-approve, no timeout-to-yes** — if the user is away from the terminal, the prompt WAITS. Claude does not answer `1`/`2`/`Enter`/`y` on their behalf. An absent user is not a consenting user. A timeout that defaults to "yes" is a hard rule violation. A timeout that defaults to "no" is also forbidden: Elijah wants to answer every confirmation himself.

**Why:** 2026-04-19 freeze. New permission layer (sudo handoff + CWD fence + audit log at master_ai.py L2664-2820) landed and the reasoning loop ran. Both tmux "brains" (one 4-stage, one 2-stage) hit confirm_run with no live stdin in one pane. `input()` deadlocked. Ollama serializes model calls, so both panes stalled waiting for each other. RAM climbed, system locked, Elijah hit the power button. Elijah's exact framing: "Claude has no permissions, and [they] have no permissions in their conflicting, not knowing what to do as per the code." He was right. The gates had no resolution path.

**How to apply:** Before wiring any new gate — confirm_run, safe_input, sudo handoff, cwd fence, profile switch, mesh auth, anything — write out: (a) what happens if stdin is non-TTY, (b) what happens if the config/MODE/profile is unset, (c) what the fail-closed default IS. If any answer is "block forever" or "assume yes" — it is not safe to ship. Use `_safe_input()` in master_ai.py (around L2686) for any new interactive prompt so the TTY refusal path is consistent.

Related memories:
- `feedback_passwords_other_terminal.md` — sudo always hands off to separate terminal. This rule is the same family: Claude never acts on Elijah's behalf for anything privileged.
- `feedback_local_mode_default.md` — default-to-safest-setting pattern for mode routing.
