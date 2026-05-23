# Claude Task: Sensei Clean Real Connector Build

Elijah's actual goal:

Build Sensei Clean into a consumer-grade cleaner/organizer that works across real customer sources, not just local test folders. The customer should not need to know paths. The app should discover/connect sources, scan safely, show actual file previews/content where possible, then organize/clean with review-first permissions and undo.

Current state:

- Desktop launcher: `/home/elijah/Desktop/SenseiClean.desktop`
- Main app: `/home/elijah/scripts/sensei_clean_app.py`
- Engine: `/home/elijah/scripts/sensei_clean/engine.py`
- Local adapter only: `/home/elijah/scripts/sensei_clean/adapters/local_fs.py`
- Connector registry exists but only detects local folders/synced mount paths: `/home/elijah/scripts/sensei_clean/connectors.py`
- Preview writer exists: `/home/elijah/scripts/sensei_clean/previews.py`
- Tests:
  - `/home/elijah/scripts/test_sensei_clean.py`
  - `/home/elijah/scripts/test_sensei_clean_connectors.py`

Do not claim current connector support is complete. It is not. Current support is only local filesystem roots, local sync folders, and mounted Android/removable storage.

## Required Outcome

Implement real connector architecture and consumer-ready flows for:

1. Local OS sources
   - Linux home folders and mounted drives.
   - Design abstraction so Windows/macOS paths can be added cleanly later.
   - Do not hard-code only Linux assumptions into the core engine.

2. Microsoft Office + LibreOffice files
   - Detect and classify:
     - Word/Writer: `.doc`, `.docx`, `.odt`, `.rtf`
     - Excel/Calc: `.xls`, `.xlsx`, `.ods`, `.csv`
     - PowerPoint/Impress: `.ppt`, `.pptx`, `.odp`
     - PDF and plain text docs.
   - Preview readable document contents where possible.
   - Provide open/view action using local apps (`xdg-open`, LibreOffice) without moving files.
   - Organize by category with reversible moves and undo journal.

3. Android
   - Support mounted Android storage through MTP/GVFS and `/media`.
   - Add a clear adapter boundary for future ADB/app-cache cleanup.
   - Do not pretend Android app cache/data cleanup works unless it is implemented and tested.
   - If ADB support is added, it must be explicit opt-in and never delete app data without typed approval.

4. Cloud storage
   - Implement real connector abstractions, not only local folder detection.
   - Minimum real connector target: Google Drive API first.
   - Next targets: Microsoft OneDrive/Graph, Dropbox.
   - OAuth/token storage must be explicit and safe.
   - No cloud delete by default. Start with scan/list/preview/export metadata.
   - Any move/delete/organize action must require review and undo/safe recovery where provider supports it.

5. File preview/viewing
   - Reports must point to actual local files or provider file IDs.
   - For local files, write `previews.json` and `reports/previews.md`.
   - For cloud files, include provider URL/file ID plus downloaded/exported preview where allowed.
   - For images/videos/PDFs, provide open/view records, not just counts.
   - For docs, extract actual readable text preview where possible.

6. Permissions
   - Keep Sensei-style gates:
     - scan is explicit
     - sensitive roots require typed `YES`
     - monitored lane requires extra approval
     - mutations write undo records immediately
     - no destructive delete path in first pass
   - Private content must not be sent to cloud LLMs.

## Acceptance Criteria

The work is not passing until these are true:

- App has a clear Sources/Connectors screen that distinguishes:
  - Local OS folders
  - Mounted Android/removable storage
  - Local synced cloud folders
  - Real cloud API connectors
- The UI does not ask normal customers to type paths as the main flow.
- Tests prove scan -> preview -> organize/clean -> apply -> undo.
- Tests cover Office/Libre files, Android mounted storage, and cloud connector behavior.
- For cloud API work, tests can use fake providers/mocks, but the adapter contract must match the real API workflow.
- Report includes actual file preview/view data, not only summary counts.
- `python3 -m py_compile` passes.
- Existing Sensei Clean tests pass.
- Existing parser/safety/privacy tests still pass.

## Suggested Implementation Order

1. Refactor adapters into a provider contract:
   - `scan()`
   - `enrich()/preview()`
   - `plan_actions()`
   - `apply()`
   - `undo()`
   - `open/view metadata`

2. Split current local filesystem behavior into:
   - `LocalFolderConnector`
   - `MountedAndroidConnector`
   - `LocalSyncedCloudConnector`

3. Add `CloudDriveConnector` base class with fake provider tests first.

4. Implement Google Drive connector behind explicit connect/auth flow.

5. Add viewer/preview actions:
   - local open command
   - cloud provider URL
   - local exported preview file when possible

6. Expand tests and run gates.

## Current Verification Baseline

Current verified status is favorable only for:

- local filesystem cleanup
- local synced-folder cleanup when sync folder exists
- mounted Android/removable storage when present
- Office/Libre classification and local preview extraction
- reversible apply/undo

Current status is not complete for:

- real Google Drive API
- real OneDrive/Microsoft Graph
- real Dropbox API
- Android app cache/data cleanup
- cross-OS packaged consumer app

