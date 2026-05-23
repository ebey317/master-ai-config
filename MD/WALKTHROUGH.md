# Master AI — System Walkthrough (Index)

Operator-requested walkthrough of every config, app, and code detail. Index form: one paragraph per piece with file paths and what-it-does. Drill into source for full content.

Generated 2026-05-19.

---

## 1. Product layers

**Brand:** Master AI. The product on the shelf.

**Brain (master_ai):** Local Python application. Runs `qwen2.5:7b` (master-ai:latest tag) via Ollama by default, with cloud escalation per-message (Groq / OpenRouter / Gemini, BYOK). Installer creates two terminal commands: `master` (menu portal) and `sensei` (the agent).

**Hands (chrome extension):** Manifest V3 extension at `~/scripts/sensei_extension/`. Dispatches `BROWSER_NAV` / `BROWSER_FILL` / `BROWSER_CLICK` / `BROWSER_UPLOAD` / `BROWSER_READ_PAGE` / `BROWSER_SCREENSHOT` against the user's logged-in tabs. Native messaging back to the brain over localhost.

**Wiring:** MCP (Model Context Protocol) inside the customer install. Nothing else ships.

> See `~/scripts/ARCHITECTURE.md` section 1 for the canonical statement of this.

## 2. Top-level layout — `~/scripts/`

215 entries. Key files and what they do:

- `master_ai.py` — the brain. tmux-hosted agent (`sensei`), STT/TTS/routing, executor, BLOCKED safeguards, audit log. ~7000 lines.
- `stt_server.py` — Pupil browser-UI backend on port 8080. HTTP API: `/health`, `/status`, `/chat`, `/events`, `/mode`, `/voice`. Owns the `_API_HANDLE_LOCK` that the Ollama wedge contests.
- `pupil.html` — browser UI served at `localhost:8080/pupil.html`. Image/chat/voice tabs.
- `sensei_tui.py` — the terminal UI shell. Mode chrome (Plan/Review/Auto stoplight colors), status bar, dispatch hooks.
- `master.sh` / `launch_master_ai.sh` — entry points. `master` opens menu; option 4 = sensei direct (no dojo gate).
- `Modelfile-master-ai` — Ollama model definition for `master-ai:latest`. Local model = qwen2.5:7b base + the SYSTEM instructions, CLEANUP SAFETY rule, SYSTEM-STATE QUESTIONS exception, RESULT HONESTY rule, TOOL INSTALL POLICY.
- `install.sh` — installer for buyer bundle. Sets up `~/.local/bin/master` + `~/.local/bin/sensei`, PATH, dojo gate removal.
- `pack_for_sale.sh` — bundles buyer-safe zip with secrets scrubbed.
- `sensei_selftest.sh` — 17-phase self-test gate before pack. Includes Phase 16 (safety acceptance, 45 unit tests).
- `harvest.py` — reuse cache layer. Records local + cloud calls; serves near-duplicates from cache; provides few-shot examples.
- `hooks.py` — pre/post event hook system. `hooks.fire(event, action, ...)`.
- `subagent_registry.py` — typed sub-agents (code_reviewer, context_inspector, directive_simulator, file_finder, spend_reporter, test_runner).
- `observability.py` — `summarize()` powers `stats` CLI + Pupil `/metrics`.
- `typed_actions.py` — internal envelope for `RUN`/`RUNTERM`/`READ`/`CREATE`/`EDIT`. Currently audit/preview; full dispatch refactor pending.
- `router.py` — `route()` returns normalized RouteDecision (chat/code/filesystem/current events/vision/etc.).

## 3. Chrome extension — `~/scripts/sensei_extension/`

MV3 layout. Key files:

- `manifest.json` — permissions, side_panel + content_script registration, native_messaging host.
- `content_script.js` — DOM dispatcher in the user's logged-in tab. Receives directives and acts. Handles BROWSER_* tokens.
- `native_messaging/` — host scripts that bridge extension ↔ master_ai backend over localhost.
- `options.html` / `options.css` / `options.js` — settings page (approved sites list when Phase 4 lands).
- `blocked.html` — interstitial shown when irreversible-heuristics gate blocks an action and user must approve.
- `DEPLOY.md` — load-unpacked + native host install instructions.
- `install_native_host.sh` — registers the native messaging host with Chrome.

Anthropic-aligned phases (per AI_CONTEXT 2026-05-19 06:05):
- Phase 1+2 shipped (BROWSER_SCREENSHOT end-to-end, always-confirm heuristics for purchases/deletes/auth/sensitive_fill).
- Phase 3 deferred (auto-mode flow-through in side panel).
- Phase 4 deferred (first-touch site permission prompt).
- Phase 5 deferred (`<PLAN>` envelope + Approve-All card).
- Phase 6 deferred (mode-name copy alignment).

## 4. 3-file harness — `~/scripts/sensei_3file/`

Local executor for routine 80% work. Built 2026-05-19 from Gemini brainstorm.

- `orchestrator.py` — runs one atomic step per invocation against `qwen2.5:3b`. Parses `master_plan.md`, composes `active_step.txt`, calls Ollama HTTP, parses `FILE:/===BEGIN===/===END===/LOG:` block, runs verification, ticks the step.
- `sensei_executor_prompt.md` — verbatim system prompt for the 3B brain.
- `templates/` — `master_plan.template.md`, `active_step.template.txt`, `system_log.template.txt`.
- `README.md` — bootstrapping a workspace + master plan format.

First real run smoked successfully 2026-05-19 at `~/projects/harness-smoke/`.

## 5. Workflow scripts (BC dialogue loop)

- `start_workflow.sh` — verifies listener + tools + cadence + paste coords. Prints dialogue + cadence cards. As of 2026-05-19 the audio routing block was removed; use explicit `rustdesk_audio_on.sh` / `_off.sh`.
- `start_bc_workflow.sh` / `start_ec_workflow.sh` — symlinks to `start_workflow.sh` (BC = EC, same entity).
- `bc_wake_listener.py` — dbus monitor for browser-Claude notifications. Writes `/tmp/bc_reply_ready` sentinel + `/tmp/bc_wake_log.jsonl`. Runs as systemd user unit `bc-wake-listener.service`.
- `bc_title_relay.py` — alternative wake path: localhost:8765 → notify-send. systemd unit `bc-title-relay.service`.
- `tab_wake_listener.py` — generic pixel-diff wake daemon. PARKED (X11 focus blocks TTY delivery; `xdotool type --window` doesn't reach unfocused terminals on this WM).
- `tab_workflow.sh` — `on|off|status` wrapper for the wake listener. Parked alongside.
- `workflow_cadence.py` — 5-commit cadence counter. PostToolUse(Bash) hook ticks on git commit; every 5th auto-fires AI_CONTEXT snapshot + push + memory reminder.
- `add_workflow_hook.py` — installs the cadence hooks into `~/.claude/settings.json`.

## 6. Audio routing — NEW 2026-05-19

- `rustdesk_audio_on.sh` — load `remote_audio` null sink, set default, move RustDesk capture to monitor. For remote (tablet/phone) listening via RustDesk.
- `rustdesk_audio_off.sh` — restore default sink to bluez/alsa, unload null sink. Back to local speakers/headphones.

Both idempotent. Replace the previously-inlined auto-routing block in `start_workflow.sh` that fired falsely on every workflow start.

## 7. Speech I/O

- `speak.sh` — TTS dispatch (Piper local + cloud fallbacks). Updated 2026-05-19 04:04.
- `master-ai-tts.service` — TTS server on port 5050 (Piper).
- `master-ai-ui.service` — stt_server.py on port 8080 (Pupil UI + chat).

## 8. Systemd user units (live)

```
bc-title-relay.service          active
bc-wake-listener.service        active
master-ai-tts.service           active
master-ai-ui.service            active
master-ai-prewarm.service       activating (Ollama warm-up)
master-ai-env-heartbeat.service FAILED     — investigate; writes ~/.master_ai_env.json
```

The env-heartbeat failure is open debt. Listed in this index for visibility.

## 9. Config / state files (user home)

- `~/.master_ai_settings` — no-mouse / phone-mode flags.
- `~/.master_ai_memory` — saved AI facts (shared across all Master AI apps).
- `~/.master_ai_keys` — API keys (Groq, OpenAI, OpenRouter). BYOK.
- `~/.master_ai_chats/` — chat history (all apps).
- `~/.master_ai_approved` — approved commands allowlist (with TTL + cwd fence).
- `~/.master_ai_router_metrics.jsonl` — route decisions, model calls, cloud fallbacks, command exec.
- `~/.master_ai_audit_typed.jsonl` — structured action audit (RUN/RUNTERM/READ/CREATE/EDIT).
- `~/.master_ai_env.json` — platform/resources/services snapshot (written by failing heartbeat unit).
- `~/.master_ai_creator` — creator-bypass marker; skips dojo gate; removed by `pack_for_sale.sh`.

## 10. Memory + handoff (Claude side)

- `~/.claude/projects/-home-elijah/memory/MEMORY.md` — memory index (auto-loaded every session, truncated at ~200 lines).
- `~/.claude/projects/-home-elijah/memory/*.md` — individual memory files (user/feedback/project/reference types).
- `~/CLAUDE.md` — startup routine (this is what you're reading after the routine fires).
- `~/scripts/CLAUDE.md` — repo-level runtime notes for Codex/Claude handoff.
- `~/MD/` — shared markdown folder; mirrors memory/.
- `~/Desktop/AI_CONTEXT/context_*.txt` — session snapshots (every 5th commit + on-demand `save state`).
- `~/Desktop/AI_CONTEXT/threads/thread_*.txt` — verbatim browser-Claude transcripts saved across sessions.

## 11. Canonical docs

- `~/scripts/ARCHITECTURE.md` — the WHAT (every design decision through 2026-05-18: two-piece architecture, executor framework, audit log, PageSignals, task model v0, atss/ pattern, queued work).
- `~/scripts/DEV_PROCESS.md` — the HOW (dual-agent dialogue, brainstorm-bracket, commit cadence, threads/, framing-as-lever, trigger vocabulary).
- `~/scripts/howwework.txt` — full stack + services reference (READ FIRST per AI_CONTEXT).
- `~/scripts/HARD_LIMITS_README.md` — Sensei NEVER runs sudo.

## 12. Services + ports

| Port | Service | What |
|------|---------|------|
| 11434 | Ollama | local LLM HTTP API |
| 8080 | stt_server.py | Pupil UI + /chat + /status + /metrics |
| 5050 | tts_server.py | Piper TTS server |
| 8765 | bc_title_relay | localhost wake-relay endpoint |
| Tailscale 100.101.249.96:8080 | Pupil over Tailscale | remote access |
| RustDesk 1808427068 | remote screen + audio | tablet/phone access |

## 13. Models

- `master-ai:latest` (sha256:8ed06cc2832f) — qwen2.5:7b + Modelfile-master-ai. The default brain.
- `master-ai:pre-m9` (cc0af9ab9a78) — rollback tag.
- `qwen2.5:3b` — the 3-file harness executor.
- `qwen2.5:7b` — base model.
- `llava:latest` — vision (master-ai-prewarm keeps it warm).
- `qwen3.5:cloud` — cloud variant.
- `kimi-k2.5:cloud` — cloud variant.

## 14. Open debt (snapshot 2026-05-19 06:05 + tonight)

- Ollama wedge — long inference holds `_API_HANDLE_LOCK` in stt_server, hangs all `/chat` including cloud. Need root-cause or per-request circuit breaker.
- `local:` / `fast:` prefix gets buried inside `[API REQUEST]` wrapper when source/page_context set (`_api_prompt` in stt_server). EXTENSION-SIDE NOW per pivot.
- `master-ai-env-heartbeat.service` failed.
- Wake listener X11 focus issue parked.
- Chrome ext Phase 3-6 deferred.

## 15. What's queued architecturally (from ARCHITECTURE.md §7)

- Cycle 3 of `page_signals` — fill_form match loop with gate-then-match logic.
- Audit log follow-ups — `last_failure_ts` + `failures_count` in `audit_log_health`.
- MCP per-domain consent — proper UI to replace the same-origin JS-redirect bypass.
- Beast Mode multi-ATS captures — ZipRecruiter, LinkedIn, Glassdoor, Workday, Honest Jobs.
- Task model expansion — cross-tab routing (shipped v0), persistence, priority, email channel, document channel.
- Read-side filtering — same descriptor + executor pattern applied to gating instead of filling.

---

*This is the navigation index. For full content drill into the file paths listed. ARCHITECTURE.md + DEV_PROCESS.md are the canonical narratives.*
