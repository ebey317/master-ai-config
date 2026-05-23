---
name: RESOLVED 2026-04-21 pm — stdin race fix applied
description: Q2-eaten-during-Q1's-confirm bug. Two-channel stdin (_CONFIRM_IQ + _AWAITING_CONFIRM flag) + @_awaiting_confirm decorator on confirm_run/runterm/create/edit — all live on disk (master_ai.py:438/441/3835/3946/4014/4087/6500/6510/6520). Needs `refresh` to pick up in any running Sensei.
type: project
originSessionId: ca31505f-3b42-4554-a5cf-47e13b90690e
---
## STATUS: RESOLVED 2026-04-21 afternoon. Verified on disk 2026-04-22 AM.

## The bug (confirmed 2026-04-21 remote)

User types Q1 → Sensei emits a directive (RUN/CREATE/EDIT/RUNTERM) → confirm prompt asks for 1/2/3/4. User types Q2 while confirm is open → Q2 text was consumed as the confirm answer, not queued as a second question. Result: "one of them is deleted and discontinued" (Elijah's words).

Root cause: all `input()` calls (confirm prompts AND type-ahead queries) shared one stdin channel — the monkey-patched `_tui_input` reading from `_iq`. TUI's `_on_submit` put every Enter-pressed text into `_iq` regardless of context. Confirm prompts won the race because they're the `input()` call currently waiting.

## The fix that landed (live on disk)

Two-channel stdin, decorator-based. `master_ai.py` additions:

1. **Module-level** (lines 438-441):
   - `_CONFIRM_IQ = queue.Queue()`
   - `_AWAITING_CONFIRM = threading.Event()`
   - `@_awaiting_confirm` decorator that sets/clears the flag for a function's lifetime.

2. **Confirm functions wrapped** with `@_awaiting_confirm`:
   - `confirm_run` (line 3835)
   - `confirm_runterm` (line 3946)
   - `confirm_create` (line 4014)
   - `confirm_edit` (line 4087)

3. **TUI plumbing** (lines 6500, 6510, 6520):
   - `_tui_input`: `q = _CONFIRM_IQ if _AWAITING_CONFIRM.is_set() else _iq`
   - `_on_submit`: routes to `_CONFIRM_IQ` when flag is set, else `_iq`
   - prompt_toolkit `check_fn`/`submit_fn` plumbed the same way

## Do NOT re-enable the v1.7.11 worker queue

`master_ai.py:6362-6364` comment still says the queue-in-worker-thread approach was **deliberately reverted in v1.7.11** because *"it raced with interactive RUN/CREATE/EDIT confirmation prompts for stdin. Type-ahead is worth less than reliable directive confirmations."* The current fix is the RIGHT shape: directive safety wins, true type-ahead comes for free via the second channel. Do not resurrect `_QUERY_QUEUE` + `_query_worker`.

## What was learned — save for future similar bugs

- **Edge case to verify live**: if user types text (not 1/2/3/4) during a confirm, current behavior sends it as the confirm answer. The kick-escape helper (`_check_kick_escape`) catches `kick`/`force restart`/`hard restart` → exit 42. **Other words like `refresh`/`cancel`/`mode plan` are NOT yet escaped** — extending the helper is a separate small patch (tracked as open TODO in `project_session_end_2026_04_21.md`).

- **Also noted 2026-04-21 afternoon**: Harvest log showed a RUNTERM round-trip at 15:28:39 using model `qwen2.5:3b` instead of `master-ai`. Separate bug, log and investigate if it recurs — 3B route was supposedly removed at `master_ai.py:1018`.

## Live-test regression steps (run after any Sensei `refresh`)

- In Review mode, type Q1 that forces a directive (e.g. "create a file called test.txt").
- Start typing Q2 while the 1/2/3/4 prompt is open.
- Press 1 to accept Q1. Verify Q1's directive runs, THEN Q2 auto-processes.
- Verify `y/N` sub-prompts inside `confirm_create` (line ~3978 range) also pull from `_CONFIRM_IQ` — they run while `_AWAITING_CONFIRM` is still set, so this should just work.
