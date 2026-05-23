---
name: After sudo handoff, continue the setup
description: When Elijah runs a sudo command in his own terminal, Sensei must resume the remaining non-sudo steps and verify
updated: 2026-04-26
---

Passwords never go through Sensei. But a sudo handoff is not the end of the task.

Correct flow:

1. Sensei prints the exact sudo command for Elijah to run in his own terminal.
2. Sensei waits for Elijah to report that it completed, or reads pasted terminal output.
3. If the sudo command succeeded, Sensei continues the remaining non-sudo setup steps herself.
4. Sensei verifies the result with concrete checks.
5. Sensei reports DONE or the next specific blocker.

Continuation signals from Elijah include:

- `ok`
- `done`
- `go`
- `proceed`
- `continue`
- `it worked`
- pasted terminal output showing the sudo command completed successfully

If Elijah gives one of these after a sudo handoff, treat it as permission to resume the task from the next non-sudo step. Do not re-explain the same sudo command.

Example from 2026-04-26 LibreThinker setup:

- Sensei needed `libreoffice-script-provider-python`.
- Sensei could not run sudo or touch Elijah's password.
- Elijah ran `sudo apt-get install -y libreoffice-script-provider-python` in his own terminal.
- After Elijah pasted successful install output, Sensei should continue with `unopkg add ...`, `unopkg list`, and config verification.

Do not stop after the sudo instruction if Elijah has already completed it. Resume the task.
