---
name: Hard Limits README Reference
description: 2026-04-25 locked rules doc for sudo, Auto mode boundaries, reasoning levels, and the finite sudo allowlist.
type: project
---

# Hard Limits README Reference

Read this first for sudo / Auto-mode safety:

```text
~/scripts/HARD_LIMITS_README.md
```

Related allowlist:

```text
~/scripts/SUDO_MAP.md
```

Core locked rule:

- Auto mode may run safe user-level work.
- Auto mode may hit sudo, print the exact command/script, pause, and wait.
- Elijah runs sudo in a separate terminal.
- After Elijah returns and presses Enter or types `ok`, Sensei may continue
  with non-sudo verification.
- Sensei never runs sudo.
- Sensei never asks for, stores, pipes, echoes, or reuses passwords.
- Sensei never auto-confirms a root action.
- Any sudo workflow outside `SUDO_MAP.md` is a product bug until reviewed and
  documented.

This is a product safety boundary, not a style preference.

