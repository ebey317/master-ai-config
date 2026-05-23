---
name: Polish Creep Drags The Ground Process — Don't Add Elements That Don't Serve Speed + Power
description: Small UX flourishes (animations, handshake thoughts, decorative transitions) pile up and drag on the core working loop. Elijah's ground process needs speed + power, not ornament. When in doubt, don't add.
type: feedback
originSessionId: c53f933b-955f-4b1d-8b2b-dc75f15b261b
---
**The rule (2026-04-21):**

Little aesthetic additions — handshake animations, extra thought slots, decorative tweens, "nice" micro-transitions — drag on the core working loop even when each one looks cheap in isolation. The ground process (think → approve → execute) is the product. Anything that doesn't directly serve speed or power is bloat.

**Elijah's exact words 2026-04-21:** *"Music in this, adding these little elements take away from my ground process, speed and power."*

Voice-to-text, rough parse: *"Much in this [set of additions]; adding these little elements takes away from my ground process, speed, and power."*

**Why this rule matters more than any single animation:**
The pattern is: user asks for something (the color flip, the handshake visibility), Claude validates it works, Claude then proposes "next we could animate X, add a transition Y, show a thought Z." Each proposal looks small. Stacked, they smother the core loop. Elijah's build philosophy: strip everything that doesn't earn its spot in speed or power. A working color flip earns its spot. An animation layered on top of that flip does not.

**How to apply:**

- Default answer to any "we could also add a polish element here" is **no**. Don't build until Elijah explicitly asks.
- When a feature already works (e.g. the Plan→Review color flip, validated "beautiful"), STOP. Don't propose to gild it.
- When Elijah asks for X, deliver exactly X. Don't pitch X+Y+Z on the same turn.
- Animations, tweens, fade-ins, progress ribbons, decorative thoughts — all belong in the "propose only if asked" bucket.
- Functional fixes (bugs, routing errors, safeguards, real isolation of already-existing channels) are NOT polish. Fix those without hesitation.
- The distinction: does this addition make Sensei do its actual job faster or more reliably? If no → skip. If yes → do it.
- Corollary: if you've already shipped 2 "small" UX additions in a session, stop proposing more even if they seem isolated and cheap.

**Related:** `feedback_handshake_animation_mode_isolation.md` (the isolation rule for thought channels — THAT still stands because it's about mutual exclusion, not adding new elements), `feedback_give_it_a_home.md` (features need a home, not disembodied growth).
