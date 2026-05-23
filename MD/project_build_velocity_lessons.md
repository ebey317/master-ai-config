---
name: Build-velocity lessons learned (2026-04-25 retrospective)
description: Five things that cost the most time getting Master AI to its 04-25 state, eleven changes that would have compressed the build by 2-3 weeks, and the meta-pattern (decide-faster-on-the-boring-parts) that compounds going forward.
type: project
originSessionId: 026ab61e-8cba-4268-801a-309c4199d383
---
Distilled from a full-app retrospective Elijah asked for at HEAD `965adc9`. Save these so future-me doesn't repeat the mistakes.

## Top five time costs (ranked by hours burned)

1. **Multi-agent collisions on the same files.** Claude + Codex + Elijah all editing `master_ai.py` and `pupil.html` in overlapping windows. Today's lost-and-recovered `f3ad3ae` was just the latest symptom. No "who has the conch" mechanism.
2. **Two-mode framing churn.** Apocalypse/peacetime → local/connected → route-by-fit. Each rename costs UI + docs + mental model + lingering code paths.
3. **Model thrashing.** Pull-test-decide-revert across qwen3:8b, qwen3:4b, falcon3-bitnet, qwen3:30b-a3b. Locked model set on 04-23 should have been the day-2 decision.
4. **Mandatory gates that became optional.** Dojo: sealed → optional → bypass-by-default. Each step was real engineering on a feature that ended up barely used.
5. **Late tests.** First parser test landed 2026-04-25. Every prior bug got found by Elijah running it on his phone — expensive instrumentation.

## Eleven changes that would have compressed the build by 2-3 weeks

A. Memory system from session 1 — kills the re-investigation cycle.
B. Single locked model from week 1 — qwen2.5:7b Q4 base, stop testing alternatives.
C. No mandatory entry gates. Optional pinning, direct entry.
D. No two-mode app framing. Local-default with cloud-opt-in only.
E. Tests in week 1, even ten lines of parser fixtures.
F. "Default ship" for whichever AI is in front of him — don't write Codex handoffs when Elijah is asking me to ship.
G. One pitch locked by month 1 (only locked 04-23).
H. Buyer bundle from week 1 as the build target — every feature checks against "does this go in the bundle?" before being built.
I. `~/scripts/` as a clean tracked repo from day 1 with PR-style isolation per agent (Claude on a branch, Codex on a branch, Elijah on main).
J. "No homeless features" rule earlier — chunker, scrappy, study-sessions all consumed brain time before being parked or archived.
K. Pack-the-snapshots cadence (the every-5-min `~/Desktop/AI_CONTEXT/` cron) from week 1.

## The meta-pattern

Compression comes from **deciding faster on the boring parts** so the maker brain stays on the hard part. Elijah is a maker who loves solving the hard stuff (routing, mode framing, recovery chains) — most lost time was decisions that got revisited rather than locked. Memory + locked model set are the two best frictionless-decision tools in the project. Extend the pattern: locked-pitch, locked-buyer-bundle, locked-test-suite, locked-agent-isolation.

## How to apply going forward

- **When Elijah is choosing between revisiting a settled decision and shipping the next thing, push toward shipping.** If a settled topic comes back up (model size, mode framing, gate flow, pitch wording), name that it's settled and ask if he wants to genuinely re-open it or just ship.
- **When proposing options, lead with my recommended path and ask `go` or correct.** Reserve numbered choices for cases where the tradeoff truly belongs to him (surface picks, cost/risk forks). Implementation detail is mine to default — see `feedback_numbered_5wh_before_execution.md` for the full rule.
- **When a new feature is asked for, stress-test it against "does this go in the buyer bundle?"** If no, suggest parking it as a project memory and not building yet.
- **When two agents (Claude + Codex) are working same-day, name it and ask Elijah which file each owns** before either edits. Today's lost-commit incident is on me for not asking.
- **When a feature feels homeless (no menu entry, no Sensei command, no Pupil card), do not build the back-end first.** Either find/design its home or park it. The chunker is the cautionary tale.
