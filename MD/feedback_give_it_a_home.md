---
name: Give Every Feature a Home — nothing should be a leaf falling off a tree
description: Elijah's UX rule — features that exist as disembodied shell commands outside the app are broken. Everything must live INSIDE Sensei / Pupil / the menu, with structure and discoverability.
type: feedback
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---
## Elijah's principle (2026-04-19)

> "Why isn't it part of the app or part of something? Why is it just floating in whitespace and I can just tap it in somewhere — it has no structure, no home. It's basically a leaf falling off of a tree. Where does it go? Where is it supposed to be?"

He was talking about the chunker (`chunker-test`, `chunker "..."`) existing only as shell commands — not wired into Sensei, Pupil, or the menu. A powerful feature with no home.

## The rule

**Every feature of Master AI must have a home inside one of the hardcoded scaffolds:**

| Scaffold | What lives here |
|---|---|
| `master.sh` menu | Top-level launchers (Sensei, Pupil, Remote, Learn, etc.) |
| Sensei commands | Inside-the-dojo actions — `dojo`, `done`, `chunked:`, `mode`, `remember:`, `project`, `task add` |
| Pupil Projects ▾ | Template-card launchers for modes and lessons |
| Pupil Plan ▾ | Structured workflow templates (Budget, Project, Travel, Custom, Chunked Document) |
| learn.sh | Curriculum lessons |
| PROJECTS.md boards | Per-project task ladders |

If a feature is reachable ONLY by typing a bare shell command at a terminal prompt, it doesn't have a home yet. It's homework for the build, not a shippable feature.

## What "having a home" means

1. **Discoverable from the brand surfaces** — the user should find it without remembering a command name. It shows up in a menu, a dropdown, a command list, a slideshow, or an idle thought tip.
2. **Named and documented in one of the hardcoded places** — in `master.sh`'s row labels, in Sensei's `help` / `hub` slides, in Pupil's dropdowns, or in `README_FOR_BUYER.md`.
3. **Addressable by a Sensei command** — anything you can do outside Sensei, you should be able to do FROM Sensei. That's the dojo's role — the single front door for everything.
4. **Tied back to the project board** — if it's meaningful work, there's a task in `PROJECTS.md` that references it.

## Specific case — the chunker

The chunker was a leaf. To give it a home:
- **Sensei command:** `chunked: <task>` — invokes the chunker from inside the dojo; shows progress in Sensei's output area
- **Pupil template:** add 📜 **Chunked Document** to the Plan ▾ dropdown — opens a form (task + word target + model) that fires the chunker
- **Menu option** (optional): a dedicated "📜 Chunked Task" menu launch for terminal-only users
- **Idle discovery tip** in Pupil: "Did you know? Type `chunked:` in Sensei for any document over 3000 words."
- **README section:** a paragraph on chunked mode in the buyer doc

Every feature gets this same treatment. When building a new thing, ask: *where does this LIVE?* If the answer is "a shell command the user has to remember," stop — give it a home first.

## The discovery surfaces (the other half of the insight)

Elijah also said — use idle and thinking moments to educate:

> "Anywhere we're gonna have thoughts — idol or thinking — what we can put there. If we're in the command list, you can always do 'these are the commands — did you know you could do this? Did you know [command] opens this?'"

**Apply:** every idle-thought cloud, every "thinking…" spinner phrase, every unused empty-state in the UI is a chance to teach a command. Rotate through: *"Did you know `dojo tasks` shows your open work?"* *"Did you know `done` marks a task complete and pins the next one?"* *"Did you know Pupil auto-detects any API key you paste?"*

This turns dead moments into teachable moments. Sensei + Pupil should NEVER have a silent UI while waiting — that's wasted real estate for discoverability.

## How to apply

- When designing any new feature: plan its home FIRST, implementation second.
- When reviewing: search for "disembodied commands" — shell scripts that don't appear in any menu/dropdown/help-list are incomplete.
- When updating idle/thinking phrases: make at least 30-50% of them be command-discovery tips ("Did you know?") rather than generic encouragement.
- Maintain a canonical list of Sensei / Pupil / menu commands so the idle-tip rotator can draw from it.
