---
name: Cloud lanes hallucinate success when safeguards block locally
description: Any local safeguard that prints BLOCKED to terminal but doesn't append to LLM history causes cloud lanes (Groq, Fireworks, DeepSeek, Gemini) to assume the next turn that the command ran. Same architectural class as project_pending_identity_self_reference_fix.md.
type: feedback
originSessionId: 7834c79c-e693-4399-af54-dbf4f7da69b5
---
When Sensei's safeguard layer (confirm_run, cleanup-safety guard, etc.) refuses a directive with a BLOCKED message, that refusal goes to the user's terminal but not to the LLM's conversation history. On the NEXT user turn, the cloud lane (especially Groq/Fireworks) sees a plain user message with no failure context and answers as if the prior command succeeded — hallucinating output, claiming "Result: X" lines, recommending follow-ups that depend on the blocked work having happened.

**Why:** cloud lanes don't see local execution context unless it's in the message history. The `process_reply()` chain-abort path was just printing BLOCKED + returning, never appending anything to history. Same root pattern as `project_pending_identity_self_reference_fix.md` — cloud lanes are blind to local-only signals unless explicitly fed.

**How to apply:** when designing any local safeguard that refuses to execute a directive, also feed a synthetic user-role message like `[TOOL BLOCKED]\nRUN command was refused...\nCommand: ...\nReason: ...\nChoose an alternative or propose minimal install. Do not assume the command succeeded.` to history before returning. Pattern lives in commit `45f6072` (`_LAST_BLOCKED_ACTION` global + consume in `process_reply`'s chain-abort branch).

**Caveat — deterministic short-circuit routes don't get this retry.** When a route bypasses the LLM entirely (e.g. weather route, system-query short-circuit), the BLOCKED-feedback chain doesn't fire because there's no model turn to re-prompt. Surfaced 2026-05-03 when Codex's deterministic weather route emitted a complex shell command that tripped the hallucination guard — no recovery loop for that case. Fix is to make safeguards permissive enough not to false-positive on legitimate compound shell expressions (commit `88614d0`).

**Validated in production 2026-05-03 evening:** weather query first attempt was BLOCKED (hallucination guard false-positive on `loc=$(curl -fsS ...)`), retry via fireworks emitted simpler `curl -s "wttr.in?0"` which ran to completion with the 3-day forecast. Recovery happened end-to-end without user re-prompt.
