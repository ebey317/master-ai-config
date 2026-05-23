---
name: feedback_sensei_tennis_lessons
description: "Lessons learned operating the sensei browser extension — copy/paste, tab reset, notification hooks, reading replies"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 97ddefb0-48a9-438d-ab97-7029ccbc783a
---

## Sensei Browser Operation Lessons ("Tennis" Game 2026-05-23)

**1. Locate before navigating — LS rule**
On terminal: `ls` to look around. In browser: Google is the lookup. Never trust a spoken/voice URL — search it first. Three-try max: try 1 = locate, try 2 = confirm, try 3 = act.

**2. Stuck error tab recovery**
When Chrome tab is stuck on error page (`Frame with ID 0 is showing error page`), `sensei browse` can't navigate away — `location.href` is blocked on error pages. Fix: `xdotool key ctrl+t` to open fresh tab, then browse from there.

**3. Copy/paste over fill**
`sensei fill` works on many inputs but not always. Prefer:
```bash
echo -n "text" | xsel --clipboard --input
xdotool key ctrl+v
```
Then submit via `xdotool key Return`.

**4. Submit: Enter via xdotool, not sensei**
`sensei fill` + `xdotool key Return` is the pattern. `sensei click button` often fails (wrong selector). xdotool Return on the focused Chrome window submits most chat inputs.

**5. Notification hook — set up BEFORE submitting**
Once I start rendering a response, the side panel comes up and I "leave" the browser. Monitor must be armed FIRST, before submitting, so it fires while I'm away.
Monitor watches Chrome window title via:
```bash
xdotool search --onlyvisible --class "Google-chrome" getwindowname
```
Title changes when AI replies and the chat gets named.

**6. Reading replies — audit log `visible_text`**
`sensei read` truncates. Full content is in `~/.sensei_bridge_audit.jsonl`.
Last action_result entry has `final_state.page_context.visible_text` — that's the full page text including AI replies.

**7. Golden brace = active tab indicator**
The Chrome extension shows a golden brace/highlight around the tab it's controlling. If no golden brace, the extension isn't gripping the right tab. Need to ensure the MCP tab is the active/focused one.

**8. Sensei bridge is at localhost:8080**
`/health` → confirms bridge up. `/extension/action_result` → where Chrome posts results. Audit log at `~/.sensei_bridge_audit.jsonl`.

**Why:** These are the operational mechanics of driving Chrome via sensei. Without them, actions fail silently or loop.

**How to apply:** Before any browser automation task — locate first, set monitor before submitting, read reply from audit log `visible_text`, confirm golden brace on tab.
