---
name: Cloud lane prefixes are provider-named, not model-named
description: When adding a new cloud router prefix to Master AI's Sensei, name by provider (`fireworks:`, `groq:`), not specific model (`dsv3:`, `kimi:`).
type: feedback
originSessionId: 134c8c08-6a45-4a61-81f3-bafcf73e16b9
---
When wiring a new cloud lane into Sensei's prefix dispatch, the prefix is named after the **provider**, not the specific model. The provider is the constant; models swap behind it as defaults change.

**Why:** 2026-04-27 — when adding Fireworks/DeepSeek V3.1, I proposed `dsv3:` (model-flavored). Elijah went with `fireworks:` (provider-flavored). Two reasons he gave: (1) the prefix surface stays small as new Fireworks models come and go, (2) it matches the provider-buttons pattern on the Pupil side (Groq, OpenRouter, Fireworks — provider names, not model names). The model-flavored variant would force a new prefix every time the default model changes.

**How to apply:** Future cloud-route additions (xAI, Mistral, Cohere, etc.) → prefix is the lowercase provider name + `:`. The default model lives in code (and is overridable later via a `--model` arg or settings), not in the prefix. Don't spawn `kimi:`, `gpt5:`, `claude4:` style prefixes; those become stale fast and clutter the help card.
