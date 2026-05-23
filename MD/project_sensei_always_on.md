---
name: Sensei & Pupil — 24/7 always-on design
description: Sensei and Pupil are designed as 24/7 always-on processes on their own dedicated workspace. The machine never cuts off. This is the deployment model Elijah is building toward.
type: project
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---
**Design principle:** Sensei (and likely Pupil) run as a **24-hour machine-based program** — always on, on their own workspace/screen, never closed. The computer (Madam-Mary) stays running continuously. Elijah's quote: *"I'm designing Sensei as a 24 hour machine based program... open 24/7 on a completely different workspace."*

**What that means concretely:**
- The tmux session backing Sensei (`master_ai.py`) is supposed to live forever — supervisor loop already handles crash recovery
- Pupil (`pupil.html`) opens in a browser tab/window on its own workspace and stays open indefinitely
- RustDesk from phone is the primary remote surface — the user reconnects to the same always-running session, doesn't launch fresh
- State (active project, pinned task, chat history, memory) must survive reconnects — it already does, but this is the *why* behind all the persistence work

**Why:** Elijah wants Sensei to behave like a long-running agent/daemon he can walk up to at any time and pick up where he left off — not a CLI tool he launches on demand. The 24/7 model is also how he plans to make Sensei *remember* continuously (not just across restarts, but as an ongoing presence).

**How to apply:**
- Any feature decision: prefer persistent/daemon-style behavior over on-demand launching
- Never suggest "just restart it" as a solution to state problems — the point is that state outlives restarts
- Workspaces matter: Sensei's tmux + Pupil's browser tab should each have their own virtual desktop so they're never fighting other apps for attention
- "Turn on the computer, see Sensei on its workspace" should be the booted state — suggests a `systemd` user service to auto-start Sensei's tmux + auto-open Pupil on login (not yet implemented; potential future task)
- Feature suggestions should assume the session is months old, not minutes old — that's why memory/auto-save/drift reminders matter so much
