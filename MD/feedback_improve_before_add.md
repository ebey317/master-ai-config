---
name: feedback_improve_before_add
description: Standing rule from 2026-05-17. Improve accuracy / fine-tuning of what's already shipped BEFORE adding tools or code. Sensei is operating at "6-year-old" level vs Claude-for-Chrome at "21-year-old" — gap is in intelligence/orchestration of existing tools, not in tool inventory. Raise the age of what's there. Only add new capability after demonstrating the existing surface can't be improved OR after explicitly justifying the addition as a wedge for raising the age.
metadata:
  type: feedback
---

Before adding any new tool, helper, route, or feature to Master AI, first ask: can the existing surface be improved to do this? Fine-tuning, prompt-clarity, accuracy fixes, loop fixes, routing refinements, Modelfile rules — all count as "improving." A new module, a new directive, a new helper, a new endpoint — all count as "adding." Default to improving; only add when you can explicitly defend the addition as a wedge that raises the existing surface's capability.

**Why:** 2026-05-17 — Elijah's framing: "Sensei is six years old, Claude-for-Chrome is twenty-one." We have the tools (88 CLIs detected, 20+ browser primitives, image gen, voice in/out, blended search, memory, harvest cache, cloud lanes). Sensei loops, opens wrong pages, misfires on accuracy. The intelligence to wield the existing tools well is what's missing — not more tools. Adding more tools while the orchestration is shaky compounds the failure surface; improving orchestration on what's there compounds the wins.

**How to apply:**

1. **Default = improve.** When the temptation is "let's add X to handle Y," first list everything in the existing surface that could be tuned to handle Y better. Modelfile rules. CLOUD_SYSTEM rules. Prompt-clarity changes. Routing heuristic edits. Memory entries that teach the model the case. Audit log review to see where the existing surface actually fails. Try those first.
2. **Adding is justified ONLY when:** (a) the existing surface genuinely cannot do the thing (no parser for it, no executor for it, no model knowledge of it), AND (b) the addition is orchestration of existing capabilities (connector tissue between things we already have), NOT duplication of capability the model already has. Example that PASSES: a `SEND_EMAIL` directive that lets the model's existing writing ability + voice-to-text input become outbound email. Example that FAILS: a "polished email composer" helper that re-implements writing the model already does.
3. **When adding is justified, scope to the minimum surface that delivers the capability.** A directive + dispatcher branch + template files (text only) is smaller than a multi-purpose helper module. A focused parser line is smaller than a refactor of the routing engine. Smallest viable addition wins.
4. **Never add to chase a one-off ask.** If Elijah needs a one-shot send / a one-shot fetch / a one-shot anything, do it inline (Bash + Python stdlib) rather than adding code to the repo. The repo is for things that need to live there permanently; one-shots leave no permanent code residue.
5. **Bug fixes are not additions.** Fixing the loop gate (commit `d594665` today) is improvement, not addition. Splitting `_API_HANDLE_LOCK` to per-lane scope is improvement. Restoring a missing synthesis step in cloud RUN/READ is improvement. None of those add new capability; they make existing capability work.
6. **Surface this rule when designs creep.** When Codex's queued work (or Claude's queued work) starts looking like "let's also add…", quote this rule + reframe as improvement. If the answer is genuinely "no, this needs to be added," say so explicitly and defend it against criteria (a) and (b) in point 2.
7. **The 6yo→21yo framing is the diagnostic.** When evaluating any proposed change, ask: does this raise the age of what we already have? Or does it add another tool the 6-year-old still doesn't know how to use well? Only the first kind of change ships under this rule.

Related: [[feedback_no_synth_routes_as_skills]] (sub-second canned replies = no model call = workaround — exact opposite of orchestration). [[feedback_no_polish_creep]] (don't stack ornament on features that work). [[feedback_real_fixes_not_option_menus]] (one root-cause fix, not a buffet). [[feedback_codex_multi_step_standard]] (six-step pattern — improvements typically only need steps 1-3).
