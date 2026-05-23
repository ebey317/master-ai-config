---
name: Automatic OS-Aware Maintenance Window
description: Sensei owns its own maintenance. v1 LIVE 2026-04-21 — biweekly deep clean on Thursdays 04:30-06:00 Indianapolis, computer-idle gated (not human-idle, Elijah is away at work then). Systemd user timer + ~/scripts/deep_clean.sh. Forward work: spring-clean scope, Windows/Mac branches, Pupil review card.
type: project
originSessionId: 39d12576-f270-4aa3-8989-c741ebeec446
---

## LIVE v1 (2026-04-21)

**First production maintenance schedule installed 2026-04-21:**

- **Cadence:** biweekly (every 13+ days; timer fires every Thursday, script skips if last run <13 days ago)
- **Window:** Thursday 04:30–06:00 America/Indiana/Indianapolis
- **Gate:** computer-idle, NOT human-idle. Elijah is at work by 5:15 AM so human-idle is trivially true — what matters is the box has spare cycles. Script checks load avg <0.8 AND no `ollama runner` process.
- **Script:** `~/scripts/deep_clean.sh` (executable, `bash -n` clean)
- **Systemd:** `~/.config/systemd/user/master-ai-deep-clean.{service,timer}` (copied from `~/scripts/systemd/`), enabled via `systemctl --user enable --now master-ai-deep-clean.timer`
- **Stamp file:** `~/.master_ai_last_deep_clean` (biweekly gate)
- **Report output:** `~/Desktop/master_ai_cleanups/deep_clean_<timestamp>.md`

**What v1 actually does:**
1. Bug check — syntax-scans every `.py` and `.sh` in `~/scripts`, flags errors
2. File clean — invokes existing `~/scripts/cleanup.sh` (pip cache, browser caches, Downloads clutter)
3. Session archive — gzips session files in `~/scripts/sessions/` older than 30 days
4. Ollama audit — lists pulled models, flags any NOT in the trifecta (3b/7b/llava) without deleting (Elijah decides)
5. Markdown report written to Desktop so he sees it when he gets home

**Check the timer state:**
```
systemctl --user list-timers master-ai-deep-clean.timer
```

---

## Original design (2026-04-20 early morning, discussion mode) — kept for context
## Decision (Elijah, 2026-04-20 early morning, discussion mode)

Sensei runs automatic maintenance on its own schedule — **AI-controlled cadence**, **idle-gated trigger**. Built into the product, not a hand-off to the user.

## How it works

**Cadence — AI decides, based on workload:**
- Heavy workload (lots of model calls, CPU hours, disk churn) → tighten toward weekly
- Light workload → loosen toward monthly
- Default range: **weekly / biweekly / monthly**

**Trigger — day-window + idle-gate, NOT wall-clock:**
- ❌ Bad: "3 PM Sunday" (Elijah might be mid-brainstorm at 3 PM or 3 AM)
- ✅ Good: "on Sunday, after 60 straight minutes of idle"
- The AI respects flow states. If the user is active, maintenance waits.

**Flow:**
1. Idle gate fires → Sensei announces maintenance window starting
2. Pauses itself + any in-flight work (work resumes where it left off, no data loss)
3. Runs OS-appropriate cleanup
4. Resumes Sensei + queued work

## OS-awareness (cross-platform — the product ships on any OS)

Master AI is cross-platform by design. Maintenance must branch on OS:

- **Linux** (Elijah's current install on Mint): bash script — `apt update/upgrade`, `journalctl --vacuum-time`, `fstrim`, clear model caches, rotate logs. ~20 lines, no third-party tool needed.
- **Windows**: shell out to the system's own tools (Disk Cleanup, SFC, built-in defrag) or hand off to an installed utility if present (IOLO System Mechanic, Norton Utilities, CCleaner). **Unknown**: whether any of those expose a real API vs CLI-only — needs research when we build the Windows branch.
- **Mac**: its own path — `brew cleanup`, purge, log rotation.

## Why this is the right call

- Always-on product means cruft accumulates — maintenance isn't optional.
- Buyer-friendly: "it takes care of itself" is a stronger pitch than "remember to run IOLO weekly."
- Idle-gated trigger means it never fights the user's attention. Respects the fact that Elijah's schedule is irregular (5 AM brainstorms, Sunday 3 AM flow sessions).
- AI-controlled cadence means the system adapts to actual use, not a fixed guess.

## How to apply

- When building the 24/7 soak test plumbing, the maintenance window is a required companion feature — don't ship always-on without it.
- Don't hard-code Linux paths into the maintenance logic. Branch on OS from day one.
- The idle gate should be a reusable primitive — "wait until user has been inactive for N minutes" is also useful for study-sessions review, deferred notifications, and background project-queue work.
- Maintenance pause must be **safe-resume**: in-flight model calls abort cleanly, queued work survives, nothing half-writes state to disk.

## Filesystem spring-cleaning (Elijah, 2026-04-20, same discussion)

Maintenance window is NOT just OS upkeep (apt/fstrim/etc). It also runs a **filesystem spring clean** across the user's directories. Scope is big:

- **Scan every file / every directory** the user has access to
- **Find duplicate files** (same content in multiple places)
- **Find clutter** — orphan files, wrong-folder files, stale downloads
- **Suggest consolidation** — e.g., "you have 40 HTML files scattered — want me to make an `html/` folder?"
- **Sort Downloads** — clear the Downloads folder by moving each file to its proper home

**Split between automatic action and suggestion:**
- Auto: sorting Downloads into known-type folders (images → Pictures, docs → Documents, etc.)
- Suggestion: anything involving restructuring the user's own folders, creating new folders, or removing duplicates. User approves in Pupil or via Sensei prompt when they return.

**Where suggestions surface:** Pupil "cleanup review" card OR a Sensei greeting on next session ("Here's what I'd like to tidy — approve or skip each"). Decision deferred.

## Not yet decided

- Exact idle threshold (60 min is Elijah's example — may tune).
- Whether user can override / postpone an incoming maintenance window ("not now, I'm working").
- Whether maintenance logs surface in Pupil as a visible history.
- Windows branch tooling choice when we get there.
- **Spring-clean scope**: whole home directory? User-configured roots? Opt-out folders (e.g., never touch `~/scripts/`)?
- **Dedup method**: name+size (fast, cheap, can miss) vs full hash (slow on large trees, accurate). Maybe hash only when name+size matches.
- **Suggestion delivery**: Pupil card vs Sensei greeting vs both.
- **Auto-sort confidence**: do we ever move a file without asking, or does every move need approval? Downloads is probably safe, but rest might not be.
