---
name: project-claf-throttle-and-account-separation
description: CLAF orchestrator gained Flash/Tap/Local throttled routing 2026-05-22; account separation hardened (ANTHROPIC_CONSOLE_KEY); 9 cloud peers wired with 6 serving chat as of session end.
metadata: 
  node_type: memory
  type: project
  originSessionId: 05017a5d-eb68-4621-983a-d4f7e91b8fbd
---

CLAF orchestrator (~/projects/claf/) gained three-mode trickle routing on 2026-05-22 with companion module `claf_throttle.py` (new), `_select_mode + TAP_TEMPLATES + detect_tap_intent` in `claf_config.py`, routing splice in `orchestrator.py`, and account separation via `ANTHROPIC_CONSOLE_KEY → ANTHROPIC_API_KEY` alias projection at bootstrap.

**Why:** Operator's $100/mo Max subscription pays for Claude Code CLI runtime, NOT for tokens; routine 80-90% runs on local Qwen; selective Flash escalations hit cloud peers under a 25K-token/day ceiling. Three modes:
- **Local** — local-ollama (`fast-agent:latest` or `qwen2.5:7b`) via :11434
- **Tap** — local drafts, then cheap cloud peer (Groq tier-2) polishes largest fenced code block via intent template (regex/bash/sql/debug/generic in `TAP_TEMPLATES`)
- **Flash** — full cloud handoff; `select_provider(body, prefer_tier=1)` picks lowest-tier enabled cloud peer (currently ollama-cloud-coder)

**Throttle (claf_throttle.py):** `ThrottleState` dataclass with `threading.Lock`, separate hourly/daily windows, reserve/commit/refund pattern. Defaults: 5 flash/hr, 15 tap/hr, 25_000 tokens/day, 3 emergency_flash/day (bypasses hourly cap via `metadata.emergency=true`). `/stats` exposes `throttle.snapshot()`. Verified live: refunds fire correctly on every `provider_error` (Anthropic 429s during Tier-1 testing all refunded; `open_reservations=0` at rest).

**Cloud peer pool (claf_config.py:_cloud_peers, as of 2026-05-22 end of session):**

| Tier | Name | Model | Status | Cost |
|------|------|-------|--------|------|
| 1 | ollama-cloud-coder | qwen3-coder:480b-cloud | ✅ serving (~1.4s) | FREE (Ollama account 'ebey317', free tier) |
| 2 | groq | llama-3.3-70b-versatile | ✅ serving (0.26s) | FREE (rate-limited) |
| 3 | cerebras | qwen-3-235b-a22b-instruct-2507 | ✅ serving (~4s) | paid/free credits |
| 4 | deepseek | deepseek-reasoner | ❌ HTTP402 "Insufficient Balance" | needs top-up at platform.deepseek.com |
| 5 | openai | gpt-4o-mini | ❌ HTTP429 "Quota exceeded" | needs billing setup at platform.openai.com |
| 6 | fireworks | accounts/fireworks/models/deepseek-v4-pro | ✅ serving (0.87s) | paid per-token |
| 7 | openrouter | anthropic/claude-sonnet-4.6 | ✅ serving (2.9s) | pay-per-use credit balance |
| 8 | gemini | gemini-2.5-flash | ❌ no key set | paste a key to enable |
| 9 | anthropic | claude-haiku-4-5-20251001 | ✅ serving (1.2s) | paid per-token; Tier-1 caps Opus/Sonnet at 429, only Haiku passes |

**Why the Anthropic peer is pinned to Haiku 4.5 specifically:** Console account on Tier-1; Opus 4.7 and Sonnet 4.6 both return 429 on every call. Haiku has the most rate-limit headroom. Restore to Opus once monthly spend raises the tier — change `claf_config.py:_cloud_peers()` anthropic entry's `model="claude-haiku-4-5-20251001"` back to `"claude-opus-4-7"`.

**Why qwen3.5:cloud and kimi-k2.5:cloud peers were REMOVED:** Operator never subscribed to Ollama Cloud paid tier; account 'ebey317' is on free tier. Only `qwen3-coder:480b-cloud` is unlocked. If/when subscribed, re-add as `kind="ollama", pool="cloud", url=localhost:11434/api/chat, env_key=None` — SSH-signed via local `ollama` CLI.

**Account separation (load-bearing):**
- Keychain at `~/.master_ai_keys` stores Anthropic key as `ANTHROPIC_CONSOLE_KEY` (NOT `ANTHROPIC_API_KEY`).
- `orchestrator.py:_normalize_bootstrap_key` has alias `ANTHROPIC_CONSOLE_KEY → ANTHROPIC_API_KEY` so CLAF's process env gets the canonical name only INSIDE CLAF.
- `launch.sh:30` already does `unset ANTHROPIC_API_KEY` before `exec claude` — defense-in-depth.
- `~/Downloads/claf_lockdown.sh` writer updated to emit `ANTHROPIC_CONSOLE_KEY`, filtering both legacy and new names on rewrite so re-runs don't undo the separation.
- Max OAuth at `~/.claude/.credentials.json` is what Claude Code uses — never the Console key.

**Keychain tool (~/scripts/keychain.sh):** `list / probe / edit / backup / check / path`. Schema docs at `~/scripts/KEYCHAIN.md`. Probe uses auth-required endpoints (Anthropic /v1/models, OpenRouter /v1/auth/key, Groq /v1/models, etc.) plus User-Agent header to avoid Cloudflare 1010s. `keychain check` verifies env separation (currently reports "All clean").

**Two Ollama device keys — same account, different devices (clarified 2026-05-22):**
- Initially misread as "two accounts." Per Ollama's own settings/keys docs: device keys are auto-added when you run `ollama signin` on each machine. Both keys are device-level identifiers under the single account `ebey317`.
- IGBX (`SHA256:I1Inu2aC8moo...`, `~/.ollama/id_ed25519`) = Madam-Mary's device key. Signs cloud calls from this machine.
- UbgD (`SHA256:g7nhhDSwJK/...`) = operator's other device (confirmed valid by operator 2026-05-22). Stays registered. No CLAF wiring needed from Madam-Mary's side.

**How to apply / extend:**
- Adding a new cloud peer: append a `Provider(...)` to `_cloud_peers()` in `claf_config.py`. Set tier, name, pool="cloud", kind ("openai_compat" for OpenAI-shape APIs, "anthropic" for Anthropic-shape, "ollama" for /api/chat-shape). Set env_key to the keychain variable name. If new variable name, also add it to `orchestrator.py:_KEY_MAP`.
- Raising the daily token ceiling: edit `claf_throttle.py:THROTTLE.token_budget_daily` (currently 25_000). Each Flash reserves 5000; each Tap reserves 800.
- Pinning Flash to a specific peer: change `prefer_tier=1` in `orchestrator.py` messages() handler's Flash branch.

**Sunkissed Soul (Base44 app id 69bbc5d1e9e0ac17a3180439) integration — updated 2026-05-22 late:**
- App schema rebuilt via `edit_base44_app` with comprehensive finish-the-app prompt (audit + 11 module fixes).
- Schema changes verified: `groq_api_key` field REMOVED from HubConfiguration, `keychain_status` enum added, `claf_url` added, `ollama_url` default set to localhost:11434.
- HubConfiguration record (id 69d5abf846151f76a40cc7a7, hub_name="tavern") updated: ollama_url corrected from :5173 to :11434, claf_url=http://localhost:8000, keychain_status=configured, models_available synced from `ollama list`.
- LEAKED KEY SCRUBBED: schema rebuild removed `groq_api_key` field but the record still held the orphan value `gsk_H8a8kw...`. Manually $unset via update_entities 2026-05-22. Different from current keychain key (gsk_CvBn...) — leaked one should be revoked at console.groq.com.
- UserProfile has 3 duplicate records (re-onboarded). Latest is id 69bd2b0465aa49f4be7a0705 (Apr 7 updated, homestead/depressed/kidney profile). Base44 MCP does NOT expose entity deletion — dedupe must happen via Base44 web UI or via a build-prompt that adds soft-delete logic.
- CompanionProfile: "Madam Mary", wise, grandmother mood, Samantha voice, wake word "madam mary." Configured but not yet hooked to chat UI (the build should have added that).
- Field manual at `~/Desktop/FIELD_MANUAL.md` documents the entire stack.

See related: [[feedback-account-separation-strict]].
