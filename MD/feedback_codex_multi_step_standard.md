---
name: Codex's 2026-04-26 multi-step harness-fix pattern is the standard
description: When debugging a Sensei/Master AI flow that fails on a natural request, follow Codex's six-step pattern from 04-26 as the quality bar — root-cause first, then routing, then parser, then memory rule, then rebuild Modelfile, then verify with deterministic checks. Skipping any step leaves the gap visible to the user.
type: feedback
originSessionId: 70696e88-5f85-4999-b10b-463752c63aca
---
When a Sensei flow fails on a natural request, the standard is the multi-step pattern Codex landed across the day on 2026-04-26 (generative video / "make a 30 second video of a bunny hopping in a field"). Elijah called it: *"codex set standard."*

**The six-step pattern, in order:**

1. **Root-cause the failure mode** — read the actual error output, not the symptom Elijah described. ("Provide a URL" reply was a routing bug, not a prompt-tuning bug. Bash text in `.mp4` was a parser bug joining a multi-line ffmpeg, not a model bug.)
2. **Fix the routing** — does the request hit the right model lane in `orchestrate()`? Add detection BEFORE the generic tool gate so the natural ask doesn't fall through to the wrong path. Add explicit cloud→local fallback rather than silent failure.
3. **Fix the parser / executor** — does the harness pass the model's output to the right runner without corruption? Multi-line shell continuations, dangling backslashes, missing-target spawns all need explicit guards (`BLOCKED` + audit log) so failures fail loud, not silent.
4. **Add a memory rule** that teaches the model the EXACT execution shape — not "be better" but the literal 7-step shape (CREATE first, `subprocess.run([...], check=True)` not `os.system`, absolute paths, ensure intermediate dirs, generate then encode, explicit ffmpeg argv list, verify the artifact exists). Goes in `~/.master_ai_memory` so any model lane (cloud or local) sees it.
5. **Rebuild the Modelfile + ollama image** so the local model has the same rule baked in. `ollama create master-ai -f Modelfile-master-ai` after every Modelfile edit, every time.
6. **Verify with deterministic checks** — `python3 -m py_compile`, `git diff --check`, plus a targeted unit-style probe of the specific code path you changed (Codex did `python3 -c '<route check>'` and `python3 -c '<parser test>'`). Don't skip to "tell user it's done" without a green checkmark on each layer touched.

**Why this is the bar:**
- The fix that enabled `rabbit_hop.mp4` to come out as a real MP4 from a natural ask required ALL six steps. Earlier attempts that did one or two of them (just the routing, just the rule) shipped a partial fix and the user-facing test still failed.
- The verification step is what makes "DONE" trustworthy. Without `python3 -m py_compile` + targeted probes, "edited the file" can be claimed when the change is broken.
- The memory rule layer is what propagates the fix to future asks — without it, the next "make a video of X" request relapses to the same wrong instinct.

**How to apply:**
- When Elijah asks me (Claude Code) to fix a Sensei flow, do not stop after one or two steps. Walk all six.
- Be honest about which steps shipped on disk vs which are still pending verification. "Routing fixed, parser fixed, rule added, Modelfile rebuilt, py_compile passed, but no end-to-end run yet" is the right report shape — not "fixed it."
- The ordering matters: routing before parser before memory rule. A memory rule won't help if the request is hitting the wrong model lane.
- This standard applies to harness-layer fixes specifically. Pure copy / brand / docs work doesn't need all six steps — but anything that changes how Sensei processes a natural ask does.
