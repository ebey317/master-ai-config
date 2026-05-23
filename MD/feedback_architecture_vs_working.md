---
name: Don't Conflate "Built" With "Works"
description: Architecture memory and code landings do NOT equal functional reliability. When Elijah uses Sensei and it breaks, that's the real grade — not the logbook of what was coded.
type: feedback
originSessionId: 01013ad7-2ca6-4c2c-a21f-0331a77353a0
---
Architecture memory ≠ working product. A feature can be "landed in code" and still be a D-minus experience when Elijah actually uses it.

**Why:** 2026-04-20 evening, after a full day of logging wins (stoplight colors, i915 fix, recursion fix, profile inheritance), Elijah asked Sensei to find his Tailscale settings. The local model hallucinated `tailscale info` (not a real subcommand) and the plan-mode `yes` handler re-rendered the plan text instead of dispatching the RUN directive. Meanwhile I ran `tailscale status` directly in one tool call and produced clean output in seconds. Elijah called out the contradiction: *"you're talking about packing up to be ready for sale… functional it's a D-minus."* He was right. I had been describing architecture as if it proved reliability.

**How to apply:**
- When summarizing state, separate "what's coded" from "what Elijah has verified works end-to-end." Don't present architecture wins as product wins.
- Don't frame a session as sale-ready or shipping-close unless Elijah-the-user has driven the flows and they held up. His hands on the keys is the only real acceptance test.
- When I run something directly with my own tools and it works, that is NOT evidence that Sensei works — I'm skipping Sensei's orchestrator, local model, and plan dispatcher. Say so when the gap is relevant.
- If Elijah reports a bug while I'm logging "v1.8 in testing," treat the bug as the real grade and adjust the status framing. Testing that finds nothing isn't testing.
- Never use "pack it up for sale" framing until Elijah says the phrase. His rule; respect it.
