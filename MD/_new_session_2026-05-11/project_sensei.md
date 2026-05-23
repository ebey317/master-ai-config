---
name: project-sensei
description: "Sensei Universal Cleanup — review-first, reversible cleanup/synthesis engine; current state, architecture, and the open hardening items"
metadata: 
  node_type: memory
  type: project
  originSessionId: 9ee8a8f7-ee27-4ebe-9264-c84ee3dac19a
---

Sensei = universal scan-first, review-first cleanup system (Linux/macOS/Windows + Android/iOS export + cloud connectors + browser-assisted). Evolved out of a Google Drive cleanup Bash script.

**Why:** the user wants one decision engine + one manifest format + one review/apply/undo contract, with many source adapters (Drive, local FS, browser, exports). The goal is not just cleanup but also downstream synthesis — résumé timelines, portfolio packets — built from the cleaned inventory.

**How to apply:** when working on cleanup/Drive/résumé/portfolio code, treat the Python scaffold at `~/scripts/sensei_clean/` as the intended trusted core. The Bash Drive tool at `~/Downloads/drive_cleanup.sh` is the legacy review-first implementation that informed the Python design.

## Current state (2026-05-11)

Python scaffold exists and tests pass:
- `~/scripts/sensei_clean/` — package (`schemas.py`, `policy.py`, `ranker.py`, `queue_builder.py`, `apply.py`, `reports.py`, `adapters/base.py`, `adapters/local_fs.py`)
- `~/scripts/sensei_clean.py` — CLI wrapper
- `~/scripts/test_sensei_clean.py` — stdlib unittest suite (passes)
- Local FS adapter: probe / scan / enrich (sha256, screenshot hint, txt+md snippet) / reversible apply + undo

Outputs per run: `inventory.jsonl`, `findings.jsonl`, `actions.jsonl`, `queue.json`, `capabilities.json`, `reports/summary.md`.

Three execution lanes: `monitored`, `background`, `unattended`. Five capability types: `api`, `local`, `export`, `browser`, `unavailable`.

## Open hardening items (must land before more adapters)

Ranked, from the last review:

1. **High** — `apply.py` calls `adapter.apply(action)` without enforcing `policy.can_apply`, lane restrictions, monitored approval, or capability gating. Sensitive actions could slip through.
2. **High** — undo records are written *after* successful moves, not before. A mid-run crash leaves moved files with no durable reverse log.
3. **High** — duplicate quarantine destination is `quarantine_root/duplicates/<basename>` with no uniqueness — same-basename duplicates collide.
4. **Medium** — `sensei_clean.py` hardcodes duplicate actions with `lane="unattended"`, `approval_required=False`, but `queue_builder.py` independently recomputes lane. `actions.jsonl` and `queue.json` can disagree.
5. **Medium** — hashes only computed with `--sha256`, so default runs miss exact duplicates.
6. **Medium** — screenshot heuristic flags any filename containing `img_20`, which mislabels ordinary camera photos.

## Next build step after hardening

Review-only Google Drive adapter emitting the same manifest/JSONL schema. Read-only first; only add remote apply once policy enforcement + durable undo + retry/backoff + reversibility are correct. Then résumé-source discovery, export/browser adapters, then a desktop review UI.

Related: [[reference-master-ai-locations]]
