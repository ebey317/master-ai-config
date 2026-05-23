# Cloud-send guard for READ-injected private content — 2026-05-11

## Goal

When `READ:` injects file content from a private path, mark the conversation
turn private and block all cloud continuations for that turn unless the user
explicitly approves that exact cloud send (one-shot consume).

Source of truth: `harvest._privacy_reason()` — the same policy that filters
harvest entries and few-shot examples. No duplicate policy in master_ai.py.

## Landed

### Module-level state in `master_ai.py`

- `_TURN_PRIVATE: bool` — current turn carries READ-injected private content
- `_TURN_PRIVATE_REASONS: list[str]` — short reason labels (path + cause)
- `_TURN_PRIVATE_APPROVED: bool` — one-shot approval token

### New helpers in `master_ai.py`

- `_reset_turn_privacy()` — clears all three; called at every `handle()` entry
- `_mark_turn_private(reason)` — flips `_TURN_PRIVATE`, appends reason
- `_is_turn_private()` — predicate, used by `privacy status`
- `_privacy_check_path_or_content(path, content="")` — calls
  `harvest._privacy_reason()`; returns reason string or `""`
- `_approve_cloud_send_once()` — flips `_TURN_PRIVATE_APPROVED` (one-shot)
- `_check_cloud_send_allowed()` → `(ok, reason)`; consumes approval on `ok=True`

### Wiring

- `handle()` entry now calls `_reset_turn_privacy()` so privacy state is
  per user-input turn.
- READ injection in `process_reply()` calls
  `_privacy_check_path_or_content(exp, full_text[:4000])` for files and
  `_privacy_check_path_or_content(exp, "")` for directories. Match → turn
  is marked private and a yellow `🔒 Privacy:` line is printed.
- `ask_cloud()` first checks `_check_cloud_send_allowed()`. Block path
  returns `None` BEFORE provider dispatch, audits `PRIVACY-CLOUD-BLOCK`,
  records a blocked action, prints the reason and the approval hint. The
  gate sits in front of both the named-provider call and the fallback chain.

### REPL commands

- `privacy status` — shows whether the current turn is private, the
  reasons, and whether an approval is pending.
- `privacy approve send` (aliases: `privacy approve`, `privacy ok`) — sets
  the one-shot approval flag. Consumed by the next `ask_cloud` check.
  Reset along with the rest of the privacy state at the next user input.

### TUI wiring (`sensei_tui.py`)

- `privacy status` and `privacy approve send` added to
  `COMMAND_MENU_HINTS` and to the `;` toggle/status group for tab
  completion and the punctuation menu.

## Tests

New file: `~/scripts/test_privacy_cloud_guard.py` — 13 focused tests in 3
classes.

- `TurnPrivacyStateTests` — default/mark/block/approve/reset semantics
  (5 tests).
- `PrivacyPolicySourceOfTruthTests` — same path/content harvest flags
  must flag here too (Pictures, jobseeker, private term, secret pattern,
  clean control) (5 tests).
- `AskCloudGateTests` — gate short-circuits BEFORE `_cloud_allowed` is
  called; not-private falls through; approval consumes one send (3 tests).

## Verification

All gates green on this round:

```text
python3 -m py_compile master_ai.py harvest.py sensei_tui.py \
    test_privacy_cloud_guard.py
                                                              # OK
python3 -m unittest -v test_privacy_cloud_guard               # 13 OK
python3 test_master_ai_parser.py                              # 77 OK
python3 test_master_ai_safety.py                              # 55 OK
python3 -m unittest -q test_harvest_privacy                   #  3 OK
```

Totals: 148 tests, 0 failures, 0 errors. `agent_standards_score()` was
95/100 going in; no FAIL or new WARN introduced by this round.

## Limits / not in scope this round

- RUN output exfiltration is NOT covered. A `cat`/`grep` against a private
  file feeds content back into history just like READ, but RUN doesn't yet
  call the privacy check. Out of scope — user asked for READ-injected only.
- Whole-turn approval (vs. per-send) is NOT offered. The current design is
  one-shot consume: each cloud send needs its own approval. If you want a
  "stay-approved-for-this-turn" mode, that's a follow-up.
- The interactive `confirm_run`-style yes/no prompt at the moment of block
  is NOT wired. Block prints a hint, returns `None`, and lets the orchestrator
  fall back to local. User retries after typing `privacy approve send` in
  the REPL. Picked this shape because it's testable without monkeypatching
  `input()` and because the user is voice-input first.
- A `privacy_status` Sensei command was already on the privacy-handoff
  "Still Needed" list (handoff_privacy_gate_2026-05-11.md). This round
  ships it as `privacy status` (with a space).
- Broader READ-fence policy for `~/Documents`, `~/Pictures`, `~/Downloads`,
  `~/Desktop` is still a follow-up — privacy gate guards cloud send, but
  READ itself still allows the file to enter local-only context.

## Surface summary

```text
PRIVATE TURN
    └── ask_local         : allowed (local-only is the safer fallback)
    └── ask_local_stream  : allowed
    └── ask_cloud         : BLOCKED unless _TURN_PRIVATE_APPROVED
                            (one-shot, consumed)
```

Default behavior on a private turn: orchestrator's existing fallback chain
gracefully degrades cloud → local. Visible to the user via the
🔒 Privacy print at READ time and the BLOCKED pill at cloud time.
