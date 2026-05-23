---
name: feedback_proactive_sourcing
description: Before proposing architecture for Master AI internals or any agent/extension/automation design, canvas prior art across the full AI vendor landscape (Anthropic, OpenAI, Google/Gemini, Meta AI, plus relevant GitHub repos). Cite at least one source per architectural recommendation. Behavior change born from Elijah losing days re-discovering documented patterns himself. Expanded 2026-05-17 from Anthropic-only to cross-vendor + GitHub.
metadata:
  type: feedback
---

Before recommending architecture, search for prior art across the AI vendor landscape — NOT just Anthropic. Master AI lives in a market where every major vendor has published agent / extension / browser-driving / document-field patterns. The relevant published surfaces:

- **Anthropic** — Claude for Chrome process spec, computer use, Managed Agents, Claude Code internals, prompt caching, citations, batch API.
- **OpenAI** — Operator / Atlas browser agents, Assistants API with tools, function calling spec, Realtime API, file_search / code_interpreter, structured outputs.
- **Google / Gemini** — Gemini grounded search, multimodal live, code execution, function calling, Project Astra / Mariner agent patterns.
- **Meta AI** — Llama agent patterns, CodeLlama tooling, published agent eval benchmarks, Meta AI assistant browser surfaces if/when documented.
- **GitHub repositories** — there's a long tail of open-source mirrors and forks (`open-claude-in-chrome`, `claude-brainrot`, agent registries, MCP server collections) that surface practical patterns ahead of vendor docs. Search with topic+vendor name + "agent" / "extension" / "tool use" before committing to an approach.
- **Independent research / community** — LangChain agent docs, Mistral agents, xAI Grok agents, the agent-benchmark literature (SWE-bench / WebArena / AgentBench results).

Cite at least one source per architectural recommendation. Surface the link in the reply with a one-line summary of what each says. If a source contradicts your instinct, name the contradiction instead of papering over it.

**Why:** 2026-05-17 — Elijah named this as a real error pattern. He spent days studying GitHub repos and AI-vendor process docs himself because I was diagnosing with what I already knew, not pulling fresh sources. The published Claude-for-Chrome process doc in particular had answers we should have started with. Original version of this rule scoped only to Anthropic; Elijah corrected: "don't just look at Anthropic also look at topics from Meta AI and ChatGPT. We want to look at all the AI documents who have extensions or tools or browsers something that can work into end document field. We want to look at GitHub repositories. We wanna source all the information we can before we just take one and say OK let's build it. That's what you're here for — to thoroughly search."

**How to apply:**

1. At the start of any architecture-shaped task (new capability, new policy, new safety gate, new routing path, new agent split, new automation surface), pause before drafting. Open WebSearch / WebFetch on the named area. Spend one tool-call on prior art before spending a tool-call on code.
2. **Canvas, don't grab first.** Pull from 2-3 vendors minimum so you see the spread of approaches. Pick ONE path with evidence after canvassing, not before. "Anthropic does X" is incomplete; "Anthropic does X, OpenAI does Y, the open-source consensus is Z, for our constraints X fits because..." is sound.
3. In the reply, surface the source(s) explicitly: link + one-sentence summary + how it shaped the recommendation. Elijah needs to see the path from "what's published" to "what I'm proposing" so he can challenge it.
4. When the recommendation contradicts a public pattern from any major vendor, lead with the contradiction. "OpenAI's Operator gates on X. Anthropic's Claude for Chrome gates on Y. We're proposing Z because our constraint is W." Honesty beats agreement.
5. **GitHub repos count as prior art.** Even unofficial forks of vendor extensions surface practical solutions to problems we'd otherwise re-derive. Search GitHub by topic + keyword before declaring "no prior art."
6. Bug-fix work and routine code changes are exempt — this rule fires on **architecture**, not edits. Threshold: if the change introduces a new capability, a new agent boundary, a new safety policy, or a new external integration, sourcing applies. If it edits a single function, it doesn't.
7. Failure mode is silent: if no published prior art is found after a real canvas, say so explicitly in the reply ("Searched Anthropic / OpenAI / Gemini / Meta / GitHub for X; no public pattern found, building from first principles"). Don't fake citation; don't silently skip.

Related: [[feedback_real_fixes_not_option_menus]] (one root-cause fix, not a buffet — sourcing supports this by grounding the one fix in evidence). [[feedback_codex_multi_step_standard]] (six-step pattern — sourcing is a layer beneath step 1, root-cause). [[feedback_improve_before_add]] (improvements rarely need a sourcing pass; additions almost always do).
