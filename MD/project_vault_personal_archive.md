---
name: vault.py — personal archive tool (designed, NOT built)
description: 2026-04-24 spec. Single Python tool handling the full personal-archive workflow — find music to buy, organize files onto flash drive with embedded metadata + cover art, photo organization by EXIF date, document handling, private content inventory (count/size only, no content read), CD burning. Multi-mode flash drive behavior baked in via standard file organization (car radio, TV native player, computer all read same files differently). Build deferred until Sensei is reliable enough to be the natural surface for these commands.
type: project
originSessionId: 7483a978-8120-4537-84d4-a02d0ae689da
---
The vault is a single Python tool (`~/scripts/vault.py`) that handles the full personal-archive workflow. Find what to own → buy externally → organize onto flash drive → cover art + metadata embedded → optionally burn CDs. Files end up structured so the same flash drive works in car radios, TVs, computers, and DAPs without conversion.

**Sub-commands designed (NOT built yet):**
- `vault find "kendrick lamar damn"` — search iTunes Search API + multi-store URLs. Extends existing `~/scripts/iprice.py` + `~/scripts/whereisit.py` (both shipped 2026-04-24).
- `vault art "kendrick lamar damn" /target/` — download cover art from MusicBrainz Cover Art Archive (free, no auth).
- `vault rip --music /src /usb/Music` — copy + organize music with embedded ID3 tags + album art per file. Folder shape: `Artist/Album/##-Track.mp3`.
- `vault rip --photos /src /usb/Photos` — organize photos by EXIF date taken into Year/Month/Day folders.
- `vault rip --docs /src /usb/Docs` — preserve document structure or organize by type.
- `vault inventory /private /usb/Index.txt` — counts/sizes/dates ONLY, NO content read or logged. The "you know it's there but don't see it" privacy path.
- `vault burn /usb/Music/Kendrick/DAMN` — burn folder to blank CD via Linux's `wodim` (apt-installable).
- `--encrypt` flag (optional): GPG-encrypt files for true content secrecy.
- `--private` flag: blind mode, no filename logging, just totals.

**Multi-mode flash drive behavior** — same drive, context-aware automatically if files organized right. NO special script on the drive itself:
- **Car USB** → music plays via car radio's built-in player (reads `Artist/Album/##-Track.mp3` natively)
- **Computer USB** → opens as files
- **TV USB** → TV's native media player (Tizen / WebOS / Android TV / Roku) auto-detects music + photos + videos
- **Phone USB-C / OTG cable** → file browser opens
- **Raspberry Pi + HDMI to TV** → can run a custom Python UI on the TV (separate hardware project, $35 Pi)

**Apocalypse-fallback note (do NOT build for normal use):** in offline / old-TV / no-network scenarios, vault.py could optionally generate an `index.html` on the USB that any browser-equipped device can open as a fallback library UI. Modern smart TVs don't need this. Saved as conditional/future — maybe `--html-ui` flag added later when off-grid kit work resumes. Per `feedback_no_polish_creep.md`: NOT building until the apocalypse use case is real.

**Why deferred (Elijah 2026-04-24, verbatim):**
"I don't wanna make the vault yet. I'm not using it yet. I'm gonna position to do what I wanna do with it. When Sensei is running right and I can say 'go find it' and it's found I'll be happy. Not yet. These are great ideas based on the system that's not developed yet."

Translation: spec is solid, home isn't ready. Sensei needs to be reliable + capable of natural-language commands like "go find Section 80 on Bandcamp" before vault commands feel natural to invoke. Until then, vault stays paper.

**Cross-references:**
- `project_radio_app.md` — the Library Archiver concept that birthed vault. The Radio app's "killer differentiator" section IS where vault came from.
- `project_digital_relative_vision.md` — generational heirloom framing ("flash drive your kids inherit, four generations of music"). Vault is the physical-archive embodiment.
- `feedback_give_it_a_home.md` — vault MUST live IN Sensei as commands when built, not as orphan scripts. Per Elijah's defer reasoning.
- `feedback_no_polish_creep.md` — HTML UI fallback waits.
- Already-shipped tools that vault subsumes when built: `iprice.py` (iTunes search), `whereisit.py` (multi-store URLs).

**How to apply:**
- When Sensei reliability work is solid AND Elijah says "let's build vault," reference this spec exactly.
- When pitching the Radio app or Master AI generally, the vault feature is the "physical heirloom" hook — concrete, generational, family-memory-protecting.
- Do NOT build sub-commands one at a time without his explicit start signal — he wants the system ready first.
- The apocalypse HTML UI fallback is conditional; only revisit if off-grid scenarios become real, not as polish.
- Cross-platform compatibility note: the multi-mode behavior is FREE if files are organized to the standard `Artist/Album/##-Track.mp3` pattern. That's the design discipline — every script's output should be consumable by dumb hardware (car radios, old TVs) not just by software the user controls.

**Status check 2026-04-25:** `iprice.py` + `whereisit.py` are on disk in `~/scripts/` but **still untracked in git** (last commit was 2026-04-22 evening). "Already-shipped" above means functional-on-disk, not committed/wired-into-Sensei. Not yet surfaced as Sensei sub-commands; Elijah invokes them directly via `python3`. Verify with `git status ~/scripts` before claiming any vault piece is committed.
