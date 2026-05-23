---
name: anthropic-claude-for-chrome-process
description: Canonical reference for the permission + plan-and-approve process Anthropic ships in Claude for Chrome. Master AI's Chrome extension should MIRROR this pattern, not invent a parallel one. Use as the spec for the extension UX refactor (mode dropdown, plan-as-block approval, site-level permissions, always-confirm list).
metadata:
  type: reference
---

# Anthropic Claude for Chrome — Permission & Plan Process

**Why this exists:** Elijah's directive 2026-05-13 — "why is this not universal language like yours and codex" + "didn't you read anthropic's extension information." Master AI's Chrome extension should mirror Anthropic's published process for browser automation, not invent its own parallel UX. This doc is the canonical spec for the refactor.

## Anthropic's process (as documented)

### Two permission modes (extension dropdown on the chat input)

1. **Ask before acting** — Claude builds a plan and asks for approval before execution.
2. **Act without asking** — Claude takes actions without asking. Flagged in the docs as a **high-risk mode**.

These are the ONLY two modes. The dropdown lives on the chat input, not in a separate settings page.

### Plan-and-approve flow (Ask Before Acting)

- Claude reads the user's prompt and proposes a multi-step plan.
- The plan specifies **which websites it will access** and **the approach it will follow**.
- The user reviews the plan and either approves or asks for modifications.
- Once approved, Claude operates **within those parameters independently** — it does NOT ask per-action.
- BUT: Claude must still pause for irreversible actions even within an approved plan.

### Site-level permissions (chrome.storage)

- First time Claude touches a site: a permission prompt appears.
- Three options: **Allow once** (safest) / **Always allow** / **Decline**.
- Persisted under "Your approved sites" in extension settings.
- Sites with "Always allow" status flow through without re-prompting.

### Always-confirm list (regardless of mode)

Even in "Act without asking," Claude requires explicit user confirmation for:

- Financial transactions / purchases
- Permanent file or data deletion
- Permission modifications
- Account creation
- Authorization grants
- Sensitive information input (login forms, personal data fields)

### Routine actions (auto-execute within approved plan + approved site)

- DOM clicks on non-sensitive elements
- Navigation within approved sites
- Reading page content
- Filling form fields that aren't sensitive (search boxes, comment boxes, etc.)

## Master AI's current state (2026-05-13, commit 2d1ec0d)

| Aspect | Master AI today | Anthropic |
|---|---|---|
| Modes | plan / review / auto (3) | Ask Before / Act Without (2) |
| Mode location | Persisted via MODE_FILE; Shift-Tab cycles | Dropdown on chat input |
| Approval shape | Per-action Approve button always (every kind, every mode) | Per-plan once, then flow within plan |
| Site permissions | None | First-touch + persisted allow list |
| Irreversible-action gate | None | Hardcoded list, always gates |
| Screenshot capability | Not wired (BROWSER_SCREENSHOT proposed but not implemented) | Used implicitly to "see" pages |
| Browser directives | BROWSER_CLICK / FILL / READ / NAV | (Internal, not exposed as directives) |

The current extension always renders Approve + Reject buttons regardless of `state.config.mode`. The mode selector in `side_panel.js:49` exists in the UI but has no effect on whether approval is required.

## Gap analysis — what's missing for Anthropic-parity

1. **Plan-as-block UX in Review mode** (the bulk-approve pattern). The side panel should render the model's full action list with a single Approve-All button, not per-action Approve. After approval, actions flow.
2. **Auto-flow in Auto mode** for routine actions. Read-only and non-sensitive interactions execute without click; sensitive ones still gate.
3. **Site-level permission prompt** on first BROWSER_NAV to a domain. Persist allow list in `chrome.storage`.
4. **Always-confirm heuristics** that trigger regardless of mode for:
   - Buttons with text matching `/buy|purchase|pay|checkout|order/i`
   - Buttons with text matching `/delete|remove|destroy|uninstall|cancel.*account/i`
   - Form fields with `type="password"` or `name` matching `/password|ssn|credit.*card|cvv|api.*key/i`
   - URLs matching financial / authentication patterns
5. **BROWSER_SCREENSHOT capability** so the model can capture the visible tab as PNG (Anthropic uses this implicitly; Master AI should expose it as a directive).
6. **Plan rendering format** — model needs to emit a recognizable "plan block" the side panel can render distinctly from per-turn directives. Possible format: a `<PLAN>` ... `</PLAN>` block listing sites + steps.

## Mapping Master AI's 3 modes to Anthropic's 2

- **plan** (RED) — propose plan only, NO execution even after approval. Master AI-specific "thinking" mode that Claude for Chrome doesn't have. Keep this.
- **review** (AMBER) — Anthropic's "Ask before acting." Show plan as block, approve once, flow within plan with irreversible-action gates.
- **auto** (GREEN) — Anthropic's "Act without asking." Flow through immediately; only the hardcoded irreversible-action list still gates.

This is a 3 → 2 + 1 mapping, not a 3 → 2 collapse. Master AI keeps the pure-thinking `plan` mode as a step beyond what Anthropic ships.

## Phased migration plan

**Phase 1 — Capability parity:** ✅ **SHIPPED 2026-05-13 (commit `8eea451`).**
- BROWSER_SCREENSHOT kind registered in typed_actions.py + stt_server.py regex; chrome.tabs.captureVisibleTab wired through service_worker (MV3 pattern); side_panel.js dispatches and renders inline; CLOUD_SYSTEM + LOCAL_DIRECTIVE_HINT + Modelfile-master-ai teach it. master-ai rebuilt to `8ed06cc2832f` (rollback `master-ai:pre-m9` = `cc0af9ab9a78`).
- Test: `test_browser_directives.test_5_browser_screenshot` green on cloud + LIVE_LOCAL.

**Phase 2 — Always-confirm heuristics (works in all modes):** ✅ **SHIPPED 2026-05-13 (commit `02c47c0`).**
- `classifyBrowserAction(action)` in side_panel.js returns `{safe, requires_confirm, gated_by}` from five regex categories (PURCHASE / DELETE / AUTH / SENSITIVE_FILL / PURCHASE_URL).
- `gated_by` propagates through `reportAction` POST body, `recordLoopResult` M9 continuation, `/extension/action_result` audit JSONL, and `_format_action_results` `[PREVIOUS ROUND RESULTS]` rendering (model sees the safety category that got approved last round).
- Test: `test_irreversible_heuristics.py` 10/10 (including `ContinuationFormattingTests` backend contract assertion).

**Phase 2 — original spec preserved below for reference:**
- Add irreversible-action detector to `side_panel.js` that runs before any Approve auto-trigger.
- Heuristics over `action.target` (selector text, URL substring).
- Even in Auto, these gate.
- Audit log: `extension_action_result` rows get `gated_by="irreversible_heuristic"` for these.

**Phase 3 — Auto-mode flow-through for safe actions:**
- When `state.config.mode === "auto"` AND the action ISN'T flagged by irreversible-heuristic AND the target site is in approved-sites allow list: auto-fire `approveAction(action, row)` without rendering buttons.
- Other actions in Auto still render Approve.

**Phase 4 — Site-level permissions:**
- On any BROWSER_NAV (or first action against a domain not in allow list): render a permission prompt (Allow once / Always / Decline).
- Persist Always-allow domains in `chrome.storage.local`.
- Settings page exposes the list for editing.

**Phase 5 — Plan-as-block UX (Review mode):**
- Model emits `<PLAN>` block listing sites + step-by-step actions.
- Side panel renders the plan distinctly with ONE Approve-All button.
- On approval, actions flow with irreversible-action gates (Phase 2).

**Phase 6 — Mode-name alignment:**
- Optional rename of "review" → "Ask before acting" and "auto" → "Act without asking" in user-facing copy (Sensei TUI legend, extension dropdown, status bar).
- Internal code keeps `plan / review / auto`.

## What NOT to copy from Anthropic

- The 2-mode collapse (Master AI keeps `plan` as a third pure-thinking mode — that's a Master AI differentiator).
- Cloud-only operation (Master AI is local-first — that's the brand).
- Anthropic-account login (Master AI is BYOK / standalone).

## Sources

- https://support.claude.com/en/articles/12902446-claude-in-chrome-permissions-guide
- https://support.claude.com/en/articles/12012173-get-started-with-claude-in-chrome
- https://www.anthropic.com/news/claude-for-chrome

## Memory cross-links

- `[[project_apps_built]]` — Master AI's surface map (Sensei + Pupil + Chrome extension)
- `[[feedback_no_polish_creep]]` — the refactor should hit Phase 1-2 before adding ornament
- `[[feedback_make_it_work_here]]` — software fixes (this refactor) before hardware buys
- `[[user_sensei_as_her]]` — Sensei = she/her in user-facing copy
- `[[feedback_provider_named_prefixes]]` — universal-naming convention applies to extension copy too
