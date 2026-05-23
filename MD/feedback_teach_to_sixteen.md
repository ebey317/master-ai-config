---
name: Teach Like They're 16 — voice rule for all instructional content
description: All teaching content (lessons, README, slideshow, Sensei explanations, Pupil tips) should be written for a bright 16-year-old. That's the voice and the wording standard.
type: feedback
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---
**Elijah's rule (2026-04-19):** *"We wanna teach this to a 16-year-old, so that's the wording and how we wanna teach."*

## What "teach to a 16-year-old" means

Audience mental model: a curious, smart 16-year-old who's new to what you're explaining. NOT a condescending "dumbed-down" tone, and NOT a dense technical manual.

Specifically:
- **Smart but new** — assume they can *learn* anything but they haven't *met* anything yet. Don't say "as you know" or "you already understand" — they don't.
- **Respectful** — never talk down. They're sharper than a lot of adults; they just haven't been shown this specific thing yet.
- **Plain words, real examples** — "the computer ignores everything after a `#`" beats "the pound symbol denotes a comment in the shell lexer."
- **Short sentences. Concrete stakes.** Why does this matter? What does it do for them? Relate to things they already know.
- **Analogies > jargon** — "`ls` is like opening a folder and seeing what's inside" > "`ls` enumerates filesystem directory entries."
- **Humor when it lands, never forced** — the ninja/belt/dojo voice is already on-brand; keep it but don't clown.
- **One idea per line / paragraph.** A 16-year-old disengages the moment a sentence runs three clauses deep.
- **Show the result.** After explaining a command, show what the output looks like so they can recognize it when they see it.
- **Name the mistake before they make it.** "Don't type `rm -rf /` — that deletes EVERYTHING on your computer. Ever. We're warning you now so you never do it."
- **"You" > "the user."** Speak directly. "You can..." not "The user may..."

## Where this voice rule applies

1. **Pupil lessons** — every class (bash + python + future tracks)
2. **`README_FOR_BUYER.md`** — intro + menu walkthrough + command reference
3. **`slideshow.html`** — every slide text + TTS narration
4. **Sensei's inline explanations** (in `RUN:` / `READ:` / `CREATE:` dialogs and the behavior file) — reply with a 16-year-old in mind
5. **Installer prompts** — descriptions in `ask_install()`
6. **Menu labels in `master.sh`** — prefer "Pupil (local)" over "Pupil (local — tied to Master AI umbrella via API key sync)"
7. **Error messages** — "Ollama isn't answering — that's the program that runs the AI. Try: `systemctl start ollama`"

## Where this voice rule does NOT apply

- Code comments aimed at contributors (Elijah + future Sensei edits) — those can assume dev knowledge
- Memory files (like this one) — internal, for the AI/dev
- Technical specs (systemd units, API schemas)

## How to apply

- When generating new lesson classes / README sections / slideshow text, audit each paragraph: *"would a 16-year-old read this and say 'got it' or 'huh?'"*
- Test: if a sentence has more than one semicolon or parenthetical, rewrite it into two sentences.
- Swap in shorter synonyms: "use" → "run", "execute" → "run", "utilize" → "use", "leverage" → "use".
- Add a concrete example immediately after any abstract concept.
- Existing content (README + slideshow + 6 lesson classes) should be audited and revised toward this rule. Content added from 2026-04-19 forward must already meet it.
