# Sensei Clean — Cloud Apply Wired — 2026-05-11

Follow-up to `handoff_sensei_clean_real_connectors_2026-05-11.md`.

## Commit

`2ba3afa feat(sensei-clean): wire real cloud apply/undo via rclone moveto`

## What shipped

- `RcloneRemoteAdapter.scan(list_enabled=True)` calls `rclone lsjson --hash` and yields ItemRecords with provider hashes (md5 from Drive, sha1 from many others).
- `RcloneRemoteAdapter.apply()` is real: runs `rclone moveto <src> <dst>` after validating that source AND destination are both `rclone:<this_remote>:...` specs. Cross-remote moves are refused. The action.destination_path is always `rclone:<remote>:Sensei-Cloud-Quarantine/duplicates/<basename>` — nothing leaves the provider.
- `RcloneRemoteAdapter.undo()` runs the symmetric `rclone moveto` against the swapped paths.
- `engine.build_findings` dedups by best-available hash: sha256 → md5 → sha1. Each finding's `evidence` names the algorithm used.
- `engine.build_actions` emits `cloud_move` for cloud items with the in-remote quarantine destination. All cloud actions go to the **monitored** lane this round — apply requires `--approve-monitored`.
- `sensei-clean apply` and `sensei-clean undo` now group actions/records by `action.adapter` and dispatch per-adapter. Mixed local+cloud runs work.
- `policy.can_apply` permits `cloud_move` for api capability. `policy.requires_monitored_review` flags every api capability as monitored. A new `_DELETE_ACTION_TYPES` denylist makes "no destructive deletes" enforceable regardless of capability.

## Hard contract (still true)

- **No deletes**. delete / cloud_delete / trash / permanent_delete are refused by `policy.can_apply` regardless of capability.
- **No cross-remote moves**. `RcloneRemoteAdapter.apply` refuses if src or dst names a different remote.
- **No cross-provider exfiltration**. Cloud-quarantine destination lives INSIDE the same remote, in a `Sensei-Cloud-Quarantine/duplicates/` folder.
- **Monitored lane gate stays on**. Cloud actions are monitored — `sensei-clean apply <run> --approve-monitored` is required to mutate any cloud file.

## Verification

```text
python3 -m py_compile sensei_clean.py sensei_clean_app.py \
        sensei_clean/engine.py sensei_clean/policy.py \
        sensei_clean/adapters/rclone_remote.py        — OK
python3 -m unittest test_sensei_clean*                — 30 OK (8 + 2 + 20)
python3 -m unittest test_privacy_cloud_guard test_harvest_privacy \
        test_master_ai_parser test_master_ai_safety   — 155 OK
Total: 185 tests, 0 FAIL, 0 ERROR.
```

Every cloud test mocks `rclone_lsjson` / `rclone_moveto`. **No real cloud has been touched yet.** The next section is for you to do that manually.

---

## Manual integration test plan (your turn)

This is the safest possible smoke against your real gdrive. **Run from a normal terminal on Madam-Mary, NOT inside a sandboxed agent.** The Codex sandbox failed `rclone about gdrive:` on a DNS lookup; Madam-Mary's real network is what we need.

### 1. Prepare a sandbox folder on gdrive
Create a small test directory in your gdrive that we control. From any shell on Madam-Mary:

```bash
rclone mkdir gdrive:Sensei-Cloud-Test
echo "hello cloud" | rclone rcat gdrive:Sensei-Cloud-Test/A/x.txt
echo "hello cloud" | rclone rcat gdrive:Sensei-Cloud-Test/B/x.txt
echo "unique"      | rclone rcat gdrive:Sensei-Cloud-Test/C/y.txt
```

Two identical `x.txt` files (one cluster of duplicates), one unique. Total: 3 files in a folder you can `rclone purge` if anything goes sideways.

### 2. Confirm `rclone about` works in your shell
```bash
rclone about gdrive: --json
```
You should see `{"total":..., "used":..., ...}`. If this fails with DNS issues, you're not in your real shell — open a normal terminal on Madam-Mary.

### 3. Run the scan with list_enabled (Python entry, since the CLI doesn't expose list_enabled yet)
The bare CLI exposes scan but not the cloud listing flag. Do this from python:

```bash
python3 - <<'PY'
from sensei_clean.engine import scan_run
from sensei_clean.adapters.rclone_remote import RcloneRemoteAdapter

# Listing isn't exposed via CLI flags yet — driver script.
RcloneRemoteAdapter._ORIGINAL_LIST = RcloneRemoteAdapter.list_enabled
import sensei_clean.engine as eng

# Monkey-patch the engine's cloud-root constructor to enable listing:
real_init = RcloneRemoteAdapter.__init__
def force_list(self, *a, **kw):
    kw["list_enabled"] = True
    real_init(self, *a, **kw)
RcloneRemoteAdapter.__init__ = force_list

run_path, caps, items, findings, actions = scan_run(
    roots=["rclone:gdrive:Sensei-Cloud-Test"],
    sha256=False,
    quarantine_root="/tmp/sensei-clean-cloud-test-q",
    include_previews=False,
)
print("run:", run_path)
print("items:", len(items), "findings:", len(findings), "actions:", len(actions))
for a in actions:
    print(" ", a.action_type, a.lane, a.source_path, "->", a.destination_path)
PY
```

Expected: items=3, findings=1 (the x.txt cluster), actions=1 cloud_move with lane=monitored, destination=`rclone:gdrive:Sensei-Cloud-Quarantine/duplicates/x.txt`.

### 4. Inspect the artifacts
```bash
RUN=$(ls -1dt ~/sensei_runs/*/ | head -1)
cat $RUN/reports/summary.md
cat $RUN/actions.jsonl
```

### 5. Apply with monitored approval
```bash
sensei-clean apply $RUN --approve-monitored
# Confirm with y at the prompt.
```

Verify on the remote side:
```bash
rclone ls gdrive:Sensei-Cloud-Test
rclone ls gdrive:Sensei-Cloud-Quarantine/duplicates
```

Expected: one of the `x.txt` files has left `Sensei-Cloud-Test/` (under whichever folder rclone picked first) and now lives in `Sensei-Cloud-Quarantine/duplicates/x.txt`.

### 6. Run undo
```bash
sensei-clean undo $RUN
# Confirm with y.
```

Verify:
```bash
rclone ls gdrive:Sensei-Cloud-Test
rclone ls gdrive:Sensei-Cloud-Quarantine/duplicates
```

The x.txt should be back at its original path; the quarantine folder should be empty.

### 7. Clean up the test folders
```bash
rclone purge gdrive:Sensei-Cloud-Test
rclone purge gdrive:Sensei-Cloud-Quarantine   # optional, only if you want to remove the structure
```

## What's still NOT wired

- `list_enabled` is not exposed via the CLI flag set. Step 3 above uses a monkey-patch driver. Next round will add `sensei-clean scan --list-cloud` and a checkbox in the GUI.
- No paginated listing. Large drives will block the scan. Acceptable for a first smoke; rclone returns all matching files in one shot anyway.
- No mid-apply progress reporting for cloud actions. Each `rclone moveto` runs in one go.
- OneDrive, Dropbox, Box etc. would work in theory (same rclone interface) but are untested.

## Recommended next round

Once the manual smoke is green:

1. Expose `--list-cloud` on `sensei-clean scan` and a "list files" checkbox in the GUI per cloud row.
2. Add `--cloud-path PREFIX` to scope the cloud listing to a subfolder (so customers don't accidentally hash their whole drive on the first scan).
3. Add a "dry run" mode (`apply --dry-run`) that prints the move commands without executing.
4. Add OneDrive smoke test plan (mirror this doc against a Dropbox/OneDrive test folder).
