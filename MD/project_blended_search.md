---
name: Blended Web Search Architecture (5 engines, 3 no-key)
description: Master AI's current-events pipeline blends Gemini grounded search, Wikipedia REST, DuckDuckGo, DDG Instant Answer, and WikiHow-via-Gemini. Three engines need no API key. Architecture added 2026-04-19 after the Wrestlemania hallucination incident.
type: project
originSessionId: aed40948-dd62-4e0b-b731-3a2af0814dac
---
## Why this exists

A local frozen model CAN'T know current events, AND cloud AI models have training cutoffs too — so "route to cloud" isn't a fix for "what happened last night." The fix is live web search injected into the answer path. Built 2026-04-19 after Sensei hallucinated a Wrestlemania result.

## The five engines (blended in `master_ai.py:web_search()`)

| Engine | Needs key? | What it's best at | Notes |
|---|---|---|---|
| **Gemini grounded search** (gemini-2.5-flash + `googleSearch` tool) | yes (Elijah already has one) | current events, synthesized answers with citations | walks a model chain on 429: 2.5-flash → flash-latest → 2.0-flash → 2.5-flash-lite |
| **Wikipedia REST API** | no | encyclopedic facts, people, definitions | no rate limit in practice |
| **DuckDuckGo** (via `ddgs` package) | no | general web results | old `duckduckgo_search` lib is deprecated; `ddgs` is the maintained fork |
| **DuckDuckGo Instant Answer** | no | structured quick facts | often empty for news-style queries — harmless |
| **WikiHow (via Gemini `site:wikihow.com`)** | uses Gemini key | "how to..." questions only | only fires when query starts with how-to phrasing |

All five return None on failure. `web_search()` blends whichever answered; "Search unavailable" fires only when ALL five are silent.

## Where the code lives

- `master_ai.py:gemini_grounded_search()` — Gemini call with fallback chain
- `master_ai.py:wikipedia_search()` — Wikipedia search+summary via REST
- `master_ai.py:duckduckgo_search()` — ddgs lib with fallback to old duckduckgo_search package
- `master_ai.py:ddg_instant_answer()` — DuckDuckGo Instant Answer JSON API
- `master_ai.py:wikihow_via_gemini()` — only when query looks like "how to ..."
- `master_ai.py:web_search()` — top-level blender
- `master_ai.py:orchestrate()` — has a `time_sensitive_warn` route that auto-calls `web_search()` when time-markers detected and user hasn't used an explicit prefix
- `master_ai.py:_looks_time_sensitive()` — the heuristic (words: yesterday/tonight/latest/news/..., phrases: "last night"/"who won"/"score of"/...)
- `stt_server.py:/web_search` — POST endpoint Pupil calls with `{query}`, returns `{query, results, engines}`
- `pupil.html:looksTimeSensitive()` + `runWebSearch()` — client-side JS mirror of the server check; intercepts before Ollama when a message looks time-sensitive

## How Sensei + Pupil stay consistent

Both UIs run the same detection, both route to `web_search()`, both get the same blended results. The server-side `_looks_time_sensitive()` in Python and the client-side `looksTimeSensitive()` in JS have matching word/phrase lists. Keep them in sync when editing — if you add a keyword to one, mirror it to the other.

## Key failure mode Elijah hit 2026-04-19

Gemini free-tier quota is granted **per model, not per project**. `gemini-2.0-flash` returned `limit: 0` (HTTP 429) on a fresh key. `gemini-2.5-flash` on the same key had quota and worked. The fallback chain exists because of this — one model's quota being zero doesn't kill the whole search path.

## Ways to extend (not done yet)

- **Cache `/web_search` by query for 1 hour** — save Gemini quota on repeat asks. Store in memory dict or sqlite.
- **Add Brave Search API** as a third web engine (2000/month free, often better than DDG for news)
- **Add Tavily** — LLM-optimized search with pre-synthesis
- **Add Wolfram Alpha** — specialist for math/dates/conversions (2000/month free)
- **Cross-check mode** — when Gemini says X and DDG says Y, flag disagreement in the UI

See `LINKS.md` Tier B/C/D for the full free-key menu if you want to plug more engines in.

## Install note for fresh boxes

`pip install --user ddgs` — the old `duckduckgo_search` package returns nothing on current DDG. `ddgs` is the maintained fork. Include in `install.sh` so buyers don't hit the broken lib.
