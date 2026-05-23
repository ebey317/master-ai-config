---
name: Do not claim Sensei learned when Claude or Codex injected a rule
description: Do not claim Sensei "learned" something when Claude/Codex wrote a feedback memory and sync_hard_limits.py injected it. Sensei applies rules from ~/.master_ai_memory; she does not yet author them from her own mistakes. Real learning loop is unbuilt: remember: direct memory, directive-repair harvest pairs, harvest-to-draft memories, then fine-tune data.
type: feedback
originSessionId: 019defe9-9233-7843-962d-8dbd742b032b
---
Do **not** say Sensei "learned" a behavior when what really happened is Claude or Codex wrote a rule and `sync_hard_limits.py` copied it into `~/.master_ai_memory`.

Current truth: Sensei applies memory rules. She does not yet reliably author those rules from her own failures.

**Why:** On 2026-05-03, the matrix/weather/demo discovery rule was written by Codex after a failed natural-request test. That was useful, but it was an immediate behavioral patch, not autonomous learning. Future sessions must not blur that line.

**Correct wording:**
- "The rule was added to Sensei's memory."
- "Sensei now has an injected guardrail for this."
- "This is a teacher-written patch, not learned behavior."

**Wrong wording:**
- "Sensei learned from the matrix rain failure."
- "Sensei taught herself to grep `~/scripts`."
- "The harvest loop learned the correction."

**Order to close the real learning loop, when ready:**
1. `remember:` writes directly into Sensei memory. This is a lower bar than the current Claude/Codex -> memory bridge round-trip.
2. directive-repair emits learnable failure/correction pairs into harvest.
3. harvest promotes repeated repaired patterns into draft memories.
4. corrected harvest pairs eventually become fine-tune data.

**Current state as of 2026-05-03:**
- Step 1 partly exists via Sensei's `remember:` prefix.
- Steps 2-4 are unimplemented.
- The matrix/weather/demo scripts-before-create rule shipped today is an immediate behavioral patch, not autonomous learning.
