---
name: project-sensei-clean-ai-file-manager
description: Sensei Clean is evolving from "duplicate cleanup CLI" into an AI file manager across local + Google Drive + OneDrive + photos + phone. Full System Scan is milestone 1.
metadata:
  type: project
---

The Sensei Clean product direction shifted on 2026-05-11 from "review-first local cleanup" into "AI file manager." Full vision:

- scan the whole connected world: Madam-Mary, local folders, Google Drive, OneDrive, photos, phone storage, cloud storage
- show what exists, what's old, what's duplicated, what's wasting space
- organize files into useful folders
- clean old junk and clutter
- help edit/rename/sort files with AI
- open real apps when needed: browser, file manager, LibreOffice, Drive web, photo viewer
- keep local and cloud organization connected (a resume in Drive reflects the matching folder on Madam-Mary)
- show storage saved in MB/GB
- track "last full clean" date in Master AI / Sensei menu
- build a searchable memory/index so Sensei knows where files are later and can reference them with permission

**Hard safety rule (still in force):** full scan first. No moving or deleting yet. Then review screen. Then safe quarantine. Then undo. Permanent delete is **last** and only with clear approval.

**How to apply:** when proposing work on Sensei Clean, lead with the milestone the spec named first — Full System Scan — and don't drift into AI-edit / open-app / search-index features until the user calls them up. Don't claim cloud or photo-API or Android-app-cache cleanup is done unless implemented and tested (per [[handoff-sensei-clean-real-connectors-2026-05-11]]).

## Milestone status (as of 2026-05-11, commit 2df27bd)

**Milestone 1 — Full System Scan ✅ shipped:**
- `sensei-clean scan-all` auto-discovers every source via `connectors.detect_sources` and runs one scan_run
- waste report (biggest / oldest / by-category / reclaim totals) in summary.md and waste.json
- `sensei-clean status` shows last-clean timestamp + reclaim total
- state lives at `~/.config/sensei-clean/state.json` (0600, no file names)

**Milestone 2 — Organize (already shipped earlier today):** archive_move actions by extension bucket. Reversible. Lives in `engine.build_organize_actions`.

**Milestone 3 — Clean / quarantine (already shipped):** quarantine_move + cloud_move via rclone (in-remote folder only, never leaves provider).

**Open milestones (not yet built):**
- AI edit/rename of files (use a model to suggest better names / re-categorize)
- Open real apps from the review screen (xdg-open already exists in app; needs an "Open in app" action per item)
- Cross-host organization mirror (resume in Drive ↔ matching folder on Madam-Mary)
- Searchable memory/index of file locations + summaries (built atop inventory.jsonl, queryable from Sensei chat)
- Permanent delete path with typed YES approval (the hardest one; deletes are explicitly the last thing built)

## Key files

- `~/scripts/sensei_clean.py` — top-level CLI (scan / scan-all / status / apply / undo)
- `~/scripts/sensei_clean_app.py` — prompt-toolkit GUI launched by `~/Desktop/SenseiClean.desktop`
- `~/scripts/sensei_clean/engine.py` — scan_run pipeline, multi-adapter dispatch
- `~/scripts/sensei_clean/connectors.py` — Source/Connectors picker data
- `~/scripts/sensei_clean/adapters/rclone_remote.py` — real cloud via rclone
- `~/scripts/sensei_clean/adapters/local_fs.py` — local FS adapter with privacy-aware classification
- `~/scripts/sensei_clean/waste.py` — analytics (no mutations)
- `~/scripts/sensei_clean/status.py` — last-clean state tracker
- `~/.local/bin/sensei-clean` — wrapper customers run

## Where the spec lives
- Task brief: `/home/elijah/MD/CLAUDE_TASK_sensei_clean_real_connectors_2026-05-11.md`
- Connector handoff: `/home/elijah/MD/handoff_sensei_clean_real_connectors_2026-05-11.md`
- Cloud apply handoff: `/home/elijah/MD/handoff_sensei_clean_cloud_apply_2026-05-11.md`

Related: [[project-sensei]] (the original Universal Cleanup scaffold briefing — has the four-layer policy direction this evolved from).
