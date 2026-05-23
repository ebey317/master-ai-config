---
name: Make This Box Work First
description: When Madam-Mary hits performance/freeze limits, default to making current hardware work, not recommending new hardware. Scale up AFTER exhausting what this machine can do.
type: feedback
originSessionId: b4ce21ed-453b-485b-b331-49a6b6168f23
---
When performance, freeze, or capacity issues come up on Madam-Mary (i7-6700T / 16 GB / iGPU), the default response is NOT "buy a laptop." The default is "what software change makes this box work."

**Why:** Elijah 2026-04-22: *"we're not thinking about buying a laptop yet we're gonna make this one work. We're not giving up. We started here. We're going to make it work here and scale up."* The product's whole pitch is "your AI on your hardware" — if the first answer to a freeze is "upgrade," the pitch collapses. Also: the cassette-tape reliability frame ("a machine I keep for 10-20 years") is the wedge; abandoning it for new silicon at the first sign of strain is the opposite of what he's selling.

**How to apply:**
- When he reports a freeze, overload, or slow inference: start with workload questions (what was running concurrently? can anything drop?) and software fixes (context trimming, model swap, process priority, turning off RustDesk).
- Specifically: the 2026-04-21 freeze pattern was Sensei + Ollama + 2× RustDesk + Claude Code + Cinnamon at once. RustDesk is usually the highest-value thing to remove — see the Tailscale-activation path below.
- Tailscale-based activation of Sensei / Master AI from the phone is the thing that kills RustDesk and therefore kills the freeze pattern. If he brings up "I have to use RustDesk because I can't reach Sensei from outside," that IS the feature request — not a laptop upgrade.
- Laptop purchase still exists as a future option (see `project_three_tier_mesh_hardware.md`), but it's no longer the first-tier answer to performance complaints. Only bring it up if he explicitly asks about hardware.
- Same rule for RAM/NVMe upgrades to Madam-Mary — already superseded, don't resurrect unless he raises it.
- **Revert-to-working-point reflex:** Elijah 2026-04-22: *"it was working fine. We were just trying to tune. We can get back to where it was and use that working point."* When a tuning session breaks a known-working state, the first move is identify the last working commit/config and revert — not patch forward. Ask what was changed, not what to add.
- **"Leave well enough alone" once working:** Elijah 2026-04-22: *"this is it was working. I'm gonna get it back to work and leave well enough alone and you are too."* Once a state works, STOP tuning. Don't chase marginal gains that risk the working state. This rule applies to BOTH of us — if I notice a "we could polish X" impulse after a feature works, suppress it.
- **Rebuild escalation requires explicit approval:** If minimum-viable tightening isn't enough and we'd need to rebuild from scratch, STOP and say *"I'm going to rebuild X from scratch using the notes — approve, or tell me to work around."* Verify scope, wait for yes/no. Never auto-rebuild after a block. Work-around is an acceptable alternative — don't frame rebuild as the only path.
- **Form factor is load-bearing — laptop is OUT permanently:** Elijah 2026-04-22: *"I want portability off-grid, plug into a solar panel. I'm not doing that with a laptop. HP ProDesk is what it is — backpack size, about a lunchbox or a CD player. EMP-proof box, fan, battery-powered."* Madam-Mary (HP ProDesk, i7-6700T) isn't just the current box — it's the intended final form. The product's off-grid pitch depends on the device fitting in an EMP-shielded enclosure with battery + solar power. Laptops don't fit that constraint. Don't revisit the laptop path.
- **The upgrade path is RAM, not a new box:** Scale-up target = 32 GB RAM in the existing HP ProDesk (up from 16 GB). Elijah: *"32 GB of RAM is where I need to be looking at for the AI."* Until that upgrade, local qwen2.5:7b on i7-6700T + 16 GB is a known-slow reality; hybrid local+cloud dispatch is the accepted workaround, not a product weakness.
