---
name: Portable Master AI — The Flash Drive Cache
description: Master AI + models + survival/infrastructure knowledge corpus on a USB flash drive. Walk into any town with working hardware, plug in, rebuild. This is the deepest apocalypse-mode form of the product.
type: project
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---
## The scenario, in Elijah's words (2026-04-19)

> "I run into a computer that I need to get in, everybody's dead, nobody's around. I need you to get into that computer or show me how you write code. I can put you in a flash drive and you can get to work and we can turn this power grid back on, get some electric flowing, turn the cold station on."

## HE'S ALREADY DOING IT (context that changes everything)

Elijah's follow-up clarification same day:

> "That's how I got you working a flash drive. I have a flash drive and I got Linux Mint on it. That's and we got here all from my thought."

**Elijah's actual setup is already USB-portable.** The HP ProDesk he's using isn't his "home machine" — it's a host for a Linux Mint USB. Every session, every memory file, every script, every model we built together lives on a flash drive that goes with him.

This changes the advice pattern:
- Don't explain "portable AI" to Elijah like it's new — he invented his own version of it before I showed up
- His `~/scripts/` and `~/.master_ai_*` files live on the USB by default (whatever persistent-storage layer Mint Live uses)
- The hardware upgrade plan (4 TB M.2) is about improving the HOST machine, NOT the portable tool. The tool stays on the USB
- When he walks into "a dead town with a working PC" he's describing a scenario he's already set up for — plug in, boot his Mint USB, his whole Master AI comes with him
- Corpus curation + model pulls should consider: "does this fit on the USB, or does it need to live on the host when that host is available?"

## How to apply

- Assume Elijah's primary operating surface is a **persistent Linux Mint Live USB**, not the HP ProDesk's internal drive
- His data moves with him; the host is interchangeable
- The HP ProDesk upgrade (32 GB RAM, 4 TB M.2) makes a BETTER host, not a new home — the USB is still home
- When advising on model storage: prefer having smaller models (qwen2.5:7b, qwen2.5:3b, llava) on the USB itself; reserve host-only storage for the big models (qwen3:30b-a3b etc) that can be re-pulled if he lands at a new host with internet
- This is the "Tailscale wedge" in even truer form — not just "my AI anywhere I am," but "my AI IS with me, on this stick"
- When he says "the apocalypse scenario" he means a LITERAL walk-around-with-it use case. The product is already sold to him by the fact that he built it for himself this way
- Future Master AI buyers might want the same — doc a "run from USB" mode explicitly; it's a legitimate sales angle distinct from "install locally"

## What this is

**A USB flash drive (128 GB+) containing the complete Master AI stack + a survival/infrastructure knowledge corpus, runnable on any PC with enough RAM (16 GB+).**

Not a backup. Not a bootable Linux. A **cache** — pre-prepared supplies you carry for when the supply chain is dead. Yours happens to contain an intelligence and a knowledge library.

This is the deepest form of Apocalypse Mode: not "my local machine works when the cloud dies" but "I can carry this to someone ELSE'S working machine and rebuild from there."

## What must fit on the drive (~128 GB target)

| Item | Size | Purpose |
|---|---|---|
| Ollama binary (Linux + Windows) | ~30 MB | Runs the models |
| `qwen3:30b-a3b` | ~18 GB | Primary MoE brain (fast on CPU) |
| `qwen2.5-coder:32b-q4` | ~19 GB | Code + industrial systems |
| `llava:latest` | ~4.7 GB | Vision — meter faces, wiring diagrams, machinery ID |
| `qwen2.5:3b` | ~2 GB | Fast quick-response tier |
| Master AI scripts (`~/scripts/`) | ~100 MB | Dojo, gate, Pupil, Sensei, chunker |
| **Survival corpus** (see below) | ~30-60 GB | The differentiator |
| Minimal Ubuntu Live ISO + tools (optional) | ~5 GB | Self-contained boot if host is hostile |
| Spare | ~10-20 GB | Room for docs you generate in the field |

## The survival corpus — what to curate

Public-domain / free-to-distribute references, indexed for offline vector search:

### Electrical & power
- Military FM 20-3 (camouflage, low priority here but cheap) and FM 3-34 (engineer ops)
- US Army Corps of Engineers field manuals
- OSHA regulations — PDF archive
- NEC (National Electrical Code — check licensing) or open alternatives
- University courseware: MIT OpenCourseWare (EE, ME, chem eng intro courses)
- Diesel engine operator manuals (Caterpillar, Cummins generic service docs)
- Wind turbine ops (NREL publications — public domain)
- Solar design (NREL PVWatts docs)

### Industrial controls / SCADA
- Open-source PLC programming docs (OpenPLC, CODESYS community edition docs)
- Modbus protocol full spec (public)
- Ladder logic primers
- Allen-Bradley ControlLogix free training PDFs (public from Rockwell)
- Siemens TIA Portal community docs

### Water / sewage
- EPA operator certification study guides
- AWWA (American Water Works Association) free resources
- WHO drinking water guidelines (public)
- Homestead water systems (open-source homesteader archives)

### Medicine / survival
- WHO essential medicines list + treatment protocols
- Where There Is No Doctor (Hesperian — freely available in print, licensing check needed)
- FEMA emergency medical procedures
- Army FM 4-02 series (combat medic)
- Poison plant ID (USDA public)

### Mechanical / fabrication
- Haynes / Chilton style guides (licensing — open alternatives preferred)
- Machinery's Handbook older editions (public domain eventually)
- Welding ops (AWS free resources, military welding manuals)

### Communications
- ARRL HAM radio basics (some free, some paid — check)
- FCC license study guides (public)
- Emergency frequency tables (public, widely shared)

### Food / plants
- USDA plant hardiness, wild edible guides (public)
- Food preservation (USDA National Center for Home Food Preservation — free)
- Homesteader archives (many public)
- Apothecary scanner tie-in — Herbal Medic references

### Engineering fundamentals
- Wikipedia snapshot — ~20 GB compressed, covers everything as overview
- MIT OCW course videos transcripts (if storage allows)

## Implementation stages

### Stage 1 — Portable run (no boot, host OS provides)
- USB contains the stack; user plugs into host Linux/Windows/Mac
- Runs `./start.sh` (or `start.bat`) from USB
- Ollama spins up on host RAM; models load from USB (SLOW — USB 3.1 reads ~400 MB/s vs NVMe ~3 GB/s); first cold load takes 2-5 minutes
- Inference then uses host CPU/RAM; models stay hot in host RAM while Ollama runs
- When done, eject USB, host is unchanged (no installed footprint)

### Stage 2 — Copy-to-host mode
- First run offers: "Copy models to host SSD for faster inference? (45 GB)"
- If yes, copies to host, runs from there (much faster)
- Leaves USB clean to take with you

### Stage 3 — Fully bootable
- USB boots a minimal Ubuntu with Master AI pre-configured
- Don't trust the host OS at all
- Slower (USB-only storage) but maximally portable/secure
- Think Tails for AI

## Why this specific use case fits the "rebuild" framing

The scenario Elijah describes — dead town, working hardware, need to turn on power/water — is:
- **Engineering + operations**, not hacking. The corpus provides domain knowledge, the AI provides reasoning, the user provides hands.
- **Consistent with the Off-Grid Civilization Information project** — scrap scanner + apothecary + now **industrial-systems corpus**.
- **Not about bypassing security** — it's about operating unfamiliar machinery correctly and safely. "How do I cold-start this genset?" is the question, not "how do I crack this password."

When/if the scenario involves encountering a computer that's password-locked: that's not what this tool does. If a system is protected, it stays protected — that's a deliberate choice the previous operator made and the AI should respect. Physical-systems operation (gensets, pumps, HVAC) is the target.

## How to apply

- Pull this in as a natural extension of **Apocalypse Mode** (`project_apocalypse_mode.md`) and the **Off-Grid Civilization Information** PROJECTS.md entry — same stack of concerns, different form factor.
- Treat corpus curation as its OWN project — one person can't create 50 GB of content; they can curate freely-licensed sources into a searchable archive.
- Legal constraint: ONLY ship content that's public domain, Creative Commons, or the operator has the right to distribute. Don't bundle paid textbooks or copyrighted manuals. Make a list of LINKS the operator can download themselves if they want extra.
- Future Sensei command for Stage 1: `portable: status` reports USB health, corpus freshness, model integrity. `portable: sync` refreshes corpus from curated sources.
- Vector search over the corpus is the enabler — models don't need to have every fact memorized if they can cite from a well-indexed offline library.
- The scrap scanner + apothecary + industrial corpus are the three input-to-knowledge pipelines: look at the object → get the reference → get the plan.

This is the final form of Master AI — the product you can mail to a farmer, a remote homesteader, a disaster relief volunteer, a researcher in a third-world country. It's the one they can't take away because you're holding it in your pocket.
