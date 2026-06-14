# Where we are — CLAF / Anthropic CLI docs task

**Date:** 2026-06-12  
**Original ask:** *“go to anthropic.com and open the CLI docs page for me”*

## What happened
- The active task file had a stale HVAC/cover-letter placeholder instead of the real request.
- On doc/help follow-ups the local tool router collapsed back to `core` tools (`TaskList`, `Read`, `Bash`), breaking browser-loop continuity.
- The model got stuck scrolling/clicking the Anthropic marketing footer instead of using search/tools to discover the docs page.
- For general Claude Code CLI reference the canonical page is `/cli-reference`; for MCP-specific CLI commands the page is `/docs/en/mcp`.

## Fixes committed
| Repo | File | Change |
|------|------|--------|
| `projects/claf` | `claf_config.py` | Added doc/help/? signals to browser group; added short-followup fallback so a mid-browser queued command keeps browser tools. Reordered browser tools so `search`/`find_doc_link` appear before `browse`. |
| `projects/claf` | `charter/charter_browser.md` | Added rule: for docs/help requests without an exact URL, use `search` first; don't scroll the marketing homepage footer. |
| `projects/claf` | `charter/charter_capabilities.md` + `orchestrator.py` | New always-injected capabilities slice so the model knows it has terminal access, browser tools, `search`, `find_doc_link`, and the docs workflow. |
| `projects/master-ai` | `sensei_mcp_server.py` | Hardened `click` schema to require `what`. Added `find_doc_link` tool to discover doc URLs from a docs landing page. Removed hardcoded URL example from the description. |
| `projects/claf` | `task_state.py` | Patched `format_task_for_injection()` to emit optional `strategy`, `success_criteria`, and `fallback_chain` fields on every turn. |
| `projects/claf` | `TASK_STRUCTURE_RUNBOOK.md` | New reusable guide for rich task state. |
| `projects/claf` | `charter/charter_tasks.md` | Added pointer to the runbook for multi-step / failure-prone work. |

## Outcome of this session
- The CLI docs page in the MCP section was reached: **`https://code.claude.com/docs/en/mcp`**.
- Full page content was retrieved via `FetchURL` after browser DOM tools truncated.
- Page title: **“Connect Claude Code to tools via MCP”**.
- Confirmed CLI commands documented: `claude mcp add`, `claude mcp list`, `claude mcp get`, `claude mcp remove`, `claude mcp serve`.
- `claf.service` restarted so the patched `task_state.py` is loaded.

## How to verify (any agent can run these)
1. **Service is healthy:**
   ```bash
   curl -s http://localhost:8000/healthz | python3 -m json.tool
   ```
   Expected: `ollama_reachable: true`, mode `hybrid`.

2. **Patched task_state.py loads the richer structure:**
   ```bash
   cd ~/projects/claf && source .venv/bin/activate && python3 - <<'PY'
   from task_state import load_task, format_task_for_injection
   print(format_task_for_injection(load_task()))
   PY
   ```
   Expected: output includes `Strategy:`, `Success criteria:`, and `Fallback chain:`.

3. **Orchestrator is running the patched code:**
   ```bash
   systemctl --user status claf.service --no-pager
   ```
   Expected: `Active: active (running)` with a recent start time.

4. **Docs page is reachable:**
   ```bash
   curl -sI https://code.claude.com/docs/en/mcp | head -1
   ```
   Expected: HTTP/2 200 (or similar 2xx).

## Current state
- `~/.claf/current_task.json` — deleted when all items were marked done (per charter).
- `claf.service` running with patched `task_state.py`.
- Handoff and runbook are the durable record of what was done and how to verify it.

## Next steps / open items
Canonical open task list: `~/.claude/projects/-home-elijah/memory/project_open_tasks.md` (injected at session start). Current priorities include AI-103 cert prep, profile builder, Telegram connector, Fair Chance deploy, Command Center, and Gmail app password.

- Do NOT duplicate tasks across handoff docs; update `project_open_tasks.md` and reference it here.
- Future doc/help tasks should use the rich task structure from `TASK_STRUCTURE_RUNBOOK.md`.
