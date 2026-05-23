# Master AI — Architectural diagnostic for review

**Audience:** human reviewers and other AI systems (Claude, ChatGPT, Gemini, Meta AI).
**Purpose:** request for honest critique on a working but uneven local-first AI agent. Tell me where the gaps are, where the design is weakest, and what published patterns I should be reading.

---

## What this product is, in one paragraph

A local-first AI agent named Sensei. Runs on a single Linux machine, consumer hardware, 16 GB RAM, 6th-gen Intel CPU, no GPU. Lives in a tmux pane and reaches into Chrome via a sibling extension. One brain: a Modelfile-customized qwen2.5:7b on Ollama with a long SYSTEM prompt. Two bodies: terminal and browser side panel. One HTTP bridge between them. Cloud lanes (Groq, OpenRouter, DeepSeek-R1, Gemini, Cerebras, Fireworks) are opt-in per-message via `fast:` / `deep:` / `reason:` prefixes. Memory is file-based and the agent reads it every turn. The product goal: a Claude-Code-shaped local-first computer agent, modeled on Anthropic's safety patterns, that the operator owns end-to-end.

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
   │ STT/TTS/routing    │  │ /chat /cont  │  │ content_script│
   │                    │  │ /dispatch    │  │ + service_wkr │
   └────────────────────┘  └──────────────┘  └───────────────┘
```

Cloud lanes are NOT a third body — they are router destinations the brain calls into when a prefix asks for them.

## Tool inventory

**Shell + filesystem:** RUN (one-shot), RUNTERM (long-running), READ, CREATE, EDIT, ASK. Gated by an approval list, mode-aware confirmation, self-modification denylist, typed-action audit JSONL.

**Browser primitives via the Chrome extension:** BROWSER_NAV, BROWSER_CLICK, BROWSER_FILL, BROWSER_FILL_FORM, BROWSER_SUBMIT, BROWSER_READ_PAGE, BROWSER_READ, BROWSER_SCREENSHOT, BROWSER_WAIT, BROWSER_SCROLL, BROWSER_DOUBLE_CLICK, BROWSER_CDP_MOUSE, BROWSER_CDP_KEY, BROWSER_TAB_CREATE, BROWSER_CLOSE_TAB, BROWSER_JS, BROWSER_CONSOLE, BROWSER_NETWORK, BROWSER_RESIZE_WINDOW, BROWSER_FIND, BROWSER_EXTRACT_LIST, BROWSER_DRIVE_INSPECT_FOLDER.

**Local capabilities:** Stable Diffusion on CPU (sd.cpp + LCM + TAESD), Piper TTS, Whisper STT, harvest cache (records every call, serves near-duplicates with no model call), blended web search across five engines (three keyless), SQLite FTS5 index over conversation + memory.

**Memory + identity:** Markdown-file memory system the agent reads every turn. Modelfile teaches identity, reasoning discipline, safety gates, routing hints. A sibling CLOUD_SYSTEM prompt mirrors the same rules for cloud lanes — both surfaces must match or the cloud lane silently diverges.

**Anthropic-aligned safety primitives (Phase 1 + 2 shipped):** typed action envelope, JSONL audit trail, irreversible-action heuristics (purchases / deletes / auth / sensitive-fill / purchase-URL) that gate before execution, BROWSER_SCREENSHOT for verification, policy gating at confirm_run, self-modification denylist, BLOCKED-feedback injection so cloud lanes don't hallucinate success when a safeguard fires.

## What works today, honestly

- One-shot questions answered; router picks local or cloud per prefix or content heuristic.
- File find / read / edit on simple targets ("where is X", "open file Y", "edit function Z in W").
- Shell command execution with mode-aware confirmation.
- Single-step browser actions when the page is unambiguous (one input, one button).
- Local image generation, no internet needed.
- Voice in / voice out, both local (Whisper + Piper).
- Persona stays consistent across tmux and browser side panel.
- Memory persists across sessions.

## What is uneven or broken — the operating gap

This is the part I most want feedback on. Concrete failure modes from real sessions:

1. **Browser auto-fire loop.** The Chrome extension's side panel kept POSTing `/chat/continue` after the model emitted DONE, because the gate at `continueLoop()` checked `state.loop.stopped/active/turn_id` but ignored `done === true` and `terminal_reason === "no_actions"`. One user prompt fired 17 separate turns through a cloud lane. Fix landed; root question is why the gate wasn't there from the start.

2. **Wrong-page navigation.** "Open YouTube" sometimes lands on a channel rather than the homepage. URL choice is heuristic rather than grounded in interpretation.

3. **Missed accuracy on directive emission.** The local 7B sometimes writes prose for "where is X" / "find X" / "what's on port N" instead of emitting `RUN:` / `READ:` directives, even though the Modelfile teaches them. Deterministic short-circuit and retry-on-prose help but the underlying issue may be that a 7B simply can't carry this much rule-following reliably.

4. **Cloud-lane synthesis after RUN/READ is missing.** Model emits `RUN: jq …`, dispatcher runs it, result lands in the journal — but the chat UI shows only the bare command line. No second model call to turn the result into a user-facing sentence. The BROWSER_* lane handles this correctly; the RUN/READ lane does not.

5. **Concurrency contention.** Ollama serves one request at a time per model. When multiple agents (e.g. a coding CLI, a personal assistant, the local TUI) hit the same model, requests serialize. Cloud-lane requests can also queue behind a local-lane inference holding a coarse lock — observed latency of 58 seconds for a one-word cloud-fast acknowledgement.

6. **Conversational acknowledgments reopen action loops.** "nice" / "ok" / "cool" classify as continuation triggers in some paths, firing a fresh round when the user was just acknowledging.

7. **No formal skill / discipline abstraction.** Multi-step workflows (form-fill, multi-page ATS, structured email composition) are derived per-turn by the model rather than loaded as a packaged skill. Competing personal-AI platforms (e.g. OpenClaw) ship 50+ bundled skills that carry the multi-step state explicitly. The gap here is the orchestration layer, not the tool inventory.

8. **No file-upload primitive for multi-page form scenarios.** The browser tool set is missing a "push a file into an `<input type=file>` element" directive. Real use cases that require this (resume uploads, attachment uploads, document submission) fail at the file-input step.

## The framing that organizes this critique

The orchestration layer is operating at a "6-year-old" capability level: it knows the tools but doesn't know how to wield them in sequence reliably. Comparable extensions from major AI vendors operate at "21-year-old" — same tools, much better discipline. The gap is workflow / state / discipline, not tool count.

Adopted rule: improve accuracy of what is shipped before adding new tools or capabilities. Additions are justified only when the existing surface genuinely cannot do the thing AND the addition orchestrates existing capability rather than duplicating it.

## What I'm looking for from you

1. **Architectural critique.** Where is this design weakest? Where would you re-architect?
2. **Sourcing pointers.** What documents, GitHub repos, papers, or patterns from Anthropic, OpenAI, Google / Gemini, Meta AI, or independent research should the author be reading? Be specific.
3. **Failure-mode triage.** Of the 8 gaps above, which would you fix first and why? Which two or three give the biggest "raise the age" per unit of effort?
4. **Honest comparison.** Where is this product overstating what it does? Where is it understating?
5. **Wedges that might be missing.** What small, focused improvement would have an outsized effect on the day-to-day feel of using this system?

Honest replies are more useful than polite ones. Tell me what is wrong.
