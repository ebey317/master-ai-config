---
name: not-fine-tuning-yet
description: Current harvest/few-shot/LoRA-prep work is "adding features + making it run better," not "fine-tuning." Reserve "fine-tune" for actual training runs.
metadata:
  type: feedback
---

Don't call the harvest few-shot wiring, A/B harness, dataset curation, or score-raising work "fine-tuning." Call it features / improvements / hardening.

**Why:** "Fine-tune" specifically means a training run that modifies model weights (LoRA on qwen2.5:7b, per [[project-harvest-layer]]). That work is deferred. The current track is wiring around the model — prompt-time few-shot injection, toggles, A/B comparison, dataset prep — which doesn't touch weights. Conflating the two muddies what's actually shipped vs. what's still future work and makes status reports wrong.

**How to apply:**
- "we're wiring few-shot," "we're building the A/B harness," "we're curating the dataset," "we're raising the score" — yes.
- "we're fine-tuning Sensei," "few-shot fine-tunes the model" — no, even informally.
- Score context (2026-05-11): `agent_standards_score()` is at 95/100. Stated goal is 101 (past the ceiling). Not chasing that this round.
