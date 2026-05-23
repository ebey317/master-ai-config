---
name: No Claude API in Master AI
description: Don't add Anthropic/Claude API to Master AI — user only uses paid Claude Code, everything else in Master AI must stay free
type: feedback
originSessionId: 40445963-46e1-46f3-82c6-257d45f0bc25
---
Do NOT add Claude API (Anthropic SDK or direct HTTP) as a selectable model in Master AI, and do NOT suggest adding an `anthropic` key to `~/.master_ai_keys`.

**Why:** User pays only for Claude Code. When they say "I upgraded Claude Code" or "upgrade my account," they mean more usage *inside Claude Code itself* — not adding Claude API to other tools. Master AI must stay free-only (local Ollama + free-tier cloud: Groq, Gemini, OpenRouter). Previously I misread "upgrade claude" as a request to wire Claude API into Master AI; the user rejected it and asked for a revert.

**How to apply:** If Master AI needs another model, pick free options (OpenRouter free tier, Groq, Gemini). If the user mentions upgrading their Claude/Anthropic plan, assume it's about Claude Code usage limits unless they explicitly say otherwise.
