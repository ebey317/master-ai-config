---
name: Light terminal background color fix
description: Elijah's terminal has a light/white background — bright ANSI colors are invisible
type: feedback
originSessionId: ba514b86-0a5c-4af2-95ba-f99f805d2840
---
Elijah's terminal has a light/grey background. Grey (`\033[90m` / `${D}`) is completely invisible. White (`\033[97m`), red, yellow, and green ARE visible and readable.

**Why:** User has complained multiple times about grey text blending in. Never use grey.

**How to apply:**
- NEVER use `${D}` (dark grey) for any visible text — replace with `${W}` (white)
- Safe visible colors: `${W}` white, `${Y}` yellow, `${R}` red, `${G}` green, `${BC}` bold blue
- `BW` in brand.sh = `\033[97m` (bright white) — use for label text in banner/status line
- Ollama and master-ai local models are UNLIMITED — no token cap to show
- Cloud providers (Groq, OpenAI, OpenRouter) have 128k token limits
