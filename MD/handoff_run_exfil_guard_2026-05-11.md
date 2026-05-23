# Cloud-send guard extended to RUN: output — 2026-05-11

Follow-up to `handoff_cloud_send_guard_2026-05-11.md`. Same leak vector,
different injection path.

## Commit

`c01e4c2 feat(privacy): extend cloud-send guard to RUN: output exfil paths`

Stacked on top of `816af60` (READ-side cloud-send guard).

## What it closes

`RUN: cat ~/Documents/notes.txt` runs the command, captures stdout/stderr,
and feeds the output back into history via:

```
process_reply
  └── _format_tool_result(kind, cmd, result)
        └── tool_result_feedback.append(...)
              └── history.append({"role": "user", "content": ...})
```

The next model turn — potentially cloud — sees that private content.
The READ-side guard didn't cover this path because READ and RUN have
separate dispatch in `process_reply`.

## What landed

- New module-level helper `_check_run_output_for_privacy(kind, cmd, output)`
  in `master_ai.py`. Reuses `_privacy_check_path_or_content` which calls
  `harvest._privacy_reason()` — same source of truth as READ marking and
  the cloud-send gate. No duplicate policy.
- Marks the turn private when EITHER the command string or the first
  4 KB of captured output trips the policy.
- `_format_tool_result` inside `process_reply` calls this helper before
  bundling output for history injection. Yellow `🔒 Privacy` line
  surfaces the match to the user; `ask_cloud` then blocks the re-ask
  the same way it does on READ-marked turns.
- Covers both `RUN` and `RUNTERM` via the `kind` parameter.

## Tests

`test_privacy_cloud_guard.py` now has 20 tests (up from 13). New class:
`RunOutputExfilTests` covering:

- private path in cmd → marked (Documents, Pictures)
- secret value pattern in output → marked (AKIA…)
- private term in output → marked (tax 1099)
- clean cmd + output → NOT marked
- RUNTERM kind also marks
- end-to-end: marked RUN → `ask_cloud` blocks

## Verification

```text
python3 -m py_compile master_ai.py test_privacy_cloud_guard.py      # OK
python3 -m unittest test_privacy_cloud_guard                        # 20 OK
python3 -m unittest test_master_ai_parser                           # 77 OK
python3 -m unittest test_master_ai_safety                           # 55 OK
# total: 152 tests, 0 FAIL, 0 ERROR
```

`agent_standards_score()` still 95/100. No FAIL or new WARN.

## Privacy surface summary (post-c01e4c2)

```text
PRIVATE TURN can now be set by:
    READ: private/path           -> _mark_turn_private  (816af60)
    READ: private content        -> _mark_turn_private  (816af60)
    RUN/RUNTERM: cmd with priv   -> _mark_turn_private  (c01e4c2)
    RUN/RUNTERM: output with priv-> _mark_turn_private  (c01e4c2)

PRIVATE TURN -> ask_cloud blocks pre-dispatch unless _TURN_PRIVATE_APPROVED
            -> handle() resets state on each new user input
            -> ask_local + ask_local_stream still allowed (off-box safe)
```

## Still open (forward-looking)

- **LoRA dataset builder must mirror the same policy before any JSONL
  export.** The builder is not yet implemented (deferred per current
  scope). When it is, route every (prompt, response) candidate through
  `harvest._privacy_reason()` BEFORE writing to the export file, and
  also skip image-meta entries (already enforced in `harvest.record()`).
  Co-locate the export with a small log of skipped entries (counts +
  reasons, no content) so dataset coverage is auditable.
- `privacy_status` Sensei command landed (Step 6 of the prior round)
  as `privacy status` (with a space).
- Broader READ-fence policy for `~/Documents` / `~/Pictures` /
  `~/Downloads` / `~/Desktop` — still allows local reading. The
  cloud-send guard is the protection that matters most for off-box
  leakage; expanding the local fence is a separate decision.
