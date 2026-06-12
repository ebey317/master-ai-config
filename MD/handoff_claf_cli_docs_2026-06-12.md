# Where we are — CLAF / Anthropic CLI docs task

**Date:** 2026-06-12  
**Original ask:** *“go to anthropic.com and open the CLI docs page for me”*

## What happened
- The active task file had a stale HVAC/cover-letter placeholder instead of the real request.
- On doc/help follow-ups the local tool router collapsed back to `core` tools (`TaskList`, `Read`, `Bash`), breaking browser-loop continuity.
- The model got stuck scrolling/clicking the Anthropic marketing footer instead of using search/tools to discover the docs page.
- The canonical CLI docs page is `/cli-reference`, not `/cli`.

## Fixes committed
| Repo | File | Change |
|------|------|--------|
| `projects/claf` | `claf_config.py` | Added doc/help/? signals to browser group; added short-followup fallback so a mid-browser queued command keeps browser tools. Reordered browser tools so `search`/`find_doc_link` appear before `browse`. |
| `projects/claf` | `charter/charter_browser.md` | Added rule: for docs/help requests without an exact URL, use `search` first; don't scroll the marketing homepage footer. |
| `projects/claf` | `charter/charter_capabilities.md` + `orchestrator.py` | New always-injected capabilities slice so the model knows it has terminal access, browser tools, `search`, `find_doc_link`, and the docs workflow. |
| `projects/master-ai` | `sensei_mcp_server.py` | Hardened `click` schema to require `what`. Added `find_doc_link` tool to discover doc URLs from a docs landing page. Removed hardcoded URL example from the description. |

## Current state
- `~/.claf/current_task.json` was deleted after the goal was captured (per charter).
- A live authenticated Claude Code session is running the request and has created the `anthropic.com` tab.
- `claf.service` restarted; the orchestrator now injects the capabilities slice on every local turn.
- No manual page-open was performed — the agent is expected to figure out the docs URL using search/`find_doc_link`.

## Next steps / open items
- Let the live Claude Code session finish and verify it reaches the CLI reference via search/tools, not footer scrolling.
- Remaining queued tasks are in the project TODO list (AI-103, profile builder, Telegram, Fair Chance Railway).
