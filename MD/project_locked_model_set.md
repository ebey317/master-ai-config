---
name: Locked-In Model Set — 2026-04-23
description: Canonical model set after the 2026-04-23 benchmark + research + lock-in decision. master-ai IS qwen2.5:7b + Sensei SYSTEM — no new pulls needed today. MODELS dict routes coder/general/heavy all to master-ai; qwen2.5:3b is idle/vision-preprocessing only; llava is vision. Four parked future levers with named triggers. Future sessions read this file and DO NOT re-investigate the model set.
type: project
originSessionId: 4e17fa5e-95c3-4e24-8a99-1f0bd42a1787
---
## Lock-in decision

On 2026-04-23, Elijah locked in the current local model set after benchmarking the three installed text models on Madam-Mary and reviewing published specs for candidates. **No new pulls today.** This file is the record so future sessions don't circle back through the same research loop.

## Live set (already on disk, already routed in MODELS dict at master_ai.py:208)

- **`master-ai:latest`** — qwen2.5:7b + Sensei SYSTEM baked in (behavior rules, directive taxonomy, senior-engineer habits, save-path taxonomy). Built from `~/scripts/Modelfile-master-ai` via `ollama create`. Rebuild on Modelfile changes: `ollama create master-ai -f ~/scripts/Modelfile-master-ai`. **Routed to: MODELS["master"], MODELS["coder"], MODELS["general"], MODELS["heavy"]. Daily driver brain.**
- **`qwen2.5:3b`** — idle tips + vision preprocessing only. **NEVER routed to user turns** (2026-04-21 lesson: 3B mangles RUNTERM directives and hallucinates folder paths from voice-to-text noise). Routed to: MODELS["fast"].
- **`llava:latest`** — vision (scrap scanner, apothecary, image+text chat). Routed to: MODELS["vision"].
- **Cloud lanes (keys-gated, already configured in master_ai.py):** Groq (llama-3.3-70b-versatile) · DeepSeek-R1:free (OpenRouter) · qwen3.5:cloud (Ollama cloud) · kimi-k2.5:cloud (Ollama cloud) · Gemini 2.0 Flash · hermes-3-llama-3.1-405B:free (OpenRouter) · gpt-oss-120b:free (OpenRouter) · qwen3-coder:free (OpenRouter) · nemotron-3-super-120b-a12b:free (OpenRouter).

## Benchmark reality (measured 2026-04-23 on Madam-Mary, num_ctx=4096, cold load)

| Model | Short TTFT | Short tok/s | Long TTFT | Long tok/s |
|---|---|---|---|---|
| qwen2.5:3b | 4.76s | 8.69 | 238.47s | 8.81 |
| qwen2.5:7b (bare) | 6.45s | 4.97 | n/m | n/m |
| master-ai:latest | 85.12s | 3.72 | n/m | n/m |

The ~79s TTFT delta between bare qwen2.5:7b and master-ai:latest is the baked Sensei SYSTEM being processed on cold load. The Modelfile's SYSTEM block is designed to be KV-cached once per load; subsequent warm turns benefit from the cache. `keep_alive:"30m"` in master_ai.py:1672 keeps the model resident to amortize this. Cold-load penalty is expected behavior, not a bug — it IS the product personality getting injected.

## Parked future levers (DO NOT revisit unless the named trigger fires)

- **Qwen3:8B** — **trigger: Modelfile-rebuild session.** Published +18 HumanEval, +9 GSM8K, +2 MMLU vs qwen2.5:7b at the same 5.2 GB size. Hybrid "thinking mode" reasoning. Not free — requires rebuilding master-ai's Modelfile on a new base, re-testing Sensei directive behavior. Dedicated session, not a drive-by swap.
- **Qwen3:4B** — **trigger: directive-classifier work lands.** ~67 HumanEval at 2.5 GB — materially stronger than qwen2.5:3b's 42. But directive discipline at 4B is unproven on this stack; could hit the same 2026-04-21 regression.
- **Falcon3-7B-Instruct-1.58bit (via BitNet)** — **trigger: dedicated speed-experiments phase.** Expected 9-23 tok/s (2-6× current), ~1.5 GB footprint. Requires `bitnet.cpp` runtime install (separate from Ollama, different port) + directive testing at 1.58-bit precision. Biggest speed payoff; biggest install lift.
- **Qwen3:30b-a3b MoE** — **trigger: 32 GB RAM upgrade lands.** 19 GB on disk, too big for current 16 GB box. When RAM arrives, this becomes the strongest local option (30B quality at 3B active-param speed via MoE routing).

## Rejected (DO NOT re-propose; noting reason so future-me doesn't circle back)

- Routing `qwen2.5:3b` to user turns (tried + removed 2026-04-21 — directive mangling, voice-to-text folder hallucination). 3B stays in idle/vision-preprocessing only.
- Adjusting `num_ctx` below 4096 (measured 2026-04-23 — zero speedup on typical prompt sizes; the cap only helps when context is actually filling the window, which short turns don't).
- Lowering the 300s Ollama timeout in master_ai.py:1728 (measured 2026-04-21: cold TTFT hits 220s; 180s was firing before the first token, forcing silent cloud fallback on every fresh turn — 300s is deliberate, not careless).
- Dropping master-ai for bare qwen2.5:7b (would strip Sensei's identity — the SYSTEM prompt IS the product, not a performance tax).
- Pulling `qwen2.5-coder:7b` as a "zero-risk add" (misread on 2026-04-23 — MODELS["coder"] actually dispatches to master-ai, not a separate coder model; the "qwen2.5-coder:7b" string only appears as a stale reason text at master_ai.py:1031 which gets corrected in the same commit that saves this file).
- Adding `qwen2.5:14b` or any 14B+ local (RAM-constrained at 16 GB; revisit when the 32 GB upgrade lands).

## What this unblocks

With the model set locked in, future sessions can proceed without re-investigating slot assignments. Forward direction (from other memory): phrasebook layer wiring, GitHub publish + profile README, BitNet install when the phase opens, 32 GB RAM upgrade pipeline.

## Related memory

- `feedback_make_it_work_here.md` — "make this box work" + 32 GB as the hardware-upgrade target. Parks laptop path.
- `project_two_live_agents.md` — Claude Code + Sensei are two live agents; capacity answer is scale-up, not shutdown.
- `feedback_no_dormant_in_designs.md` — only reference components that ship and work. Stale reason-strings and parked-but-not-triggered levers both qualify as dormant until their trigger fires.
- `project_master_ai_architecture.md` — fuller stack map.
- `feedback_long_session_verification.md` — the rule that caught the stale reason-string misread today.
