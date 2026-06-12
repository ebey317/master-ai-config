# Where we are — CLAF / Anthropic CLI docs task

**Date:** 2026-06-12  
**Original ask:** *“go to anthropic.com and open the CLI docs page for me”*

## What happened
- The active task file had a stale HVAC/cover-letter placeholder instead of the real request.
- On doc/help follow-ups the local tool router collapsed back to `core` tools (`TaskList`, `Read`, `Bash`), breaking browser-loop continuity.
- The model got stuck scrolling/clicking the Anthropic marketing footer instead of navigating directly to docs.
- The first direct-URL guess (`/cli`) was a 404; the canonical CLI docs page is `/cli-reference`.

## Fixes committed
| Repo | File | Change |
|------|------|--------|
| `projects/claf` | `claf_config.py` | Added doc/help/? signals to browser group; added short-followup fallback so a mid-browser queued command keeps browser tools. Added `mcp__sensei__find_doc_link` to the browser tool group. |
| `projects/master-ai` | `sensei_mcp_server.py` | Hardened `click` schema to require `what`. Added new `find_doc_link` tool: fetch a docs landing page and return links matching a term (e.g. Anthropic Claude Code overview → CLI reference). |

## Current state
- `~/.claf/current_task.json` updated with the real goal and marked complete.
- The correct CLI docs page was opened directly in Chrome: `https://docs.anthropic.com/en/docs/claude-code/cli-reference`.
- `claf.service` restarted; `orchestrator.py` is running the updated `claf_config.py`.
- The previous authenticated `claude --chrome` session was killed after it stopped responding to terminal input.

## Next steps / open items
- Restart a fresh authenticated Claude Code session and verify the fixed tool set reaches the model.
- Confirm `find_doc_link` is used on the next doc/help request instead of footer-scrolling.
- Remaining queued tasks are in `~/.claf/current_task.json` / `~/.master_ai_profile.json` / the project TODO list (AI-103, profile builder, Telegram, Fair Chance Railway).
