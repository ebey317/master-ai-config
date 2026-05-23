---
name: Installer & Component Approval — Sensei's prompt format is canonical
description: Any installer / setup dialog / component approval must use Sensei's 4-option format (Yes / Always / All / No). Buyers learn the format twice before they touch Sensei.
type: feedback
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---
Any time Master AI asks the user whether to install/add/apply something, use Sensei's exact approval dialog shape from `confirm_run()` in `master_ai.py` (L2500+):

```
╔══════════════════════════════════════════════════════╗
║  🥷  Installer wants to add: <name>
║     <short description of what it does and why>
╠══════════════════════════════════════════════════════╣
║   1) Yes     — install this one
║   2) Always  — remember this answer for next time
║   3) All     — yes to every remaining component
║   4) No      — skip this one (with instructions)
╚══════════════════════════════════════════════════════╝
```

**Why:** Elijah wants buyers to encounter this format in the installer FIRST, so by the time they meet Sensei's `confirm_run` dialog the shape is familiar. It's a learnable pattern — Yes / Always / All / No — that matches how a human assistant would ask for permission. One format, reused everywhere, reduces cognitive load and reinforces the brand.

**Where this pattern must appear:**
- `~/scripts/install.sh` (the buyer installer — already uses this shape)
- Any future "add component / enable feature / pull model" prompt in Master AI
- Pupil's setup wizard, if/when it grows a click-through installer flow
- Pack-for-sale rituals

**Rules for variants:**
- Option 1 = "Yes" (one-time approval)
- Option 2 = "Always" (remember decision for this specific prompt)
- Option 3 = "All" (bulk-approve every remaining prompt in this run)
- Option 4 = "No" (skip — and give instructions on how to add later)
- Never more than 5 options. Keep one line per option. The ninja emoji 🥷 anchors the header.

## Local server is NOT optional

Tied to the install flow: Ollama (the local model runtime) is **required**, not optional. Elijah's words: *"make sure they know that having a local server is what this is about and it's not optional you have to have it."*

- If a buyer declines Ollama in the installer, **the install stops.** Don't install partially without the local brain.
- Cloud keys (Groq, OpenAI, etc.) are always optional — they ride on top of the local foundation, never replace it.
- Setup screens (Pupil's wizard, the README, the slideshow) must state this plainly. Don't soften it to "highly recommended" — it's the whole product.

## Free-keys link list

When showing the "do you want cloud keys" step, include the canonical link list:

| Provider | URL | Note |
|---|---|---|
| Groq | https://console.groq.com/keys | fastest free cloud |
| OpenRouter | https://openrouter.ai/keys | many models, one key |
| Google Gemini | https://aistudio.google.com/app/apikey | free with Google account |
| HuggingFace | https://huggingface.co/settings/tokens | models + research |
| Anthropic Claude | https://console.anthropic.com/settings/keys | paid fallback |

Buyer flow: "click the link → sign up (free) → copy key → paste it here." Let them paste up to 5 primary + 5 secondary (backup) slots. Any-key finder auto-identifies which provider the key belongs to.

## How to apply

- When writing/updating any installer UI, confirmation dialog, or setup wizard step, reach for this format before inventing a new shape.
- When reviewing someone else's changes that add a y/n prompt, push back if it doesn't match — the consistency is load-bearing for the $100 A+ polish.
