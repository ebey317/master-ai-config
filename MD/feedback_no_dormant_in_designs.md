---
name: Don't name dormant / placeholder code in design proposals
description: When proposing routing, architecture, or feature changes, only reference answerers/components that actually ship and work today. Dormant hooks (e.g. Scrappy — a no-op until a fine-tune is pulled) are NOT live answerers and must not appear in routing lists or handoff diagrams. They add scope, muddy the mental model, and lead to code that's designed around things that don't exist.
type: feedback
originSessionId: 11fd1fb3-8c8a-4bf3-a76a-4a25a90390a4
---
When proposing routing, architecture, or handoff designs, only list components that currently ship and work. Dormant hooks and placeholder code stay out of the proposal.

**Why:** Elijah 2026-04-21 — "there should be no mention of scrappy scrappy is just an idea. See what I mean by how you're just going around doing stuff that's why my shit is messing up now." He connected my habit of dragging in adjacent/dormant pieces (Scrappy, for example) with the current messy state of his codebase. Including non-working pieces in a design expands scope silently, implies they work, and pushes him to reason about code that doesn't actually run.

**How to apply:**
- Before listing "answerers" / "routes" / "components" in any proposal, verify each one has a working code path TODAY. If it's gated behind "if model pulled" / "if key present" / "when we build it," name it once as future-work and stop, don't include it in the core design.
- Scrappy specifically is a placeholder — a hook that no-ops when no `scrappy`-tagged model is pulled. It is NOT part of the live router for Master AI. Do not name it in routing proposals, handoff descriptions, or code changes.
- Same rule applies to any other dormant feature: chunker (archived), LOCAL_SYSTEM (dead code), Modelfile-master-ai at 14b (stale), study-sessions (not-yet-built), face/bubble for Claude (not-yet-built).
- When narrowing the live answerer set for Master AI right now, the real list is: master-ai (qwen2.5:7b local), groq (cloud if key), deepseek-r1 (cloud if key), qwen3.5:cloud (cloud-via-Ollama if key), llava (local vision), kimi-k2.5:cloud (vision-cloud if key), gemini (web + vision if key). That's it. Scrappy isn't in the list.
