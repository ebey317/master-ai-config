---
name: Verify routing changes with no-edit probe phrases
description: When testing a routing/orchestrator/policy fix that triggers on a specific word, send a tiny "route/context probe only:" phrase containing the trigger but no edit verbs/file paths. Avoids the model accidentally trying to edit something during validation.
type: feedback
originSessionId: 7834c79c-e693-4399-af54-dbf4f7da69b5
---
Routing fixes need live validation — but the natural test phrase is often the original failing prompt, which may include edit verbs ("edit X", "add a comment to Y") that put the model in an action posture. A successful routing fix can still let through an UNwanted side-effect edit.

**Rule:** test routing/policy changes with the prefix `route/context probe only: <text containing the trigger word>`. The "probe only" framing tells the model we want classification, not action. The trigger word lives in the body; the prefix neutralizes the action posture.

**Why:** Elijah called out 2026-05-03 evening that "edit CLOUD_SYSTEM in master_ai.py to add a comment" risks an unnecessary file edit just to validate the slicer. Switching to `route/context probe only: explain CLOUD_SYSTEM in master_ai.py` exercises the same routing logic without action risk.

**How to apply:** for every live routing/orchestrator validation, ask: "if my fix routed correctly AND the model decided to act, would the action be safe?" If no, prefix with `route/context probe only:`. Examples that worked today:
- `route probe only: so Groq sees it directly` (validated vision-misroute fix in 82d11d8)
- `use traceroute to show the network hops to api.groq.com` (validated BLOCKED-feedback in 45f6072 — explicitly asking for a missing tool, no edit risk)
- `route/context probe only: explain CLOUD_SYSTEM in master_ai.py` (proposed for slicer validation in 94cd4f9)

The phrase shape isn't sacred — what matters is removing edit verbs from the surface while keeping the trigger word intact.
