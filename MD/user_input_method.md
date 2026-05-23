---
name: How Elijah Works with Claude
description: Elijah works remotely from his phone via RustDesk into Madam-Mary — not SSH, not voice-first
type: user
originSessionId: 40445963-46e1-46f3-82c6-257d45f0bc25
---
Elijah works remotely via **RustDesk** (NOT SSH) from his phone to Madam-Mary. He sees the full Linux desktop on his phone screen.

## Primary constraints (REAL, verified)

### RustDesk quirks
- **Esc key closes RustDesk** — never use Esc as quit/cancel in any Master AI UI.
- **Arrow keys unreliable** — arrows send `\x1b[A/B/C/D`; RustDesk can eat the `\x1b` prefix, so arrows don't reach Master AI cleanly. Use letter keys (`n`/`b`/`q`) as primary navigation, arrows as a local-only bonus.
- **Copy/paste requires X11 CLIPBOARD** — RustDesk syncs the X11 CLIPBOARD selection to the phone clipboard. OSC 52 does NOT work through RustDesk. xclip or gnome-terminal native selection must reach X11 CLIPBOARD.

### Working copy/paste path (v1.2 checkpoint)
```
drag-select in tmux pane
  → gnome-terminal native selection  (requires tmux `mouse off`)
  → X11 CLIPBOARD selection
  → RustDesk syncs
  → paste anywhere on phone
```
`~/.tmux.conf` has `set -g mouse off` as the default. This means touch-scroll via tmux mouse is sacrificed — copy/paste is more important.

`xclip` IS installed. Tmux has xclip-piped `copy-pipe-and-cancel` bindings ready if mouse mode is ever re-enabled.

## Mobile constraints
- Touch/small keyboard → prefer plain-word commands over Ctrl/Alt chords
- Long output is hard to read on phones → prefer paginated slide-shows (hub/help/projects)
- Dot page counters (●●●○○) beat numeric `[3/5]` when action picks also use numbers
- Don't echo user's input as a separate line — readline shows it already; duplicating wastes screen
- Mobile connections drop → persistent tmux + supervisor-auto-restart are essential
- Layouts should fit ~20 rows without horizontal overflow

## Voice-to-text transcription quirks (read past these, don't make him re-type)
- **"except" ↔ "accept"** flip. When Elijah types "except edits" he means "accept edits." Confirmed 2026-04-20: *"I did like accept edits… mine is except edit. I don't know why it's spelling it wrong. Talk to text."*
- Short homophone swaps are routine in his input. Infer intent and proceed — never ask him to re-type a word.
- Sentences run together with no punctuation because voice-to-text doesn't auto-punctuate on his phone. Parse by word groups, not sentence boundaries.

## Known bad interactions (from painful experience)
- **Idle thought-cloud thread + readline = broken typing.** The thread's ANSI cursor save/restore races with readline's redraw. Typed text gets wiped or truncated. **Currently DISABLED in main loop** (code still in file, commented out). Don't re-enable without fixing the race.
- **Tmux mouse mode ON + RustDesk = no copy/paste.** Tmux eats the drag-select before gnome-terminal sees it, so nothing reaches X11 CLIPBOARD. We toggled to `mouse off` for this reason.
- **`i <text>` startswith "i "** was parsing any message beginning with "i " as an image command (e.g. "i do X" → image path = "do X" → crash). Now guarded: only treats as image if arg contains `/`, `~`, or has an image extension.

## Recovery (ladder from gentle to full rebuild)
1. `refresh` inside Master AI — soft re-exec
2. `kick` inside Master AI — supervisor respawn (exit 42)
3. `~/scripts/master_ai_refresh.sh` from any shell
4. `~/scripts/master_ai_kick.sh` from any shell
5. `pkill -KILL -f "python3.*master_ai.py"` — force-kill; supervisor auto-restarts
6. `tmux kill-session -t master-ai && bash ~/scripts/launch_master_ai.sh` — nuke + rebuild

Crash log: `~/scripts/master.crash.log`. Engine PID: `pgrep -af "python3.*master_ai.py"`.

## Git save points in `~/scripts/`
- `v1.0-stable` — full features including idle thought cloud (known to cause typing issues)
- `v1.1` — basic mode: thought cloud disabled, menu cleaned to 14 items, image guard
- `v1.2` — current. Same as v1.1 + tmux mouse off for copy/paste

Return to any point: `cd ~/scripts && git reset --hard v1.2`

## Save cadence rule (set 2026-04-18 session 4)
Every 5 git commits in `~/scripts/`, perform a **FULL SAVE**:
1. Confirm working tree clean (`git status`)
2. Tag current HEAD (`git tag -f vX.Y`)
3. Update `project_master_ai_state.md` with current state snapshot (version tag, app names, menu layout, active features, any reverts)
4. Update `MEMORY.md` index line if hooks changed
5. Verify recovery ladder still works (quick sanity check, don't actually kick)
6. Print the restore-point summary for the user

Don't wait for the user to ask for "save this point" — offer the full save proactively on every 5th commit.

## How to apply (when working on Master AI)
- Default: assume RustDesk-on-phone as the execution context
- Letter keys primary, arrows optional, Esc forbidden
- Paginated slide-shows over walls of text
- Any new background thread that writes to the screen MUST not race with readline (either don't run during input(), or use a separate reserved region that readline doesn't touch)
- Every feature needs an escape hatch — `refresh`, `kick`, or an external bash script
- Commit + tag when a stable state is reached
