---
name: Routing schematic — as-built after 2026-04-20 auto-route patches
description: Snapshot of how Sensei's routing actually works after the "operate like you" / 3-slot patches. Documents the dual-authority orchestrate()+detect_route() pattern, the stale Modelfile, dead LOCAL_SYSTEM, and the default-mode gap. Read this before touching master_ai.py routing.
type: project
originSessionId: 39d12576-f270-4aa3-8989-c741ebeec446
---
## What the 2026-04-20 patches did

**Patch 1 — peacetime intent split in `orchestrate()`** (master_ai.py around line 443 + line 891)
- New constant `ALTER_WORDS` = verbs that imply changing a file or system state (edit, modify, refactor, patch, rewrite, replace, rename, install, uninstall, configure, setup, create, delete, remove, build, generate, make, update, upgrade, migrate).
- Peacetime block now routes alter/code/reasoning to `cloud_deep` (DeepSeek-R1 via OpenRouter; qwen3.5:cloud fallback). Chat/quick text still goes to Groq (`cloud_fast`).
- Result: in peacetime mode, no more typing `fast:` or `deep:`. Router picks the lane.

**Patch 2 — `LOCAL_DIRECTIVE_HINT`** (master_ai.py around line 1443 + line 4424)
- New module constant with CREATE:/EDIT:/RUN:/READ: format + heredoc delimiters.
- Prepended to every LOCAL user message so vanilla qwen2.5:7b emits directives instead of describing changes in prose.
- Stable bytes → KV cache still works across turns.

## Current routing schematic (as-built)

```
user_text
   ↓
handle()
   ↓
orchestrate() ──▶ decision dict {route, model, reason, ...}
   ↓                     ↓
   ├─ route=save_refresh        → handle_save_refresh()  [short-circuit]
   ├─ route=ask_user            → clarify prompt
   ├─ route=time_sensitive_warn → web_search() + fallback menu
   ├─ route=scope_check         → scope prompt
   ├─ route=recall_memory       → inject memory into prompt, continue
   ├─ route=cloud_fast          → OVERRIDE: route=cloud, model=groq
   ├─ route=cloud_vision        → OVERRIDE: route=vision
   ├─ route=cloud_deep          → OVERRIDE: route=local, model=qwen3.5:cloud
   └─ route=local               → NO override; detect_route() decides
   ↓
detect_route()  (legacy — only drives dispatch when decision.route==local)
   ↓
dispatch branch on route:
   ├─ web    → web_search + ask_cloud(gemini/groq) or ask_local
   ├─ cloud  → ask_cloud(provider)
   ├─ vision → ask_local_stream(kimi) + fallbacks
   └─ else   → ask_local_stream(model)   [the LOCAL path — gets LOCAL_DIRECTIVE_HINT]
```

## Dual authority — two routers, one problem

`orchestrate()` is the smart decision maker. `detect_route()` is a legacy function still being called. When orchestrate says "local" (most common in apocalypse mode), detect_route's decision actually drives dispatch. Works for the intended cases (e.g., detect_route catches WEB_WORDS and routes to `web` → web_search fires even when orchestrate said local).

**Not schematic-clear. Future refactor target:** make `orchestrate()` the sole source of truth. Fold web detection into orchestrate. Delete `detect_route()`.

## Dead / legacy code found (not fixed, logged here)

1. **`~/scripts/Modelfile-master-ai`** — points to qwen2.5:14b, which was removed 2026-04-19. The Modelfile is stale and nothing builds from it. The directive syntax in it is also wrong (uses `CREATE: <path>` without the `<<<CONTENT ... >>>CONTENT` heredoc).
2. **`LOCAL_SYSTEM` variable** (master_ai.py line ~4336) — built every turn but NEVER inserted into history for local routes (line ~4384 pops the system message). Dead until someone wires it up. Patch 2 works around this with the user-message-level hint instead of fixing the root cause.
3. **`route == "web"` branch in handle()** (line ~4436) — unreachable via `orchestrate()`; only fires via the `detect_route()` fallback. Works, but opaque.

## Behavioral gap with "operate like you"

The new 3-slot auto-routing only fires when `run_mode == "peacetime"`. In DEFAULT apocalypse mode, everything still goes local — no auto-route to cloud even when cloud is up.

For Elijah to get "no prefix typing" behavior, he must either:
- Type `mode connected` once per session, OR
- Set `~/.master_ai_run_mode` to `peacetime` persistently, OR
- Flip the default run mode to peacetime (contradicts `feedback_local_mode_default.md` which says LOCAL is default).

**Not flipped automatically.** Elijah's existing feedback memory says LOCAL is default; that's a load-bearing architectural choice. He should decide whether to change it, not me.

## How to apply

- Before touching routing again, read this file first.
- If adding a new intent → pick ONE authority (prefer `orchestrate()`), don't add to both.
- If pulling the real `qwen2.5-coder:7b`, update `MODELS["coder"]` (currently aliased to qwen2.5:7b per line 181).
- If touching LOCAL_DIRECTIVE_HINT, keep it a STABLE constant — dynamic content in system-level prompts invalidates KV cache and adds 60-120s prefill on CPU.
