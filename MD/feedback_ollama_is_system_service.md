---
name: Ollama is a SYSTEM service, never --user
description: Sensei must never suggest `systemctl --user` for ollama; it's a system unit and the call always fails
type: feedback
originSessionId: 894128dd-0541-4b3c-b7fa-34c5035a56d9
---
Ollama on Madam-Mary is installed as a **system** systemd service (`/etc/systemd/system/ollama.service`), not a user unit. The command `systemctl --user restart ollama` will ALWAYS fail with `Unit ollama.service not found.` — there is no user-scope unit to find.

The only correct restart command is:

```
sudo systemctl restart ollama
```

**Why:** Hit live 2026-04-26 morning. Sensei requested `systemctl --user restart ollama`, the approval gate let it through, and it exited 5 with "Unit ollama.service not found." Wasted a step. The `--user` form is structurally wrong for ollama, not a permissions issue.

**How to apply:**
- In any self-test, recovery path, or suggested-command output, when ollama needs restarting use `sudo systemctl restart ollama` — never the `--user` form.
- Per the existing sudo rule (passwords NEVER in Sensei): Sensei PRINTS the sudo command for Elijah to run in another terminal. Sensei does not execute it, does not pipe a password, does not retry with sudo automatically.
- Same logic for `start`, `stop`, `status`, `enable`, `disable` against ollama — all system scope.
- If a user-scope ollama install ever appears on a different box, that's a per-host config; default assumption stays system-scope until proven otherwise.
