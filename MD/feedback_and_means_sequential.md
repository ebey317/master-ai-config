---
name: "And" means ALSO (sequential), not "at the same time" (parallel)
description: 2026-04-23 / strengthened 2026-04-24. When Elijah says "do X and Y" he means ALSO — do them sequentially, one tool call at a time. NO parallel tool calls, period. The 2026-04-23 read-only-probe carve-out was REMOVED on 2026-04-24 because parallelism is where Claude makes errors regardless of resource cost.
type: feedback
originSessionId: 4e17fa5e-95c3-4e24-8a99-1f0bd42a1787
---
**The rule (2026-04-24):** ALL tool calls run sequentially. One tool_use per assistant message. No parallel batches even when the calls are cheap or read-only. Unless Elijah explicitly says "run both in parallel" or "kick them off together," sequence is the only mode.

**Why (round 1, 2026-04-23):** Elijah, after I fired a num_ctx Ollama benchmark + Ollama-registry WebFetch + GitHub-API call all at once: *"and means also perform a separate task, not simultaneously."* Concurrent heavy work recreated the two-live-agents freeze pattern the product is built to avoid.

**Why (round 2, 2026-04-24):** Elijah, after I parallelized three memory file writes I thought were independent: *"do not do them in parallel that's where you make errors do them one at a time."* Even when the calls don't compete for CPU/RAM, parallelism is the source of MY errors — I miss a result, react out of order, double-count, or lose track of which write landed. Resource-cost was never the only reason. Sequentiality is the cognitive discipline.

**How to apply:**
- One tool call per assistant message. Finish, get the result, report briefly if needed, then the next.
- This applies to every tool: Read, Edit, Write, Bash, Agent, WebFetch — everything. No "but these are just reads" exceptions.
- The OLD carve-out for read-only filesystem probes is REVOKED. Don't bring it back.
- When Elijah lists multiple asks ("do X and Y and Z"), do X first, complete it, then Y, then Z.
- The only legitimate parallel batch is when Elijah explicitly says "in parallel" or "all at once."
- Cross-reference `project_two_live_agents.md` — the product thesis is single-agent-gets-the-box. Claude Code must model the same restraint to not undermine its own premise.

**Verification pattern:**
- Before sending a response with tool calls, count them. If count > 1, that's a violation unless explicitly authorized this turn.
- If uncertain, default to sequence. The cost of one extra conversation turn is way smaller than a missed result or an error from out-of-order processing.

**Related memory:**
- `project_two_live_agents.md` — the product-level statement of the same principle.
- `feedback_make_it_work_here.md` — "leave well enough alone" + "revert to working point." Same restraint discipline.
- `feedback_no_polish_creep.md` — related: don't stack operations.
- `feedback_numbered_5wh_before_execution.md` — sequencing tasks makes the numbered-per-action pattern natural.
