---
name: feedback_no_claude_chrome_extension
description: We do NOT use the claude-in-chrome extension — sensei and secretary are our browser/system hands
metadata: 
  node_type: memory
  type: feedback
  originSessionId: de36b780-0890-4b00-bda6-2ad92f2dec80
---

We do **not** use the `mcp__claude-in-chrome__*` tools or the Claude browser extension. Our extension stack is:

- **sensei** — browser automation (browse, click, fill, screenshot, JS eval, photos, shell)
- **secretary** — autonomous task runner

Never attempt `tabs_context_mcp`, `navigate`, `computer`, or any other `claude-in-chrome` tool. When browser work is needed, route through `mcp__sensei__browse`, `mcp__sensei__click`, etc.

**Why:** The claude-in-chrome extension is not installed/used in this setup. Calling those tools always returns "Browser extension is not connected." The sensei MCP server is the wired extension and handles all browser + system operations.

**How to apply:** On any task requiring browser navigation, screenshots, clicks, or DOM reads — reach for sensei tools, not claude-in-chrome tools.
