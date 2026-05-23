---
name: Harvest Layer — cache + few-shot distillation
description: Built 2026-04-20. ~/scripts/harvest.py + 5 hooks in master_ai.py. Every local AND cloud call is recorded; near-duplicate prompts serve from cache with zero model call. Foundation for eventual LoRA fine-tune on accumulated data.
type: project
originSessionId: 555b28a0-a673-4d8f-ac99-f3d4c13f2b9a
---
## What it is

Software-only layer (~150 lines of stdlib Python) that sits between `orchestrate()` and the model calls. Every answer — local or cloud — gets recorded. Near-duplicate prompts serve from cache. Zero external dependencies.

## Why it was built (2026-04-20)

Elijah's framing — he doesn't want a subscription API; wants to buy the intelligence once and keep it. Pay-once APIs don't exist (compute is ongoing), but a local harvest + distill pattern approximates it: use free-tier cloud as the teacher, save every output, serve subsequent similar questions from cache, eventually fine-tune the local model on the accumulated corpus.

Critical correction Elijah made during build: "local stuff needs to be wired in too." Original framing was "every cloud call gets recorded" — wrong mental model. Correct: every call (local OR cloud) gets recorded. Offline works the same as online. Cloud going down doesn't break anything; it just stops NEW knowledge entering.

## Files on disk

- `~/scripts/harvest.py` — the module. Exposes `record()`, `lookup()`, `few_shot()`, `format_few_shot()`, `stats()`, `format_stats()`. Has a `__main__` CLI for `python3 harvest.py [lookup|few_shot] "query"`.
- `~/.master_ai_harvest.jsonl` — the append-only data file. One JSON object per line: `{ts, prompt, model, response, task_type, meta?}`. Created on first write.

## Integration points in master_ai.py

1. **Import** at top (after `from pathlib import Path`): `try: import harvest; except Exception: harvest = None` — fail-soft.
2. **Cache lookup in `orchestrate()`** — step 2b, after explicit prefixes, before vision check. `harvest.lookup(stripped, min_similarity=0.85, max_age_days=90)`. If hit, returns `route="cached"` with the response pre-embedded.
3. **`cached` route handler in `handle()`** — near the `ask_user` handler. Prints a "[thinking: harvest cache hit sim=X.XX ...]" line, serves the stored response, appends to history, returns. Zero model call.
4. **Record hook in `ask_local()`** — after `response_text = result["message"]["content"]`, extracts last user message from `messages[]`, calls `harvest.record(last_user, model, response_text, task_type="local")`.
5. **Record hook in `ask_local_stream()`** — after assembling `result = "".join(full_text)`, same pattern with `task_type="local_stream"`.
6. **Record hook in `ask_cloud()`** — uses a local `_record()` helper. Fallback chain now tagged with provider names (`hermes-405b`, `deepseek-r1`, etc.) so we record WHICH provider actually answered, not just the requested one.

## Tuning knobs

- `min_similarity=0.85` on the main cache lookup — intentionally strict, only near-duplicates hit. Adjust down (0.75-0.80) if Elijah wants more cache hits at the cost of slightly-off answers.
- `max_age_days=90` — cache entries older than 90 days don't hit. Code evolves, old answers go stale.
- `few_shot()` default `min_similarity=0.30` — much looser, because few-shot examples are about format/style, not exact match.
- `MAX_ENTRIES_IN_MEMORY=2000` — only most recent N entries are scanned. Past that, lookup ignores older entries (they're still on disk; just not hot-path).

## Sensei commands wired

- `harvest` — shows stats block (total entries, by_model, by_task, date range). Added to tab-completion list.
- CLI fallback: `python3 ~/scripts/harvest.py` prints the same stats outside Sensei.

## What's NOT built yet (future work)

- **Few-shot injection into LOCAL_SYSTEM** — `format_few_shot()` exists but isn't yet called from `ask_local`/`ask_local_stream`. Next step: when routing to a local model AND cache didn't hit, inject top-3 similar past (prompt, response) pairs as examples. Intended fix for the qwen2.5:7b "describes changes instead of emitting EDIT:" gap.
- **Quality tagging** — no `good`/`bad` Sensei commands yet. All entries get equal weight. Future: user can tag last response as high-quality → becomes preferred few-shot example.
- **Embedding-based similarity** — Jaccard tokens is cheap but brittle. Swap to `nomic-embed-text` via Ollama later for semantic matching.
- **LoRA fine-tune pipeline** — when harvest has ~500+ quality entries, convert to training JSONL and LoRA-tune qwen2.5:7b. Goal: permanent skill transfer, no cloud needed after.
- **Pupil UI hook** — `/harvest_stats` endpoint on stt_server would let Pupil show the harvest bar alongside the RAM bar. Not built.

## Why this matters for the product (Elijah's sellable line)

Every buyer's Master AI ships with an empty harvest. After 30 days of their use, it has THEIR work in it. 90 days in, it has their patterns. Generic cloud AIs are stateless by design; Master AI becomes irreplaceable to each owner because it specializes TO them over time. This is the moat.

## Do NOT

- Don't un-wire this without Elijah's say-so. Too much of the value of the distillation pattern depends on continuous harvesting.
- Don't raise `max_age_days` above ~180 — stale answers hurt more than they help once the product evolves past them.
- Don't auto-inject few-shot without the OK to flip it on; it will change qwen2.5:7b behavior and we want to A/B the effect deliberately.
