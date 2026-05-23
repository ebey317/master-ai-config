---
name: Study sessions — the next layer after Auto accumulates
description: Elijah's forward direction (2026-04-19). Once Auto mode has a library of approved commands + session summaries + memory, AND once the competitor benchmark has mapped Qwen's real ceiling, Pupil becomes a review layer that runs "study sessions" on the user's own work.
type: project
originSessionId: 6dd5863d-4ed5-45b6-ba4a-581fbc1b9fc0
---
**Elijah's framing (2026-04-19):** *"Once we get Auto a bunch of things saved like we got over here and we get the gist of what we can do in Auto and create, there will be study sessions."*

## What it means
Once there's enough real session data on disk, the product pivots from "run Auto → produce" to "run Auto → produce → review → learn." Pupil stops being a canned curriculum and becomes a retrospective engine. Sensei's agent-loop output becomes the next semester's lesson plan.

## Three shapes this could take

1. **Replay sessions** — Pupil walks a past chat, pauses at decision points, asks "why that move? what would you change?" Turns session logs (`~/.master_ai_profiles/<user>/chats/*.json.gz`) into interactive lessons. Every real session becomes a study artifact.

2. **Pattern extraction** — Sensei reads 30+ session summaries + approved-commands file, surfaces "you do X 80% of the time; the 20% was usually Y." Then offers to turn patterns into reusable snippets / menu shortcuts / custom commands. Sensei teaches Sensei.

3. **Post-build review** — Every time a PROJECTS.md task flips to [x], trigger a 5-min structured review: what was scaffolding (stayed), what was AI (faded), what ships, what gets archived. Aligns with the "70% hardcoded / 30% AI illusion" frame.

## Prerequisites (all currently in flight)

- **Auto-mode session persistence** — chats auto-save every ~10000 chars (already wired in master_ai.py). Good.
- **Approved commands log** — `APPROVED_FILE` per profile already tracked. Good.
- **Memory file per profile** — profile-aware, already live. Good.
- **Benchmark baseline** — competitor_benchmark.sh's `standard.md` tells us what Auto reliably CAN produce; study sessions can then frame "where we exceeded / where we hit the ceiling" as learning moments.
- **NOT YET:** a session-replay UI in Pupil, a pattern-extraction script, or a post-build review trigger on `done`. These are the build items when study-session phase opens.

## When to build

DO NOT build this now. The order is:
1. Finish competitor benchmark endurance run (in flight — will have baseline once it exits).
2. Let real-world Auto sessions accumulate for some number of workdays.
3. Once session dir has meaningful bulk, Elijah opens the study-session phase with an explicit ask.

Premature builds (empty Pupil "review" that has nothing to review) would be the classic "leaf falling off a tree" — per `feedback_give_it_a_home.md`. Wait until there's real material.

## Related

- `feedback_teach_to_sixteen.md` — teaching voice already set.
- `project_apps_built.md` — Pupil is the teaching/workshop layer; this is its next role.
- `project_materializing_whitespace.md` — scaffolding-stays / AI-fades is the review frame.
