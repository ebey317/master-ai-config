# Sensei Clean Connector Verification

Scope verified:
- Desktop launcher points to the full app: `/home/elijah/scripts/sensei_clean_app.py`
- Local OS folders are detected through the connector registry.
- Synced cloud folders are supported when present as local sync directories: Google Drive, OneDrive, Dropbox, Nextcloud, iCloud Drive, Syncthing.
- Android/removable storage is supported when mounted through GVFS MTP or `/media/<user>`.
- Office/Libre file types are classified and can be organized: doc/docx/odt/rtf, xls/xlsx/ods/csv, ppt/pptx/odp, pdf/txt/md.
- Local preview artifacts are written:
  - `reports/previews.md`
  - `previews.json`
  - Text excerpts for txt/md/csv/log/json/yaml.
  - Extracted document text for docx/odt/ods/odp/pptx/xlsx when XML is readable.
  - Open-file records for images, videos, PDFs, and other media.

Verification run:
- `py_compile`: PASS
- `test_sensei_clean`: PASS, 8 tests
- `test_sensei_clean_connectors`: PASS, 2 tests
- `sensei_clean_app.py --selftest`: PASS
- `test_master_ai_parser.py`: PASS, 77 tests
- `test_master_ai_safety + test_privacy_cloud_guard + test_harvest_privacy`: PASS, 78 tests

Important limit:
- There are no true Google Drive / OneDrive / Dropbox OAuth/API connectors yet.
- Cloud support today means "scan the local synced folder if the sync client has mounted it."
- Android support today means "scan mounted storage if the phone is exposed through MTP/GVFS or removable media."
- Android app cache/data cleanup is not implemented; that requires device permissions/tooling and should be a separate adapter.

Live machine status during verification:
- Available connector kind: `local`
- Available folders: Downloads, Desktop, Documents, Pictures, Videos, Music
- No live synced cloud folders or Android MTP mounts were present at verification time.

Consumer readiness:
- Favorable for local cleanup and local-sync-folder cleanup.
- Favorable for safe scan/review/apply/undo with preview reports.
- Not yet a full cloud-account or Android-app cleaner until API/device adapters are added.

