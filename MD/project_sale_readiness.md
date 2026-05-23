---
name: Master AI — Sale-Readiness & Pricing Snapshot
description: Honest grade of the current Sensei/Pupil state and pricing thinking. Upgrade-vs-Claude in places, but NOT shippable until multi-user lands.
type: project
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---
## Current state (2026-04-18, post-session 6)

**One-line verdict:** Master AI is a legitimate local-first AI stack with real design identity — upgrade-level in some areas (dojo workflow, key auto-detect, martial-arts belt theming, task boards), but **not shippable for paid sale until multi-user works**. Elijah's own line: *"we're getting there."*

## Grade breakdown

| Area | Grade | Why |
|---|---|---|
| Design identity (ninja/dojo/belts) | **A–** | Cohesive, unique, memorable. Not generic "AI chat #874" — the belts and dojo metaphor are a real product signature |
| Feature depth | **B** | Dojo gate + task boards + model-per-project + key finder + idle thoughts + drift reminders + PROJECTS.md as source of truth — all thoughtful |
| Polish | **B+** | Ninja animations, rotating phrases, pack-it-up-for-sale ritual, belt themes — more care than most indie AI tools show |
| Market-readiness | **C+** | Multi-user missing, no installer for non-tech buyers, Ollama dependency scares normies, docs are thin |
| Upgrade vs Claude / ChatGPT | **mixed** | Beats them on: offline, no subscription, user-owned data, dojo workflow. Loses on: model quality (14B Qwen < GPT-4), breadth of integrations, zero-setup |

**Overall: B / B+** — genuinely good work, not a toy, but a buyer would hit real friction today.

## Not-yet-shippable gates (tied to "pack it up for sale")

1. **Multi-user** — currently menu option 15 is a profile-dir stub; master_ai.py and pupil.html still read global dotfiles. Until a second user can log in and have their own memory/sessions/keys without touching the first user's data, this is single-seat only.
2. **Installer** — no one-command setup for a stranger. Ollama + pulling models + systemd services is a lot.
3. **Docs** — `howwework.txt` exists but no quickstart, no screenshots, no "here's what you can do in 5 minutes."
4. **Mesh** — the Master AI Mesh vision (node-to-node federation) isn't wired. Stays a solo node product until then.
5. **Licensing / telemetry** — nothing in place to distinguish paid vs trial.

## Pricing thinking (Elijah's target: ~$100, realistic floor: $20)

**Hardware floor for buyers:** 16 GB RAM + ≥ 500 GB disk (2 TB is nice but not required — master-ai:latest + qwen-coder weigh ~15–20 GB total).

**Who actually has that:**
- Gamers on Windows desktops — **~30% of Windows PCs** ship with 16 GB (Steam hardware survey territory)
- Mac users on Apple Silicon — Mac Mini M2/M3 base is 8 GB, but 16 GB upgrade is common; maybe **~30–40%** of modern Macs
- Linux tinkerers — small but self-selecting, likely already running Ollama
- Modern laptops 2023+ — 16 GB becoming standard, 2 TB rare (usually 512 GB / 1 TB)

Rough TAM for "16 GB + enough disk": tens of millions of machines globally, but *buyers who would pay for a local AI UI* is a much smaller subset — maybe **low six figures** of serious prospects.

**Pricing options honestly weighed:**

| Price | Pros | Cons | Verdict |
|---|---|---|---|
| **$100 one-time** | Matches Elijah's ambition; feels like a "product" | Hard sell without multi-user, without installer, without big-name social proof. Customers expect ChatGPT/Claude-level polish at $100 | **Too aggressive right now**. Revisit post-multi-user + installer + mesh |
| **$20–30 one-time** | Low-friction indie pricing; fits "try it, keep it" impulse buy; viable on Gumroad | Leaves money on the table if the product grows; needs volume to matter | **Realistic launch price** — lands the first 100 customers, builds social proof |
| **$5–10/month** | Recurring revenue; justifies ongoing updates; fits "I run my own AI" subscription psychology | Friction of subscription for a local-first tool feels wrong — buyer expects to own it | **Consider for mesh tier** (node federation, multi-user) once built |
| **Free core + $29 "pack it up for sale" edition** | Open source trust + paid convenience; Gumroad + GitHub combo works | Requires two codebases or a build flag | **Good long-term model** once packaged |

## Recommendation (revised per Elijah 2026-04-19)

**Target: $100 A+ app. Non-negotiable.**

Elijah's frame (learn from him): *"I'm not a six-figure guy, but I understand AI can make me millions. An AI that's mine, that knows me personally, that runs on my computer like Claude Code does, with a built-in tutorial — that's worth $100."* He's right. The wedge isn't pricing — it's **what the product actually does**.

The $100 A+ version must deliver, undeniably:

1. **Agentic local coding** — Sensei does what Claude Code does, on your hardware: reads files, edits files, runs commands, plans + executes tasks. Already half-there via RUN/READ/CREATE/EDIT directives. Finish the polish.
2. **AI that learns you** — persistent memory across sessions (have it), project boards (have it), personal learning history (have part via `~/.master_ai_progress.json`), pattern recognition of how you work (not yet).
3. **Real tutorial / curriculum** — NOT a 5-question quiz. Class 1-10 bash, Class 1-10 python, Class 1-5 "AI as a builder's tool." Track mastery. Propose next level.
4. **Multi-user** — menu 15 wired through to master_ai.py + pupil.html actually using per-profile paths. Up to 4 per node.
5. **Remote access (the Tailscale wedge)** — phone + desktop + guest device all hit the same Sensei + Pupil instance. Auto-sync via `/keys`. Done right, this alone justifies the $100.
6. **Installer** — one-line bash install that pulls Ollama, pulls models, wires systemd, opens Pupil. No manual anything.
7. **Docs that teach** — quickstart, video script, a "tour" doc, reference for every command. Part of the sale is the docs.

**At that level the $100 is honest.** Not aspirational — justified.

**Grade posture going forward:** the grade I give in this memory is **current state** + **distance to A+**. Today's B/B+ is the starting line; each landed milestone from the list above moves the grade. Don't pretend the grade is higher than it is, AND don't undersell the target — Elijah set $100 A+, hold him (and me) to it.

## Pricing fallback / tiering (if $100 flagship needs a ramp)

Not "sell cheaper." Instead:
- **$100 — Master AI Full** — everything above. The flagship.
- **$20 — Master AI Pupil** — browser UI + lessons + key manager only. No Sensei agent loop. Teaser tier; upgrades to Full.
- **Free — Master AI Open** — menu + Ollama wrapper, no tutorials, no dojo gate. Lead magnet / open-source trust.

This way Elijah gets the $100 price anchor without forcing poor buyers out — they start at $20, outgrow it, upgrade.

## Why "upgrade vs Claude" in places but not overall

Elijah's right that *in some dimensions* this beats commercial tools — you own the model, no rate limits, no policy layer, dojo workflow is original. But Claude has years of RLHF, a 200k context, tool use, vision, hosted API for others to build on. Master AI doesn't compete on raw capability — it competes on **ownership and sovereignty**. That's a real wedge. Price and market it like that.

## The real wedge (clarified 2026-04-19)

**Tailscale is the right comparison.** Tailscale didn't sell "another VPN" — it sold *your stuff, accessible from wherever you are, without trusting a corporation in the middle*. That's what Master AI is: **your AI, accessible from wherever you are, without trusting a corporation in the middle**.

Elijah's exact use cases for Master AI:
- **On the phone** while out / traveling
- **At home** on the desktop
- **Study mode** — companion while learning something new
- **Produce mode** — working through real builds

One AI instance (Madam-Mary at home) + many access surfaces (phone, second computer, guest device). The Tailscale-like move is that the AI follows the *person*, not the *device*. Sensei on tmux is the core; Pupil is the browser surface; phone + RustDesk is the remote hand.

This reframes the product:
- Not "a ChatGPT alternative" — competing on model capability is a losing fight
- **"Your AI, at every entry point you use, powered by hardware you own"** — competing on sovereignty + presence

**Current state of the wedge:** HTML files (pupil.html + master_ai.html) are close to what Elijah wants. Key finder + `/keys` sync landed this session. Next step per his words: *"fine-tune the app to be what I want it to be, publish it, then sync back."*

**How to apply:**
- When framing features / pricing / messaging, default to the Tailscale-style "your stuff, remote-first, sovereign" framing instead of generic "AI chat" framing
- Remote access across devices is NOT a v2 feature — it's the core promise. Anything that compromises it (e.g. requiring a cloud service to log in) is wrong direction
- When Elijah says "fine-tune," he means *polish the HTMLs + wire remaining keys*, not "add big new features"

## How to apply this memory

- If Elijah asks again about pricing / readiness, use this grade as baseline; update as features land
- When he says "pack it up for sale," take multi-user status + installer status into account before agreeing the product is ready — if they're not done, push back gently with this memory's logic
- Keep the "B/B+" tone — don't flatter and don't crush; this matches Elijah's preference for straight talk
