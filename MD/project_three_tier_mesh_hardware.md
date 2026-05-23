---
name: Three-Tier Hardware Mesh Plan (STANCE SHIFTED 2026-04-22)
description: Hardware mesh plan drafted 2026-04-21 with laptop as primary. Stance shifted 2026-04-22 — make Madam-Mary work first, scale up after. Laptop demoted from active plan to future option. See feedback_make_it_work_here.md.
type: project
originSessionId: c53f933b-955f-4b1d-8b2b-dc75f15b261b
---
**Stance update 2026-04-22:** Elijah reversed the "buy a laptop" posture. The new default is: make Madam-Mary work for what's being asked of her, identify known-working points, and revert to them when a tune breaks them. Laptop is no longer the first-tier answer to freezes or slow inference. Freezes are usually from concurrent load (RustDesk + Cinnamon + Claude + Ollama) not raw capacity — software-side fixes come first. The plan below remains on file as the long-term mesh shape but is NOT the active buy-list. See `feedback_make_it_work_here.md`.

---

**The plan (2026-04-21, on pause):**

Elijah decided NOT to upgrade madam-mary (the i7-6700T mini-PC) to 32 GB RAM + 4 TB NVMe. Instead: buy a new laptop as the primary Sensei-always-on box, keep madam-mary as a secondary mesh node, phone continues as the remote-access tier.

**The three tiers:**

1. **Laptop (NEW, to be purchased)** — primary. Sensei open all day every day. External NVMe for model storage. Runs the heavy models (7B warm, possibly 14B once hardware lands).
2. **Mini PC (madam-mary, existing)** — secondary node. Stays on tailnet at `100.101.249.96`. Holds portable USB live-boot for "Master AI anywhere" demos. Runs lighter models (3B / llava) and serves Pupil to any device on the tailnet.
3. **Phone (iphone175)** — remote access tier. Voice-to-text input via RustDesk / Pupil / Tailscale. Doesn't run models, consumes them.

**Why:**
Elijah 2026-04-21: *"I may not update [madam-mary] now that I know I got a phone tier a mini PC tier and I gotta have a super computer"* and *"I'm thinking about keeping the mini PC — want to go buy an actual laptop with an external hard drive and doing all of this. I will keep Sensei open on the laptop all day every day."*

The i7-6700T is CPU-bound for token generation regardless of RAM. A newer H-series CPU with DDR5 + discrete GPU will beat a RAM upgrade dollar-for-dollar. Plus the laptop adds a mobility dimension madam-mary can't: Sensei travels.

**Spec targets for the laptop (Claude's advice, Elijah confirming):**

- **RAM:** 32 GB minimum, 64 GB preferred (two 7B models + llava warm simultaneously)
- **CPU:** Recent H-series — Ryzen 7 7840HS / Intel 13th-14th gen H-series
- **GPU (biggest single win):** Discrete with 8+ GB VRAM. RTX 4060 mobile → 7B runs 5-10× faster than CPU. If budget bends, get this first.
- **External drive:** NVMe in USB3.2 Gen2 or Thunderbolt enclosure. Spinning HDD kills cold-load feel (2-5 min per model). $30-50 enclosure upgrade is not optional.
- **Ports:** Thunderbolt 4 / USB4 for the external drive bandwidth
- **Networking:** WiFi 6E minimum, wired ethernet preferred (Mesh stability)
- **Thermals:** "mobile workstation" class (Thinkpad P-series, HP ZBook, Dell Precision) or gaming laptop with real cooling. NOT an ultrabook — ultrabooks throttle under sustained Ollama load.

**24/7 operation checklist (after purchase):**
- Power settings → "do nothing" on lid close while plugged in
- Power settings → "never" sleep/idle while plugged in
- `systemctl --user enable master-ai-ui.service` (+ siblings) for autostart
- `loginctl enable-linger $USER` so user services run when logged out
- Cooling pad or hard surface — not bed/couch
- Tailscale on from first boot — same tailnet as madam-mary + phone

**Mesh integration (already built, just needs to wire up new node):**
- `mesh.sh add <laptop-hostname>` to register the new node
- Laptop becomes the default inference target; madam-mary + phone query it via `/ask`
- Config lives in `~/.master_ai_mesh.json`

**How to apply:**
- When Elijah asks for upgrade/hardware advice, default to "laptop first, keep mini-PC" not "upgrade mini-PC."
- Do not re-suggest the 32 GB + 4 TB upgrade for madam-mary — that plan is superseded.
- Sensei's always-on design (`project_sensei_always_on.md`) now targets the laptop, not madam-mary.
- When the laptop arrives, update `project_master_ai_state.md` with the new primary-node hostname + IP.
