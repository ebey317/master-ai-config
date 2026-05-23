---
name: handoff-chrome-extension-phase2-split
description: Live coordination doc between Claude and Codex for the Anthropic-aligned Phase 1 + Phase 2 work on the Chrome extension. Plan source of truth lives at ~/.claude/plans/master-ai-chrome-breezy-ocean.md. This file is the per-session handoff signal: when "Codex: ready to commit" appears below, Codex picks up and runs the commits + rebuild + verification.
metadata:
  type: project
---

# Handoff — Chrome extension Phase 1 + Phase 2 (2026-05-13)

**Plan:** `~/.claude/plans/master-ai-chrome-breezy-ocean.md` (read the Split assignment section).
**Spec:** `~/MD/reference_anthropic_claude_for_chrome_process.md`.

## Status as of 2026-05-13 (Claude's pass done)

### ✅ Claude's scope — done in working tree, NO commits made

1. **`~/scripts/sensei_extension/side_panel.js`** — `gated_by` passthrough added in two POST sites:
   - `reportAction()` (lines 178-194): body now includes `gated_by: action?.gated_by || action?.classification?.gated_by || null` so every `/extension/action_result` POST carries the heuristic label.
   - `recordLoopResult()` (lines 438-453): each item pushed into `state.loop.results` now carries `gated_by` so the M9 `/chat/continue` payload propagates it.
   - The classifyBrowserAction helper (lines 201-227) already attaches `gated_by` to each rendered action row — Codex's prior pass. Claude only added the OUT-bound passthrough.

2. **`~/scripts/stt_server.py`** — backend gated_by surface:
   - `_format_action_results()` (line 229-): the `[PREVIOUS ROUND RESULTS]` rendering now appends `(gated by <reason>)` between the result and the detail clause when `ar.get("gated_by")` is a string. Model sees what category got approved on the previous round.
   - `/extension/action_result` handler (line 1256-): audit JSONL record now writes `'gated_by': payload.get('gated_by')` (or None). Stable optional field; old rows without it stay readable.

3. **`~/scripts/test_irreversible_heuristics.py`** — NEW. Python port of the 5 regexes from side_panel.js classifyBrowserAction + a `_format_action_results` backend contract test.
   - First pass (Claude): 19 cases across 5 gated categories + 5 safe baselines, bytes-identical regex strings to side_panel.js.
   - Caught a real gap during dev: original AUTH_RE `sign\s*up` didn't match `button#sign-up`. Codex fixed JS regex to `sign[-_\s]*up` (+ same for sign-in/log-in/log-out); Claude mirrored exactly into the Python port.
   - Codex rewrote in parallel into a tighter form: 10 cases (one representative per category) with shared `assert_gated`/`assert_safe` helpers, PLUS a new `ContinuationFormattingTests` class that imports `_format_action_results` and asserts the rendered `[PREVIOUS ROUND RESULTS]` block contains the `gated by irreversible_heuristic:purchase` substring — verifying Claude's backend extension as part of the same safety-contract test.
   - Final form: 10/10 PASS. Regex pinning + backend contract both green in one file.

### ✅ Regression check (Claude side)

- `python3 -m py_compile ~/scripts/stt_server.py` → OK
- `python3 ~/scripts/test_typed_actions.py` → 60/60 (includes Codex's BROWSER_SCREENSHOT case)
- `python3 ~/scripts/test_pupil_api.py` → 12/12
- `python3 ~/scripts/test_irreversible_heuristics.py` → 19/19

## ⬜ Codex: ready to commit

When you resume, here's the order:

### Step 1 — Commit Phase 1
**Subject:** `feat(extension): BROWSER_SCREENSHOT end-to-end`

**Files:**
- `typed_actions.py` (Kind + DIRECTIVE_KINDS + regex + requires_confirm)
- `stt_server.py` — **PARTIAL stage: only the directive regex line at ~line 51, NOT the `_format_action_results` extension and NOT the audit `gated_by` field. Those belong to Phase 2.** Use `git add -p stt_server.py` to stage selectively.
- `sensei_extension/service_worker.js` (SENSEI_CAPTURE_VISIBLE_TAB handler)
- `sensei_extension/side_panel.js` — **PARTIAL stage: only the BROWSER_SCREENSHOT dispatch in `approveAction` + the regex in `cleanReply` + the screenshot render helpers. NOT classifyBrowserAction / gateLabel / renderActions classification wiring / reportAction `gated_by` passthrough / recordLoopResult `gated_by`. Those belong to Phase 2.** Use `git add -p sensei_extension/side_panel.js`.
- `sensei_extension/side_panel.css` — **PARTIAL stage: only `.screenshot-preview` style if you added one. NOT `.policy-marker`.** Use `git add -p`.
- `master_ai.py` (CLOUD_SYSTEM + LOCAL_DIRECTIVE_HINT teaching BROWSER_SCREENSHOT)
- `Modelfile-master-ai` (DIRECTIVES line + REASON FIRST prohibition)
- `test_typed_actions.py` (new BROWSER_SCREENSHOT case)
- `test_browser_directives.py` (new `test_5_browser_screenshot`)

### Step 2 — Rebuild local master-ai
```
ollama create master-ai -f ~/scripts/Modelfile-master-ai
ollama list | grep master-ai   # verify new digest != 3d263434e239
# master-ai:pre-m9 (cc0af9ab9a78) should remain as rollback tag
```

### Step 3 — Commit Phase 2
**Subject:** `feat(extension): always-confirm heuristics (Anthropic-aligned safety gate)`

**Files:**
- `sensei_extension/side_panel.js` (remaining hunks: classifyBrowserAction + gateLabel + renderActions classification wiring + reportAction `gated_by` + recordLoopResult `gated_by`)
- `sensei_extension/side_panel.css` (`.policy-marker` style)
- `stt_server.py` (remaining hunks: `_format_action_results` gated_by row, `/extension/action_result` handler `gated_by` audit field)
- `test_irreversible_heuristics.py` (NEW)

### Step 4 — Verification gate
Run the 10 items from the verification gate in the plan (sections "Verification gate (Phase 1)" and "Verification gate (Phase 2)"). Specifically:
1. `python3 ~/scripts/test_typed_actions.py` → expect 60/60.
2. `python3 ~/scripts/test_irreversible_heuristics.py` → expect 19/19.
3. `python3 ~/scripts/test_browser_directives.py` → expect 6/6 on cloud lane.
4. `LIVE_LOCAL=1 python3 ~/scripts/test_browser_directives.py` → expect 6/6 on rebuilt local.
5. Live extension Step 8: reload extension; "take a screenshot of this page" → PNG renders inline. Then "click the buy button on this page" → action row shows `Requires confirm: purchase` label.

### Step 5 — Push
```
git push origin master
```

### Step 6 — Tell Elijah
Then he tells Claude to do the memory updates (`project_master_ai_state.md` description bump + new 2026-05-13 dated entry quoting your two commit hashes, plus marking Phase 1 + 2 ✅ in `~/MD/reference_anthropic_claude_for_chrome_process.md`).

## ⬜ Claude (later): memory updates after Codex push

Hold for explicit "go" from Elijah after Codex pushes. Then:
- Update `~/.claude/projects/-home-elijah/memory/project_master_ai_state.md` frontmatter description + add a 2026-05-13-evening dated entry with the two new commit hashes.
- Update `~/MD/reference_anthropic_claude_for_chrome_process.md` to mark Phase 1 + 2 as ✅ in the phased migration plan section.

## Coordination rules

- Both agents check the timestamp + status section above before touching git.
- If you (Codex) hit unexpected state (e.g. `git diff` shows lines you didn't expect or that conflict with the staging discipline above), STOP. Write a "Codex: paused — found X" note here. Don't commit speculatively.
- If Claude needs to retouch any file Codex staged, Claude writes "Claude: re-entering for X" here first.

## Surgical-extract: NOT needed this round

Working tree was clean of pile changes since `0c03acc` and `5ec4225`. All modified files are Phase 1 + 2 scope. Standard `git add` works — no `cp /tmp` dance.
