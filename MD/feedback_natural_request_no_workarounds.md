---
name: Natural requests must work end-to-end — never propose pre-made scripts as workarounds
description: Master AI/Sensei's whole premise is "natural language → AI handles it." If a natural request like "make a 30 second video" fails, the fix is in Sensei/model/routing — never in pre-baking the command for the user. Pre-made scripts hide the failure and defeat the product.
type: feedback
originSessionId: 70696e88-5f85-4999-b10b-463752c63aca
---
When a natural-language request to Sensei fails, do **NOT** offer to "just write the script for him" or "produce a reference file" or "bake the command into a snippet." That bypasses Sensei and proves the OPPOSITE of what Master AI is supposed to demonstrate.

**Why:** 2026-04-26 — after the bunny video request failed twice (broken bash from local 7B; cloud routing fix shipped but not yet retested), I offered to write a working Python generator myself as a "reference MP4" and "proof the recipe is correct." Elijah called it out: *"must be a natural request not a pre made command. make a 30 second video is basic."* The product pitch is literally "Master AI is for anyone who has ever asked... and it responded paste this in your terminal." If I solve it by handing him a pre-made script, I am the failure mode the product exists to fix.

**How to apply:**
- When a Sensei flow fails on a natural ask, the diagnostic question is "where in Sensei's path did this break?" — NOT "how can I produce the artifact for him?"
- Acceptable next moves after a failure: bake a RULE into `~/.master_ai_memory` so the next attempt has better instructions; adjust routing in `master_ai.py`; upgrade the model lane (cloud vs local); add a verifier loop. All of these stay inside the Sensei pipeline.
- NOT acceptable: writing the working artifact to disk myself; giving Elijah a copy-paste command that bypasses Sensei; producing a "reference file" he could imitate. These hide the gap.
- The natural-request acceptance test is "does Sensei produce the artifact when asked in plain words?" — if no, the work is in Sensei, not in me.
- Exception: if Elijah explicitly says "you do it" or "just make the file yourself" — that's a different request and I should comply directly.
- Memory bake-ins (rules in `~/.master_ai_memory`) are NOT pre-made scripts — they're instructions to Sensei's model. Different layer. Allowed.
