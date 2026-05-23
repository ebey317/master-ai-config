---
name: Claude Code + Sensei are two live agents on one box
description: 2026-04-23. Claude Code and Sensei are BOTH live agents — not passive apps. Tool calls, subprocess spawns, context work, model I/O all happen on-box even when the model thinking is cloud. Neither closes during build sessions. The 16 GB i7-6700T is undersized for two simultaneous agents — this is the concrete driver behind the 32 GB RAM upgrade path. Sensei-solo conversation quality validated 2026-04-23 AM.
type: project
originSessionId: 4e17fa5e-95c3-4e24-8a99-1f0bd42a1787
---
**The rule:** Claude Code + Sensei running together is "two live agents," not "two apps." Both are actively taking actions (tool calls, file reads, subprocess spawns, context shuttling) — the load is agent-class, not desktop-app-class. Neither one leaves during a build session. "Close one" is not a solution.

**Why:** Elijah 2026-04-23: *"two apps aren't a lot, two agents are alive. These are too cool to take out of space they're at once."* The product's pitch is "your AI on your hardware" — and he's already extending that by running Claude Code as the builder alongside Sensei as the product. The constraint isn't workflow, it's capacity.

**Validation signal (2026-04-23 AM):** Elijah had a Sensei conversation earlier this morning (before Claude Code came online) that *"was going smooth like a conversation."* Sensei-solo → smooth. Sensei + Claude Code → Sensei starts falling to cloud-fallback via local-inference timeout. Same model, same prompt; the second live agent on the box tips the timing past the 45–90s local timeout window. This is the quality baseline: Sensei-solo is what "good" looks like, and degradation under dual-agent load is a capacity signal, not a Sensei bug.

**How to apply:**
- When Sensei quality degrades during a coding session (slow, rerouting to Groq unexpectedly, stalling), the FIRST diagnostic is "is Claude Code active too?" — not "is Sensei broken?" Don't patch Sensei to solve a capacity problem.
- Don't propose "close Claude Code" as the fix — Elijah needs both. Propose capacity answers (32 GB RAM upgrade, process priority / nice, trimming other concurrent processes like RustDesk — see `feedback_make_it_work_here.md`).
- The 16 → 32 GB RAM path (in the existing HP ProDesk, form factor locked) exists for this reason specifically. Not "more headroom in general" — concretely, "headroom for two live agents."
- Hardware form factor stays ProDesk (see `feedback_make_it_work_here.md` — laptop is OUT permanently).
- When benchmarking Sensei, note whether Claude Code was running. Solo numbers are the real product demo; dual-agent numbers are internal builder numbers.

**What Claude Code actually costs on Madam-Mary (rough, watch for precision):**
- A Node runtime for the CLI + whatever terminal hosts it.
- Every tool call spawns a subprocess (bash / grep / find / python) that competes with Ollama for CPU.
- Context/memory reads hit the filesystem while Sensei is mid-inference — I/O contention on slow disks.
- All together it's not Ollama-class RAM, but it's not zero, and it's enough to knock Sensei past local-timeout when local was going to finish just inside the window.

**Related memory:**
- `feedback_make_it_work_here.md` — scale up the existing box, 32 GB RAM is the upgrade target.
- `project_session_end_2026_04_21.md` — 04-21 freeze pattern was exactly this (Sensei + Ollama + 2× RustDesk + Claude Code + Cinnamon all fighting).
- `project_sensei_always_on.md` — Sensei is designed 24/7 always-on; the agent coexistence problem is structural, not transient.
