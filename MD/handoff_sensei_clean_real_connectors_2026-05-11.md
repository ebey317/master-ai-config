# Sensei Clean — Real Connector Architecture — 2026-05-11

Response to `CLAUDE_TASK_sensei_clean_real_connectors_2026-05-11.md`.

## Commit

`9b4256a feat(sensei-clean): real connector architecture (local + Android + cloud-API contract)`

13 files, 2093 insertions, stacked on `6f272dc` (test runner) → `d1b09b8` (standalone CLI).

## Acceptance check (honest)

| Spec requirement | Status |
|---|---|
| Sources/Connectors screen distinguishes local / Android / synced-cloud / cloud-API | **DONE** — `detect_sources()` returns four `kind` values; GUI picker shows all. |
| UI does not require typing paths as the main flow | **DONE** — checkbox picker in `sensei_clean_app.py`. Path entry only via the advanced fallback when no source is selected. |
| Tests prove scan → preview → organize → apply → undo for local | **DONE** — `test_sensei_clean_connectors.test_scan_organize_apply_undo_and_preview`. |
| Tests cover Office/Libre files | **DONE** — `previews.py` extracts docx/odt/pptx/xlsx text; the connector test writes a real docx and asserts the preview text round-trips. |
| Tests cover Android mounted storage | **DONE** — `test_detects_os_cloud_and_android_sources` covers gvfs MTP + /media. |
| Tests cover cloud connector behavior | **DONE for the contract**, via the fake provider (13 cloud tests). Real cloud listing/mutation tests are deferred — see "not wired" below. |
| Fake providers/mocks for cloud, but adapter contract matches real API workflow | **DONE** — `FakeDriveAdapter` and `RcloneRemoteAdapter` both implement the same `CloudDriveAdapter` contract that a real Google Drive subclass will. |
| Report includes actual file preview/view data, not only counts | **DONE** — `previews.json` + `reports/previews.md` per run; readable excerpts for text + Office docs. |
| `python3 -m py_compile` passes | OK |
| Existing Sensei Clean tests pass | 10/10 |
| Existing parser/safety/privacy tests still pass | 155/155 (77 parser, 55 safety, 20 privacy guard, 3 harvest privacy) |
| **Total** | **178 tests, 0 failures** |

## What is NOT wired (do not claim done)

The spec explicitly says to be honest about these. Here they are:

1. **Real Google Drive file listing.** `rclone:gdrive:` adapter is *probe-only* this round — `scan()` yields nothing unless `list_enabled=True`, and even that branch is intentionally left as TODO. Reason: cloud listing without a tested apply path encourages mistakes. Probe-only first validates the account is live; listing comes once apply is tested on a small subset.
2. **Cloud apply / undo.** Both `RcloneRemoteAdapter` and `GoogleDriveAdapter` return `ApplyResult(success=False, ...)` with the message `not wired`. The `cloud_move` action_type is declared in the contract but no real provider executes it yet.
3. **Direct Google Drive via `google-api-python-client`.** The `gdrive.py` stub exists with the right surface (probe / authorize / scan / apply). The OAuth flow body is commented so the module never accidentally pops a browser on import. Probe surfaces the specific missing piece (deps, client secret, or token). The recommended path is rclone since the user already has it configured.
4. **OneDrive, Dropbox, Microsoft Graph.** The `CloudDriveAdapter` base class supports them — drop a subclass per provider. None implemented in this round.
5. **Android app cache / data cleanup via ADB.** The spec was explicit: do not claim it. Mounted-storage support works (MTP via gvfs, /media); app-cache via ADB is not wired.
6. **Cross-OS packaging.** `connectors.py` uses XDG to locate home folders, which abstracts the Linux case. Windows-specific paths (`%USERPROFILE%`, `OneDrive - Personal` as the OS-managed sync root) and macOS paths (`~/Library/Mobile Documents/com~apple~CloudDocs`) are not implemented. The detection hook is in one place (`_home_folder_connectors`), so the eventual port is bounded.

## Cloud connector — what the customer sees today

In the Sources picker, an entry like:

```
gdrive (rclone)  (rclone:gdrive:)  - real cloud API connector via rclone; probe-only this round — listing/mutations not wired yet
```

If they check it and run scan, the run's `capabilities.json` records the cloud capability with `available=true` (when rclone can refresh the token) and a quota note in the capability's `notes` field. The run's `inventory.jsonl` contains zero cloud items. The summary report mentions the cloud capability in the Adapters section but reports no findings/actions from it. That matches the user's stated intent: **probe only account/availability metadata first, not list private file names.**

## Files added or touched

```
sensei_clean/adapters/cloud_drive.py     (new) — abstract cloud contract
sensei_clean/adapters/fake_drive.py      (new) — in-memory fake for tests
sensei_clean/adapters/gdrive.py          (new) — google-api-python-client stub
sensei_clean/adapters/rclone_remote.py   (new) — rclone-backed adapter, probe-only
sensei_clean/adapters/local_fs.py        (mod) — small restructure
sensei_clean/connectors.py               (new) — Sources discovery with 4 kinds
sensei_clean/engine.py                   (new) — multi-adapter scan_run
sensei_clean/previews.py                 (new) — docx/odt/pptx/xlsx extraction
sensei_clean/reports.py                  (mod) — capabilities list in summary
sensei_clean_app.py                      (new) — prompt-toolkit GUI app
sensei_clean_test.sh                     (mod) — Demo/Custom mode
test_sensei_clean_cloud.py               (new) — 13 cloud contract tests
test_sensei_clean_connectors.py          (new) — discovery + organize + Office tests
```

## Recommended next round

Single biggest unlock to make cloud real: pick **one** real action (e.g. quarantine of exact duplicates in a single trial folder on the user's gdrive), wire it through `RcloneRemoteAdapter.apply` using `rclone moveto`, write a contained integration test against the user's *own* drive in a sandboxed subfolder, then graduate to broader cloud actions.

Sequence:
1. Implement `RcloneRemoteAdapter.scan(list_enabled=True)` using `rclone lsjson --hash`.
2. Add `cloud_move` apply path using `rclone moveto`, scoped to a `Sensei-Cloud-Quarantine/` folder the user owns.
3. Add a `cloud_undo` path that reverses the moveto.
4. Hand the user a manual integration test plan they run once against their real drive.
5. Promote `list_enabled` to opt-in on the Sources picker (an extra checkbox per cloud row).
