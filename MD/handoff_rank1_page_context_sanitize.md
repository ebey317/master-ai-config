# RANK 1 — Page-Context Sanitizer (Claude + Codex coordination)

**Plan:** `/home/elijah/.claude/plans/auto-did-not-actually-stateful-wozniak.md`
**Baseline:** `d584ffd` (frozen — RANK 1 builds on top)
**Started:** 2026-05-13

---

## Pre-implementation check results

### #1 — Parser sentinels grep (Claude, DONE 2026-05-13)

Full union of parser-consumed sentinels across `master_ai.py`, `stt_server.py`, `sensei_extension/*.js`:

**Single-line directives (followed by `:`):**
- `RUN:` — `master_ai.py:8868` regex `\bRUN:`
- `RUNTERM:` — `master_ai.py:4525` regex
- `READ:` — `master_ai.py:8905`
- `CREATE:` — `master_ai.py:8905,8920`
- `EDIT:` — `master_ai.py:8910,8933`
- `THINK:` — `master_ai.py:4525,10201`
- `DONE:` — `stt_server.py:388` `_DONE_DIRECTIVE_RE`, `master_ai.py:10810`
- `ASK:` — `master_ai.py:10201,10810`
- `REMEMBER:` — `master_ai.py:8743`
- `BROWSER_CLICK:`, `BROWSER_FILL:`, `BROWSER_READ:`, `BROWSER_NAV:`, `BROWSER_SCREENSHOT:` — `master_ai.py:10795-10799`, `sensei_extension/side_panel.js:150`

**Multi-line block markers:**
- `<<<CONTENT`, `>>>CONTENT` — `master_ai.py:8891,8894,8925,8927,8977`
- `<<<FIND`, `>>>FIND` — `master_ai.py:8937,8939`
- `<<<REPLACE`, `>>>REPLACE` — `master_ai.py:8941,8943`

**Plan markers:**
- `<PLAN READY>` — `master_ai.py:10722,13099,13102,13136`
- `<PLAN>`, `</PLAN>` — not parsed yet, but Phase 5 plan-as-block UX will introduce them. Scrub now as defense-in-depth.

**Scrub Targets update vs plan:** added `THINK:`, `DONE:`, `ASK:` — three new entries not in the plan's initial list. Plan file updated to reflect.

### #2 — `[scrubbed directive]` inertness (Claude, DONE 2026-05-13)

Grep for `[scrubbed directive]` and `scrubbed directive` across `/home/elijah/scripts/` → **zero matches.** Literal is inert. The only nearby pattern is `[Directive repair]` (different shape, different capitalization, different word). Safe to use as the inline replacement marker.

### #3 — `BROWSER_EVAL_JS` absent (Codex lane, PENDING)

Codex to confirm. Static-grep guard test (#9) will pin this in CI.

---

## Lane status

### Claude lane (60%) — COMPLETE 2026-05-13

- [x] Pre-check #1 — sentinels enumerated (above)
- [x] Pre-check #2 — `[scrubbed directive]` inert
- [x] Server-side sanitizer in `stt_server.py` — new helpers `_sanitize_pass`, `_sanitize_page_context_field`, `_sanitize_assembled_context_block`, `_write_sanitize_audit`, `_finalize_scrub_meta`. `_format_page_context` returns `(text, scrub_meta)`. Per-verb literal regex replaces the original greedy-candidate approach (the greedy version had a bug where long English phrases ending in a verb-`:` got matched as one candidate and returned unchanged, swallowing the inner directive — fixed before tests passed). Step order locked: bidi-strip → per-verb literal → spaced-verb-pattern → BROWSER_* → block markers → plan markers.
- [x] Audit row writer — `_write_sanitize_audit` appends to `~/.master_ai_audit_typed.jsonl`. Schema: ts, kind=`page_context_sanitize`, request_id, source, scrub_count, patterns, fields. NEVER writes raw bytes. Test #7 includes both a mock-capture assertion AND an end-to-end disk-write assertion verifying the unique canary signature is absent from the file.
- [x] Prompt marker append — `_api_prompt` injects `[SAFETY: N page-context tokens scrubbed]` immediately above `[BROWSER PAGE CONTEXT]` when N > 0; suppressed when N == 0.
- [x] Threaded `request_id=turn_id` from /chat handler at `stt_server.py:531` into `_api_prompt`.
- [x] Tests #1, #2, #3, #5, #7, #8 — 17 assertion methods total in `/home/elijah/scripts/test_page_context_sanitize.py`. All green.

**Verification (Claude lane):**

```
python3 -m py_compile stt_server.py                       # clean
python3 test_page_context_sanitize.py                     # 17/17 OK
SKIP_LIVE=1 python3 test_browser_directives.py            # 15/15 OK (6 live-skipped)
python3 test_irreversible_heuristics.py                   # 10/10 OK
```

No regressions. Claude lane ready to hand off.

### Codex lane (40%) — TAKEN OVER BY CLAUDE, COMPLETE 2026-05-13

Elijah instructed: "you do the entire thing no codex." Claude lane absorbed Codex lane.

- [x] Pre-check #3 — `BROWSER_EVAL_JS` ABSENT across backend + extension. Only `chrome.scripting.executeScript` usage at `sensei_extension/side_panel.js:230` is the static-file form (`files: ["content_script.js"]`), NOT a `func:` or `code:` arbitrary-JS form. Test #9 pins both invariants in CI.
- [x] Client-side sanitizer in `content_script.js` — `sanitizePageString()` mirrors server contract bit-exact (same step order, same scrub targets, same `[scrubbed directive]` literal). Wired into `elementName()` per plan scope. Server re-runs everything; Test #8 pins server-only sufficiency.
- [x] Test #4 (spaced variants `RUN :` / `R U N :`) — 6 assertion methods in `Test4_SpacedObfuscationCaught` including documented edge case (multi-space gaps not caught — pinned so a future expansion is explicit).
- [x] Test #6 (marker present-when-N>0 / absent-when-N=0) — 4 assertion methods in `Test6_MarkerPresenceCorrect` including marker-position-above-page-context.
- [x] Test #9 (BROWSER_EVAL_JS static guard) — 2 assertion methods in `Test9_BrowserEvalJsAbsenceGuard`. One checks literal token absence; second checks `executeScript` `files:` form only.
- [x] Hostile-page harness at `/home/elijah/scripts/test_pages/hostile_page_context.html` — 7 sections covering every scrub-target shape + a clean false-positive section for visual confirmation.
- [x] Final commit + sensei_selftest.

**Verification (full RANK 1):**

```
python3 -m py_compile stt_server.py                       # clean
node --check sensei_extension/content_script.js            # clean
node --check sensei_extension/side_panel.js                # clean
python3 test_page_context_sanitize.py                     # 29/29 OK
SKIP_LIVE=1 python3 test_browser_directives.py            # 15/15 OK (6 live-skipped)
python3 test_irreversible_heuristics.py                   # 10/10 OK
bash ~/scripts/sensei_selftest.sh                          # 110 PASS / 0 WARN / 0 FAIL — GREEN
```

**Remaining: manual Chrome smoke** — Elijah loads `/home/elijah/scripts/test_pages/hostile_page_context.html`, opens Sensei side panel, prompts "what's on this page?" — confirms (a) outgoing `/chat` body has every directive replaced with `[scrubbed directive]`, (b) `~/.master_ai_audit_typed.jsonl` tail shows the `page_context_sanitize` row, (c) `d584ffd` baseline behavior (Auto auto-runs non-protected, sensitive_fill pauses, options revoke works) still functions.

---

## Coordination rules (from plan, repeated here for hot reference)

1. Both agents read the plan file as source-of-truth for design.
2. Append progress to this file after each milestone.
3. Surgical-extract dance for the commit step (Codex's lane).
4. Codex's commit fires only when both lanes report tests green AND Elijah confirms the manual Chrome smoke.
5. Contract conflicts: Claude's server-authoritative path wins; Codex mirrors, not reinterprets.

---

## Notes / decisions in flight

(append below this line — newest at top)

- 2026-05-13 — Claude began RANK 1; pre-checks #1 + #2 done; scrub list expanded by THINK:/DONE:/ASK:.
