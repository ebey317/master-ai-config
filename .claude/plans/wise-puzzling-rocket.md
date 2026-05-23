# CLAF Flash / Tap / Local throttled routing — grounded plan

## Context

You want a cost-throttled, 3-mode escalation in CLAF: **Local** (default), **Tap** (local draft + cloud polish on hard snippet), **Flash** (full cloud handoff). Two-window throttle (hourly Flash/Tap caps + daily token ceiling) keeps the platform-key spend bounded. This also resolves the original env-var crossing — by making Flash the only path that intentionally burns the Console `sk-ant-` key, with Claude Code itself routed away from that key onto your Max OAuth.

Your design sketch is sound but uses placeholder names. The real repo shape differs in three load-bearing ways, so the plan below maps onto the actual files.

## What the actual repo looks like (vs. your sketch)

- **`~/projects/claf/orchestrator.py`** (39,790 bytes, 956+ lines) — FastAPI proxy. `messages()` handler starts at line 862. `log()` at line 169 is a JSON-line writer to `~/projects/claf/orchestrator.log` (already has 756 lines of real routing data). `/stats` endpoint already exists at line 705.
- **`~/projects/claf/claf_config.py`** — provider registry + routing. `_is_hard_task(body)` is here at line 153 (not orchestrator.py:821 as your sketch said — line 821 is SSE event emission). Real triggers: `metadata.escalate=True`, system > 40k chars, msg count > 60, or `[ESCALATE]` marker.
- **Modes are `local | hybrid | cloud`** (with `off_grid` / `with_convenience` as legacy aliases). The project-level `CLAUDE.md` describing `off_grid/with_convenience` as primary is stale doc — the code uses the older names.
- **Provider dataclass** (`Provider` at claf_config.py:50): `tier, name, pool, kind, model, url, env_key, enabled, notes`. Frozen.
- **Cloud peer pool is flat with `tier` ordering, lowest-first** (claf_config.py:197). Current order: `groq(1) → gemini(2) → cerebras(3) → fireworks(4) → openrouter(5) → anthropic(6)`. **Your "Flash → Sonnet" assumption is wrong as-is** — `_pick_cloud_peer()` returns Groq llama-3.3-70b by default. To hit Sonnet you either bump Anthropic's tier or pass `prefer_tier=6` to `select_provider` (the arg already exists at line 210).
- **`launch.sh`** is the Claude Code → CLAF wire. It's where the env-var crossing must be cut: Claude Code should launch with `env -u ANTHROPIC_API_KEY` so it falls back to Max OAuth. CLAF itself keeps `ANTHROPIC_API_KEY` in its own process env (needs it for Flash→Anthropic peer).
- **`orchestrator.log` is already rich.** 756 JSON lines of `route_decision` events. Good baseline to validate the throttle against historical traffic before flipping it on.

## Bugs in your sketch I'll fix when wiring it

1. **Dataclass default `_window_start: float = time.time()`** evaluates at class-def time, not instance-creation. Use `field(default_factory=time.time)`.
2. **Daily token budget never resets** — only the hourly Flash/Tap counters do. Need a separate `_day_start` window so daily tokens roll over after 24h.
3. **No thread safety.** Counters race under concurrent requests. `threading.Lock` around the increment block (cheap, correct under uvicorn).
4. **Eager budget burn.** `_check_throttle` increments before the call; a failed provider call wastes a slot. Use reserve/commit/refund: tentatively increment at intake, commit on success, refund on exception. Three lines, no race because of the lock.
5. **`_extract_code_block` was undefined.** Will define: matches ```lang\n…\n``` fences, returns `(snippet, start, end)` tuple so we can splice by index, not `.replace()` (which is brittle when the snippet appears multiple times or whitespace differs after polish).
6. **Cost math undercounts Sonnet output.** Sonnet is $3/Mtok in, $15/Mtok out. A 5000-token Flash that's 60% output is closer to $0.024, not $0.015. I'll set daily ceiling based on a blended $5/Ktok worst case so we don't blow past the ceiling.
7. **Flash defaults to wrong peer.** Without `prefer_tier=6` it goes to Groq. Either I wire Flash to call `select_provider(body, prefer_tier=6)` explicitly, or we bump Anthropic to tier 1 (changes the whole pool's defaults, riskier). Recommendation: explicit `prefer_tier=6`.

## Recommended approach — three files, ~200 LOC total

### File 1 (NEW): `~/projects/claf/claf_throttle.py`

Owns the `ThrottleState` dataclass, the lock, and `check_throttle(request_tokens, need)`. Public surface:

```
class ThrottleState:                  # dataclass, field(default_factory=...) for time
    flash_budget_hourly: int = 5      # conservative starter
    tap_budget_hourly: int = 15
    token_budget_daily: int = 25_000  # ~$0.12-0.25/day worst case

THROTTLE = ThrottleState()
_LOCK = threading.Lock()

def reserve(tokens: int, need: Literal["tap", "flash"]) -> str | None:
    """Returns reservation_id if approved, None if budget exhausted. Caller MUST commit/refund."""

def commit(reservation_id: str) -> None: ...
def refund(reservation_id: str) -> None: ...

def snapshot() -> dict: ...  # for /stats
```

Window logic: separate `_hour_start` and `_day_start` timers. Hourly resets `_flash_used`/`_tap_used`; daily resets `_tokens_used`.

### File 2 (MODIFY): `~/projects/claf/claf_config.py`

Add `_select_mode(body) -> Literal["local","tap","flash"]` right after `_is_hard_task` (line 190). Scoring per your design:

- **flash_score**: +3 if prompt matches `debug|refactor|race condition|deadlock`; +2 if `architecture|design pattern`; +2 if messages payload > 8000 chars; +10 if `body.metadata.force_cloud=True`. Flash if score ≥ 3.
- **tap_score**: +2 if `regex|sql query|bash script|one-liner`; +1 if `explain ... code`; +1 if messages > 4000 chars. Tap if score ≥ 2.
- Else local.

Keep `_is_hard_task` for back-compat (existing `select_provider` still uses it; nothing else changes in the provider machinery).

### File 3 (MODIFY): `~/projects/claf/orchestrator.py`

Three changes inside `messages()` (around line 894, right after the local-mode hard-task 423 but before `select_provider`):

```
mode = _select_mode(body)
reservation = None
if mode == "flash":
    reservation = throttle.reserve(5000, "flash")
    if reservation:
        provider = select_provider(body, prefer_tier=6)  # Anthropic
        log("trickle_flash", reservation=reservation, ...)
    else:
        mode = "tap"  # graceful degrade

if mode == "tap":
    reservation = reservation or throttle.reserve(800, "tap")
    if reservation:
        log("trickle_tap", reservation=reservation, ...)
    else:
        mode = "local"

if mode == "local":
    provider = select_local_model(body)  # existing path
```

After the provider call returns (or raises), `throttle.commit(reservation)` / `throttle.refund(reservation)` runs. Tap mode does the polish step inline: extract code block from local draft, call cloud with a small "fix this snippet" prompt, splice by index.

Also extend `/stats` (orchestrator.py:705) to include `throttle.snapshot()` so you can see budget remaining at a glance.

### File 4 (MODIFY): `~/projects/claf/launch.sh`

One line: prepend `env -u ANTHROPIC_API_KEY` to the `claude` command. This is what cuts the crossed env var — Claude Code falls back to your Max OAuth at `~/.claude/.credentials.json` instead of reading the Console `sk-ant-` key from env. CLAF itself still has the key (different process tree, env still populated from `~/.master_ai_keys` via the bootstrap at orchestrator.py:114).

## Why this design lands the win you described

- **40% speedup on hard tasks**: Flash → openrouter or anthropic peer with `prefer_tier=6` returns a real Sonnet/Opus answer in 3 min vs local Qwen's 5 min on hard prompts.
- **80% cost cut on medium tasks**: Tap sends ~800 tokens to cloud instead of 5000; if half your "medium" traffic goes Tap instead of Flash, you save the projected $0.45/hr → $0.16/hr from your math.
- **Hard ceiling**: 25K tokens/day daily cap means worst-case ~$0.25/day in platform spend. The Max subscription is still paying for Claude Code's CLI invocations (unchanged), and the Console key only bills for explicit Flash/Tap escalations.

## Verification

1. **Pre-flight read** (no code changes yet):
   - `curl -s http://localhost:8000/healthz | jq` — confirm CLAF is up, see current mode.
   - `curl -s http://localhost:8000/stats | jq` — baseline stats before throttle lands.
   - `grep -c route_decision ~/projects/claf/orchestrator.log` — current routing event count.

2. **After landing**:
   - **Local path:** `curl -X POST http://localhost:8000/v1/messages -d '{"model":"claude-sonnet-4-6","stream":false,"messages":[{"role":"user","content":"hi"}]}'` → log shows `trickle_mode=local`, no reservation, no cloud call.
   - **Tap path:** send a prompt with `regex` keyword → log shows `trickle_tap`, reservation issued and committed, response includes a polished snippet that didn't come from the local model.
   - **Flash path:** send a prompt with `metadata: {"force_cloud": true}` → log shows `trickle_flash`, provider=anthropic (or openrouter if Anthropic key disabled), full Sonnet response.
   - **Budget exhaustion:** force 6 Flash calls in a row in `local` mode default budget → 6th flips to Tap. Force enough Tap calls → degrades to local. `/stats` shows zero budget remaining for that window.
   - **Env-var fix:** in a fresh shell, `bash ~/projects/claf/launch.sh`, then inside Claude Code run `/status` (or whatever the current Max-vs-API indicator is) — should report Max subscription, not "using API key from environment."
   - **Daily roll:** advance `_day_start` by 24h in a test (don't wait), confirm `_tokens_used` resets to 0.

3. **Real log audit after a day of use**:
   - `grep trickle_flash ~/projects/claf/orchestrator.log | wc -l` — should be ≤ 5/hr × 24h = 120 in the absolute worst case.
   - `grep trickle_tap ~/projects/claf/orchestrator.log | wc -l` — should be ≤ 15/hr × 24h = 360.
   - Sum token counts in `trickle_*` events; should be ≤ 25_000.

## Open question

You okay with the conservative starter budget (`5 flash/hr, 15 tap/hr, 25K tokens/day`), or do you want a different shape? Easy to bump after we see real log volume.
