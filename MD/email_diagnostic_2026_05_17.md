# Master AI — Architectural diagnostic for review

**Audience:** human reviewers AND other AI systems (Claude, ChatGPT, Gemini, Meta AI).
**Author of the system:** Elijah W. Sr. (ebey317@gmail.com). Maker, not developer. Field HVAC day job. Building this on a single 16 GB i7-6700T (Madam-Mary), Linux Mint, Tailscale-tunneled to phone.
**Purpose of this message:** I have a working but uneven local-first AI agent. Tools exist. Orchestration is shaky. I'm sending the full picture so you can tell me where the gaps are and what to improve before I add anything new.

---

## What this product is, in one paragraph

A local-first AI agent named Sensei that lives in a tmux pane on my machine and reaches into Chrome via a sibling extension. One brain (a Modelfile-customized `master-ai:latest` on Ollama = qwen2.5:7b + a long SYSTEM prompt), two bodies (terminal + browser side panel), one HTTP bridge between them (`stt_server.py` on localhost:8080). Cloud lanes (Groq, OpenRouter/DeepSeek-R1, Gemini, Cerebras, Fireworks) are opt-in per-message via `fast:`/`deep:`/`reason:` prefixes. Memory is a file-based system the agent reads every turn. The product goal is "Claude Code, but on your hardware, modeled on Anthropic's safety patterns." HEAD on private GitHub `ebey317/master-ai-private` is `db54f18` (`d594665` after last commit, plus 1 unpushed loop-gate fix).

## The shape — three bodies, one brain

```
                  ┌───────────────────────────────┐
                  │  one brain (Ollama qwen2.5:7b │
                  │   + SYSTEM prompt + memory    │
                  │   + harvest cache)            │
                  └───────────────┬───────────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                   │
   ┌──────────▼─────────┐  ┌─────▼────────┐  ┌──────▼────────┐
   │ Sensei (tmux UI)   │  │ stt_server   │  │ Chrome ext    │
   │ master_ai.py       │  │ /chat        │  │ side panel +  │
   │ STT/TTS/routing    │  │ /chat/cont   │  │ content_script│
   └────────────────────┘  │ /apply_prof  │  │ + service_wkr │
                           │ /dispatch    │  └───────────────┘
                           └──────────────┘
```

Cloud lanes are NOT a third body — they're router destinations the brain calls into when the prefix asks for them.

## Tool inventory (what the agent can actually invoke today)

**Shell + filesystem:** RUN (one-shot), RUNTERM (long-running), READ, CREATE, EDIT, ASK. Gated by an approval-list file, mode-aware confirmation, a self-modification denylist, and a typed-action audit (`~/.master_ai_audit_typed.jsonl`).

**Browser primitives via the Chrome extension:** BROWSER_NAV, BROWSER_CLICK, BROWSER_FILL, BROWSER_SUBMIT, BROWSER_READ_PAGE, BROWSER_READ, BROWSER_SCREENSHOT, BROWSER_WAIT, BROWSER_SCROLL, BROWSER_DOUBLE_CLICK, BROWSER_CDP_MOUSE, BROWSER_CDP_KEY, BROWSER_TAB_CREATE, BROWSER_JS, BROWSER_CONSOLE, BROWSER_NETWORK, BROWSER_RESIZE_WINDOW, BROWSER_FIND, BROWSER_EXTRACT_LIST, BROWSER_CLOSE_TAB, BROWSER_FILL_FORM (pulls from `~/.master_ai_profile.json`), BROWSER_DRIVE_INSPECT_FOLDER.

**Local capabilities:** Stable Diffusion (sd.cpp + LCM + TAESD on CPU, ~56s/image), Piper TTS, Whisper STT, harvest cache (records every call, serves near-duplicates from cache with zero model call), blended web search across five engines (Gemini grounded + Wikipedia + DDG + DDG Instant + WikiHow-via-Gemini, three of five keyless), SQLite FTS5 index over conversation history + memory (7466 rows, 40ms incremental rebuild), 88/95 CLI tool detector inventory (`~/.sensei_tool_inventory.json`).

**Memory + identity:** A markdown-file memory system the agent reads every turn (`~/.master_ai_memory`, populated from `~/.claude/projects/-home-elijah/memory/` via `sync_hard_limits.py`). Modelfile teaches identity (the agent treats "you/your app" as referring to itself), reasoning discipline, safety gates, and routing hints. CLOUD_SYSTEM is a sibling prompt that teaches the same rules to cloud lanes — Modelfile and CLOUD_SYSTEM must be mirrored or cloud-fast silently breaks (a real pattern that has happened twice in 24 hours).

**Anthropic-aligned safety primitives (Phase 1 + 2 shipped):** typed action envelope, JSONL audit trail, irreversible-action heuristics (purchases / deletes / auth / sensitive_fill / purchase_url) that gate before execution, BROWSER_SCREENSHOT for verification, policy gating at confirm_run, self-modification denylist with hardcoded protected paths, BLOCKED-feedback injection so cloud lanes don't hallucinate success when a safeguard fires.

## What works today, honestly

- One-shot questions answered (router picks local or cloud per prefix or content heuristic).
- File find / read / edit on simple targets ("where is X", "open file Y", "edit function Z in file W").
- Shell command execution with mode-aware confirmation.
- Single-step browser actions when the page is unambiguous (one input, one button).
- Image generation locally, no internet needed.
- Voice in / voice out works (Whisper + Piper, both local).
- The agent stays "in character" — same persona across tmux and browser side panel.
- Memory persistence across sessions; the agent picks up where the last session ended.

## What's uneven or broken — the operating gap

This is the part I most want feedback on. Tools exist. Intelligence to wield them well doesn't yet. Concrete failure modes from real sessions:

1. **Browser auto-fire loop.** The Chrome extension's side panel kept POSTing `/chat/continue` after the model emitted DONE, because the gate at `continueLoop()` checked `state.loop.stopped/active/turn_id` but ignored `done === true` and `terminal_reason: "no_actions"`. Real example: one user prompt fired 17 separate turns through cloud-fast → Groq, racking up cost and interfering with the user actively typing in the page. Fix landed today (commit `d594665`) — adds the missing gate. The diagnostic question this raises for me: WHY did I miss this for so long? Two architectural surfaces (Modelfile teaches the local 7B, CLOUD_SYSTEM teaches cloud lanes) need to mirror every behavioral rule — when I forget to mirror, cloud-lane behavior silently diverges from local-lane.

2. **Wrong-page navigation.** "Open YouTube" sometimes navigates to a YouTube channel rather than the homepage. "Find this listing" sometimes opens a Workday-hosted version when the request implied Indeed. The model is choosing URLs from heuristics rather than from grounded interpretation of the prompt. I have a content-routing heuristic that I do not fully trust.

3. **Missed accuracy on directive emission.** The local 7B sometimes writes prose for "where is X / find X / what's on port N" questions instead of emitting `RUN:` or `READ:` directives. I have a deterministic short-circuit + a "retry on prose" repair loop + a Modelfile rule covering this — and it still misfires often enough that I notice. The fix today (BOX REASONING + RESULT HONESTY + REASON BEFORE EMITTING + LOOP TERMINATION) is teaching the model harder, but the underlying issue may be that a 7B simply can't carry this much rule-following reliably.

4. **Cloud-lane synthesis after RUN/READ is missing.** Model emits `RUN: jq -r ...`, dispatcher runs it, result lands in the journal — but the chat UI shows only the bare `$ jq …` line. There's no second model call to turn the result into a user-facing sentence. Architectural gap in the cloud-fast flow; the BROWSER_* lane already does this correctly, the RUN/READ lane doesn't.

5. **Concurrency: cloud-fast queues behind local lock.** A 58.8-second latency for a one-word ack via Groq this morning is direct evidence cloud-fast requests are serializing behind `_API_HANDLE_LOCK` in `stt_server.py`. Groq is normally sub-second. The local Ollama inference holding the lock for 8+ minutes during a runaway also blocks all cloud-lane requests for the same duration. Whole-server-lock is the wrong scope.

6. **Conversational acknowledgments reopen action loops.** "nice" / "ok" / "cool" classify as continuation triggers in some paths, firing a fresh round when the user was just acknowledging.

7. **Zero job applications actually submitted.** All the infrastructure exists — `/apply_profile`, BROWSER_FILL_FORM, `log_application`, Simplify.jobs coordination, first-submit pause gate. There is no user-facing entry point (REPL command or button) that takes a job URL and orchestrates the full sequence. The product's primary use case has not shipped.

8. **No email-send capability at all.** Zero mail tools installed (msmtp/sendmail/mailx/mutt all absent), zero SMTP configs, no key slots for email accounts. This message is the proof of the gap — I'm writing it to a file because the agent can't send it directly.

## The framing that organizes this critique

**Sensei is operating at a 6-year-old's capability. Claude-for-Chrome (Anthropic's own product) is operating at a 21-year-old's.** I have the tools. I do not yet have the intelligence to wield them well. The path forward is to "raise the age" of the existing surface — fine-tune accuracy, improve orchestration, fix the failure modes above — BEFORE adding any new tools.

The rule I'm adopting today: **improve before add.** Improve accuracy / fine-tune / fix failure modes of what's already shipped FIRST. Only add new capability after explicitly demonstrating the existing surface can't be improved.

## What I'm looking for from you (human OR AI reader)

1. **Architectural critique.** Where is this design weakest? Where would you re-architect?
2. **Sourcing pointers.** What documents, GitHub repos, papers, or patterns from Anthropic, OpenAI, Google/Gemini, Meta AI, or others should I be reading? Be specific — link if you can.
3. **Failure-mode triage.** Of the 8 gaps above, which would you fix first and why? Which two or three give the biggest "age raise" per unit of effort?
4. **Honest comparison.** Where am I overestimating what this product does? Where am I underestimating? Where is the marketing language outpacing the reality?
5. **Wedges I might be missing.** What's a small, focused improvement that would have an outsized effect on the day-to-day feel of using this thing?

## Reference material I can attach if helpful

- `~/MD/project_master_ai_product_description.md` — 384-line full architectural product description (state as of 2026-05-13; somewhat ahead of where the SHIPPED product is).
- `~/MD/project_master_ai_state.md` — current-state hook, updated this morning, last HEAD bumped to `db54f18`.
- `~/MD/handoff_2026_05_17_loop_and_jobs.md` — today's Claude/Codex split-work coordination doc.
- `~/MD/project_product_requirements.md` — 11-point spec the product is measured against, with PASS/PARTIAL/GAP per item.
- Source repo (private): `https://github.com/ebey317/master-ai-private`. Read access can be granted on request.

I'm building this in my own time, mostly evenings and overnight, in parallel with a job search. Honest replies are more useful than polite ones. Tell me what's wrong.

— Elijah
