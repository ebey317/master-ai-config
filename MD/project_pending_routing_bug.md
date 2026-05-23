---
name: RESOLVED 2026-04-21 evening — Sensei routing falls off master-ai
description: Previously: default local turns were hitting qwen2.5:3b or groq instead of master-ai. Both halves now resolved. The 3B half was fixed earlier 2026-04-21 (line 1018 comment — short-prompt→3B route removed, 3B reserved for idle/vision only). The silent-groq-fallback half was fixed 2026-04-21 evening by content-routing chat upfront + making the remaining fallback loud.
type: project
originSessionId: 11fd1fb3-8c8a-4bf3-a76a-4a25a90390a4
---
## STATUS: RESOLVED 2026-04-21 evening

## What was broken

Two separate symptoms both routed plain user turns away from master-ai:

1. **3B route (fixed earlier 2026-04-21, pre-evening):** short prompts were being routed to qwen2.5:3b "spark." That model mushed directives. Code comment now at master_ai.py line 1018 explicitly calls out the removal: *"Short ≠ simple — 'fix the bug' is 3 words but requires senior-engineer reasoning."* 3B is now reserved for idle tips + vision preprocessing only. **Not active code anymore.**

2. **Silent groq fallback (fixed 2026-04-21 evening):** `ask_local_stream` return-None fallback at the old line ~4850 quietly routed to groq without telling the user. On an i7-6700T CPU with qwen2.5:7b, local inference often took 1–5 minutes per chat turn, and a 300s timeout meant the fallback fired constantly for short chat like "hi". Harvest log showed chat turns recorded as `model: groq, task_type: cloud` when the user expected master-ai.

## What was fixed

**Edit 1 — content-routed chat lane (master_ai.py, new block after the peacetime block around line 991):**

```
    is_chat_class = not (
        (word_set & CODE_WORDS)
        or (word_set & ALTER_WORDS)
        or (word_set & COMPLEX_WORDS)
        or any(w in low for w in REASONING_WORDS)
    )
    if is_chat_class and have_groq:
        return {"route": "cloud_fast", "model": "groq",
                "reason": "chat → Groq (content-routed)"}
```

Runs in BOTH modes (apocalypse + peacetime) whenever a groq key exists. Plain chat (no code/alter/complex/reasoning signal) goes to groq directly, in ~1s, with the cloud system prompt attached. Bypasses the 5-minute master-ai cold-start entirely.

**Edit 2 — loud fallback warning (master_ai.py, was ~line 4848, now 4848 still):**

```
        if not reply:
            print(f"\n  {R}⚠ [local model timed out — answering via Groq instead]{X}")
```

Was a dim one-liner in default color. Now a red warning with a ⚠ marker so the user knows when the cloud fallback fires. The fallback itself still exists (any build/alter/reasoning path that hits local timeout falls to groq so the user isn't stuck), but it's announced.

## How this was traced

Walked the 5W+H cascade (see `feedback_who_what_where_cascade.md`) across three concrete harvest entries:
- "hi" (chat, WENT to groq via silent fallback — bug)
- "i wanna create thermal ground" (build, WENT to master-ai — correct)
- "CAN WE Put all master ai endurance in one folder?" (build, WENT to 3B — but that code path already removed)

Only the chat-class turn was misrouted in current code. Fix narrowed to the chat lane.

## What to NOT do

- Don't remove the groq fallback at line ~4848 entirely. Build/alter/reasoning still runs local-first by design, and a 5-minute cold-start timeout with NO fallback would leave those turns dead. The fix is to make fallback VISIBLE, not to remove it.
- Don't expand the chat lane to catch reasoning ("why is the sky blue" — REASONING_WORDS catches "why", stays local). Chat lane is intentionally narrow.
- Don't touch the Scrappy block at line 997–1003. It's dormant (no-op when no scrappy-tagged model pulled) but that's intentional — it's a hook. Leave it.

## Third routing bug — RESOLVED 2026-05-03 (text→llava misroute)

Same architectural class, distinct trigger. `detect_route()` and `orchestrate()` both used `any(w in low for w in VISION_WORDS)` substring matching against words like `"see"`, `"show"`, `"look"`, `"read"`, `"describe"`. Any text/code/system request containing one of those words misrouted to llava — including `"so Groq sees it directly"`. Both master-ai AND llava loaded simultaneously on i7-6700T = 9.2 GB resident, both 100% CPU, multi-minute hangs (one observed at 586s).

Fixed in commit `82d11d8` by replacing the substring-match condition with `_is_explicit_vision_request(text)` requiring EITHER an image-extension path (PNG/JPG/etc.) OR a verb+vision-noun phrase (`describe this image`, `show me the screenshot`, `look at /tmp/foo.png`). Soft words alone never trigger vision now. Reason string changed from `"local vision → llava (offline-capable)"` to `"local vision → llava (image-confirmed)"` so the absence of the bug is visible in router metrics.

Validated live: probe `"route probe only: so Groq sees it directly"` routed to `cloud_fast/groq` with `chat → Groq (content-routed)`, llava NOT in candidate list, llava UNTIL timer kept shrinking (no new vision call). 16/16 isolated route tests pass.

## Open flag — memory conflict not yet resolved

`feedback_local_mode_default.md` says "cloud is never the quiet default." After this fix, chat IS quietly routed to cloud when a groq key exists. Elijah's 2026-04-21 reframe ("doesn't necessarily mean local has to be 1st — route by fit") updated this stance. The local-mode-default memory has NOT been rewritten yet — user needs to confirm the product-positioning language before I touch that memory. Until then, treat `feedback_local_mode_default.md` as partially stale for routing-per-turn, still accurate for product-resilience ("machine still usable when cloud is gone").
