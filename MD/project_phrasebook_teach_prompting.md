---
name: Master AI phrasebook + teach-prompting layer — hobby→career feature
description: 2026-04-22 Elijah product direction. Master AI/Sensei/Pupil should include a "how to talk to it" teaching surface — same shape as the Elijah↔Claude phrasebook at ~/Desktop/master_ai_phrasebook.md. Framing: "Claude is the now and future; the regular person needs a way to catch up." This is the feature that flips users from hobbyists to operators (to career).
type: project
originSessionId: f113ec3f-cd82-473e-9850-b39193a31d14
---
## The product insight

Elijah 2026-04-22 (5 AM, voice-to-text): *"I want to suggest a section that's pretty much turned this from hobbies to making money at home career... what Master AI has because you're saying whitespace and we're supposed to be leaning towards Master AI Sensei Pupil... you're the now and future and the regular person needs to catch up."*

Translation: regular users will hit the same language gap Elijah is hitting right now with Claude. They know what they WANT. They don't know how to phrase it. Without a teaching surface, they walk away. The layer that bridges natural language → technical precision is the layer that turns a tool into a career.

## What the feature looks like (from the prototype Elijah + Claude are living right now)

- A shared phrasebook file that grows with use. Plain-English phrases on the left, what the system does on the right, short "why" for each.
- When the user says something new, Sensei asks ONCE what they meant, locks it in, never asks again. Each user has their own phrasebook.
- When Sensei replies with a technical term, it names the plain-English version next to it in parentheses — e.g. "refresh (wake her up)."
- Belt progression (from Pupil's belt system) can anchor the teaching: white belt = everyday verbs, colored belts = shell/file/model vocabulary, black belt = directive-authoring and orchestration.
- This is NOT a docs page. It's a LIVING file that updates as the user speaks. Memory-as-dialogue.

## Where it plugs in

- Sensei: on first use, the dojo gate sees a new profile → asks the user to name 3 things they want to do. Each answer seeds their phrasebook.
- Pupil: browser UI has a "your phrasebook" card — shows the current list, lets the user add/edit/delete lines.
- The existing `master_ai_voice.json` already holds Elijah's verbatim quotes. The phrasebook is the OTHER half — user verbatims + system actions. Same file shape, different owner.
- Claude Code (via inject-memory hook) reads the phrasebook when a Sensei-adjacent session starts, so translation stays consistent between Claude the-builder and Sensei the-product.

## Why this is the pitch wedge

- Alexa answers. Claude code-reviews. Master AI turns what you SAY into what you SHIP. The phrasebook is the surface that makes that true without forcing anyone to learn bash.
- "Genius right next to you" already captures the vibe. The phrasebook is the concrete mechanism. "Knows you" = knows your words for things.
- Reinforces `project_materializing_whitespace.md` — turning thought into digital footprints. A phrase the user speaks → an action that runs → a file that persists. Same arc, now made operational.

## Current state

- Prototype living at `~/Desktop/master_ai_phrasebook.md` — seeded 2026-04-22 AM with 10 entries, grows from this session forward.
- NOT YET integrated into Sensei or Pupil. This is a vision/direction memory.
- When Elijah opens this phase, build order: (1) read phrasebook on Sensei startup, (2) resolve plain-English inputs against it before sending to the model, (3) surface unknown phrases as a one-time prompt, (4) write the new entry to the user's profile.

## Related memory

- `feedback_numbered_5wh_before_execution.md` — the behavior discipline that spawned the phrasebook.
- `user_terminal_mindset.md` — "I'm not using the computer, I'm programming it." The phrasebook is the ramp.
- `user_input_method.md` — remote, voice-to-text, phone. Phrasebook entries must be phone-readable and voice-compatible.
- `project_apps_built.md` — Sensei + Pupil are the only surfaces this lives in.
