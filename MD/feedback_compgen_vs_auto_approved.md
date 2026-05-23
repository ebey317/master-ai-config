---
name: Auto-approved commands live ONLY in Claude settings.json — not in shell, not in sudoers, not in any system file
description: Three separate approval systems — Claude Code (~/.claude/settings*.json), Codex (harness-only), Sensei/Master AI (~/.master_ai_allowed_commands.json + ~/.master_ai_approved). Never merge them. Disambiguate which system the user means before answering. None of them live in shell functions, /etc/sudoers, or any sudo-gated file. If a path requires sudo to read, it is the wrong target by definition.
type: feedback
originSessionId: 70696e88-5f85-4999-b10b-463752c63aca
---
## Three separate approval systems — never merge them mentally

There are THREE distinct "approval / allowed commands" systems in Elijah's environment. Always name which one before answering.

| System | Files | Read with |
|---|---|---|
| **Claude Code** | `~/.claude/settings.json`, `~/.claude/settings.local.json` (under `permissions.allow`) | `jq '.permissions' <file>` |
| **Codex** | Codex harness's approved-prefix list in the active Codex session (no on-disk file Elijah owns) | Codex CLI / harness only |
| **Sensei / Master AI** | `~/.master_ai_allowed_commands.json` (owned reference + safe starter list) **AND** `~/.master_ai_approved` (runtime exact-command list used by `master_ai.py`) | `cat` / `jq` directly |

Backup of the pre-2026-04-26 noisy combined list lives at `~/.master_ai_approved.backup_2026-04-26_separated` — do not restore from it without Elijah; the cleaned runtime list is intentionally tight.

**Claude Code auto-approved / allowed commands live ONLY in two files:**

- `~/.claude/settings.json` (user-level)
- `~/.claude/settings.local.json` (machine-local)

Both keep the list under `permissions.allow`. Read with `jq '.permissions' <file>`. No other location is correct. This is a Claude Code **harness** concept, not a Linux **system** concept.

**These are NOT Claude Code auto-approved commands** (do not reach for them when asked about Claude permissions):

- Bash shell functions (`compgen -A function`, `type <name>`, `alias`, `compgen -A alias`) — those are shell wrappers loaded by your `.bashrc`.
- `/etc/sudoers`, `/etc/sudoers.d/*` — Linux system sudo permissions, totally different system.
- polkit rules, PAM configs, file capabilities (`getcap`), SELinux/AppArmor profiles — Linux system access control.
- Environment files (`/etc/environment`, `~/.bashrc`, `~/.profile`, `~/.bash_aliases`) — shell setup, not permissions.
- `~/.config/sudo*` — does not exist as a standard path; do not invent one.
- systemd unit ExecStart entries, crontabs — scheduled commands, not approval lists.
- Any path in `/etc/`, `/usr/`, `/var/` — by definition not Claude Code's per-user harness state.

**Sudo-gate disqualifier (compounds with the existing hard rule):** If reading the candidate file requires `sudo` to access, it CANNOT be the answer to "where are Claude Code auto-approved commands?" — Claude Code state is per-user under the user's own `~/.claude/`, never root-owned. Use this as a one-line disqualifier: tempted to read `/etc/sudoers` or anything root-owned? You're on the wrong path. Stop.

**Why:** 2026-04-26 — Sensei twice in one session reached for the wrong target when asked about auto-approved commands: first suggested `compgen -A function` (shell functions), then suggested reading `/etc/sudoers` (which is sudo-gated AND violates `feedback_passwords_other_terminal.md`). Elijah caught both. The correction needs to be broad enough to preempt the entire class of "system permission" wrong-targets, not just the specific two she tried.

**How to apply:**
- **First, disambiguate WHICH system the user means** — Claude Code, Codex, or Sensei/Master AI. The phrasing "my own list" or "Sensei approvals" or "Master AI allowed commands" = Sensei files (`~/.master_ai_allowed_commands.json` + `~/.master_ai_approved`). The phrasing "Claude" or "Claude Code" = `~/.claude/settings*.json`. The phrasing "Codex" = Codex harness only.
- When the user asks about Claude Code auto-approved commands, allowed commands, permissions, or "what can the AI run without asking" — the answer is ONLY: `jq '.permissions' ~/.claude/settings.json` and `jq '.permissions' ~/.claude/settings.local.json`. Show both.
- When the user asks about Sensei / Master AI's own approvals — read `~/.master_ai_allowed_commands.json` (the source-of-truth reference, with metadata) and `~/.master_ai_approved` (the runtime exact-command list `master_ai.py` consumes). Never substitute Claude's or Codex's list.
- Never invoke sudo for this question.
- Never read or suggest reading any file in `/etc/`, `/usr/`, `/var/`, `/root/` for this question.
- Never confuse Claude Code permissions with Linux system permissions, shell functions, aliases, or any other "approval"-shaped concept.
- If the user asks about Linux sudo / system permissions specifically (different question), that's fine — but still never run sudo yourself per the existing hard rule; print the command for Elijah to run in his own terminal.
