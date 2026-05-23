---
name: Master AI Architecture & File Map
description: Full structure of Elijah's Master AI system — scripts, what each does, and how they connect
type: project
originSessionId: 4a207176-3e6e-4bf9-8a27-cccb78510ab3
---
Master AI is a personal AI agent system running on Madam-Mary (Linux Mint 21.3).

**Why:** Elijah built this himself as a personal AI toolkit — terminal agent + web UI + menu launcher.

**How to apply:** When working on any of these files, understand the full system so changes stay consistent.

---

## Files

| File | What it does |
|------|-------------|
| `~/scripts/master.sh` | Main menu launcher — 17 options, x to exit, auto-returns to menu after each option |
| `~/scripts/master_ai.py` | Terminal AI agent (option 4) — voice, web, cloud routing, PC control (RUN/READ/CREATE directives), readline history, modes, hub/help slide-show, sanitize, scroll commands |
| `~/scripts/launch_master_ai.sh` | Tmux launcher with **supervisor loop** — auto-restarts engine on crash; detects "tmux alive but engine dead" and relaunches in-place |
| `~/scripts/master_ai_refresh.sh` | External soft refresh — kills engine, supervisor respawns in 3s. Use from fresh SSH shell. |
| `~/scripts/master_ai_kick.sh` | External hard rebuild — kills tmux session and relaunches. Use when tmux itself is broken. |
| `~/scripts/pc_control.sh` | Bash AI agent (option 14) — plan/safe/auto modes, readline edit, approved commands |
| `~/scripts/master_ai.html` | Web UI chat (served at localhost:8080) — auto-routing, themes, phone mode, keyboard mode |
| `~/scripts/stt_server.py` | HTTP server for the UI — serves HTML, handles STT, saves sessions to ~/.master_ai_chats/ |
| `~/scripts/brand.sh` | Shared color scheme + banner — source this in bash scripts |
| `~/scripts/serve_ui.sh` | Starts stt_server.py on port 8080, opens browser |
| `~/scripts/update_keys.sh` | API key manager — saves to ~/.master_ai_keys |
| `~/scripts/tts_server.py` | TTS server on port 5050 using Piper |
| `~/.tmux.conf` | Mouse mode ON (touch-scroll on mobile SSH) + 50k scrollback |

## Menu Layout (master.sh)
- 4 = Launch Master AI terminal + UI (master_ai.py + opens Firefox)
- 11 = Launch Master AI UI only (localhost:8080)
- 14 = PC Control bash agent (pc_control.sh)
- x = only exit point (no back-to-menu prompt)

## Key config files
- `~/.master_ai_keys` — API keys (groq, openrouter, gemini — no anthropic/openai)
- `~/.master_ai_memory` — persistent facts
- `~/.master_ai_approved` — auto-approved commands
- `~/.master_ai_history` — readline history for master_ai.py
- `~/.master_ai_chats/` — saved sessions + summaries
- `~/.master_ai_cache.json` — response cache (auto-purges errors + ANSI-poisoned entries)
- `~/.tmux.conf` — mouse on + 50k scrollback
- `~/scripts/master.crash.log` — auto-populated by supervisor loop on non-zero exits

## Recovery layers (in-session + external)
1. **In-session `refresh`** (master_ai.py) → `stty sane` + screen reset + `os.execvp` re-run same script
2. **In-session `kick`** (master_ai.py) → `sys.exit(42)` → supervisor loop restarts us in 3s
3. **`~/scripts/master_ai_refresh.sh`** (external) → `pkill python3.*master_ai.py` → supervisor loop respawns
4. **`~/scripts/master_ai_kick.sh`** (external) → `tmux kill-session` + relaunch

## UI layers (mobile-first, "all modes adaptable")
- `hub` command → 18-option paginated control panel (5 slides by category)
- `help` command → 8-section paginated reference (slide show, inline, no screen clear)
- Scroll commands → `up` / `down N` / `top` / `bottom` / `last` (tmux copy-mode under the hood)
- Touch-scroll via tmux mouse mode (Termius/Blink/JuiceSSH compatible)
- `sanitize()` strips ANSI escape codes and control chars from BOTH user input and AI replies

## Colors
master_ai.py uses brand.sh scheme: BC=bold blue banner, BG=bold green, BW=bright white, G=bright green `\033[92m`, C=bright cyan `\033[96m`. Light terminal background — never use dim colors.
