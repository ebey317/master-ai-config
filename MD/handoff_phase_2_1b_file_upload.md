# Handoff — Phase 2.1b: file-upload bridge (last piece blocking job-app e2e)

Date: 2026-05-14 evening
Lane split: Codex owns content_script.js; Claude waits for that to land,
then ships the side_panel.js dispatch glue + tests in a follow-on.

## Why this exists

Commit `7fb259d feat(extension): resumePath setting + /extension/read_local_file endpoint`
landed the backend half of résumé upload:

- `options.html / options.js` — résumé file-path setting + "Test read" button.
- `stt_server.py /extension/read_local_file` — POST `{path}` → `{ok, mime, size, base64}`.
- `stt_server.py _api_prompt` — emits `resume_path:` line in `[API REQUEST]` envelope.
- `side_panel.js sendPrompt` — passes `resume_path` in the /chat body.

That commit's body explicitly deferred the content_script side because
Codex's content_script.js refactor pile was uncommitted at the time.
That pile is **still** in the working tree on 2026-05-14 evening; do
not commit the deferred bridge until the refactor lands.

The active goal is: **the Chrome extension self-automates a job
application all the way until submitted.** The deterministic
acceptance test for that goal is `sensei_extension/test/JOB_APP_CHECKLIST.md`
(commit `693a34b`), using `job_app_smoke.html` as the page. That
fixture marks résumé upload as a `Known Gap` because this bridge
isn't live yet. **This handoff closes that gap.**

## What Codex needs to land in content_script.js

### 1. Parse `file://` prefix in `parseFillTarget`

`parseFillTarget(action)` currently returns `{selector, value, overwrite}`
where `value` is a string. Extend to detect a `file://` prefix on the
value and surface it as a typed sub-shape:

```js
// In parseFillTarget — after the existing parsing, before return:
if (typeof value === "string" && value.startsWith("file://")) {
  return {
    selector,
    fileLocalPath: value.replace(/^file:\/\//, ""),  // strict prefix strip
    value: "",  // text fill path must NOT also try to set the input value
    overwrite: Boolean(extras.overwrite || extras.force)
  };
}
```

Acceptance: existing parse cases (raw selector, `=>` / `:=` / `::`
delimiter, JSON-wrapped) still return their current shape. The new
shape only fires when `value` strictly starts with `file://`.

### 2. New `SENSEI_FILL_FILE_INPUT` message handler

Add a message listener in content_script.js that builds a
`DataTransfer` with a `new File([bytes], filename, {type: mime})` and
assigns it to the target `<input type="file">`:

```js
// content_script.js — sibling to existing __sensei_* handlers
function _b64ToBytes(b64) {
  const bin = atob(b64);
  const len = bin.length;
  const out = new Uint8Array(len);
  for (let i = 0; i < len; i++) out[i] = bin.charCodeAt(i);
  return out;
}

// Inside the existing chrome.runtime.onMessage listener:
if (msg?.type === "SENSEI_FILL_FILE_INPUT") {
  try {
    const el = findElement(msg.selector);
    if (!el || el.tagName !== "INPUT" || el.type !== "file") {
      sendResponse({ ok: false, error: "target is not <input type=file>" });
      return true;
    }
    const bytes = _b64ToBytes(msg.base64 || "");
    const file = new File([bytes], msg.filename || "file.bin", {
      type: msg.mime || "application/octet-stream"
    });
    const dt = new DataTransfer();
    dt.items.add(file);
    el.files = dt.files;
    el.dispatchEvent(new Event("input",  { bubbles: true }));
    el.dispatchEvent(new Event("change", { bubbles: true }));
    sendResponse({
      ok: true,
      filled: msg.selector,
      filename: file.name,
      size: file.size,
      mime: file.type
    });
  } catch (e) {
    sendResponse({ ok: false, error: String(e?.message || e) });
  }
  return true;  // async ack
}
```

Acceptance: the handler returns `{ok: true, filled, filename, size, mime}`
on success and `{ok: false, error}` on every failure (target missing,
not file input, decode failure). The target input's `.files` attribute
is populated; one `input` event + one `change` event fire on it.

### 3. Wire `BROWSER_FILL` executor to use the new path

In `executeBrowserAction` for `kind === "BROWSER_FILL"`, branch on the
new shape:

```js
if (kind === "BROWSER_FILL") {
  const parsed = parseFillTarget(action);
  if (parsed.fileLocalPath) {
    // side_panel.js owns the bytes-fetch; content_script should never
    // get here. If it does, the side panel forgot to intercept the
    // file:// shape before dispatching the action.
    return {
      ok: false,
      error: "file:// BROWSER_FILL must be intercepted in side_panel.js; " +
             "content_script does not fetch local files for security reasons"
    };
  }
  // ... existing text-fill path ...
}
```

This makes the security boundary explicit: only side_panel.js (which
talks to the backend with the user's auth token) is allowed to read
the local résumé file. The content_script never sees `file://` paths.

## What Claude will ship after Codex commits the above

Self-contained — Claude can write this once content_script.js exposes
the listener:

1. `side_panel.js` — extend `parseFillTarget` (or add a sibling
   `inspectFillTargetForFile`) to detect the `file://` shape.
2. `side_panel.js approveAction` — when the action is a file-fill,
   POST to `/extension/read_local_file` with the local path, decode
   the response, send `SENSEI_FILL_FILE_INPUT` to the active tab's
   content_script with `{selector, base64, mime, filename}`.
3. Verification:
   - Add a 12th line to `JOB_APP_CHECKLIST.md` Pass Criteria covering
     résumé upload.
   - Update the test prompt to include `"upload resume from ~/Desktop/resume.pdf"`.
   - Add `test_file_upload_bridge.py` unit test exercising
     `/extension/read_local_file` shape + `parseFillTarget` for the
     `file://` shape (parser-only — DataTransfer can't run in node).
4. Audit row addition: ensure the file-fill audit emits a
   `final_state.file = {name, size, mime}` so the audit trail proves
   the upload bytes survived round-trip.

## Coordination signal

When Codex's content_script.js refactor (load-idempotency guard,
sanitization paths, structural selectors) lands, append a line to the
**bottom of this file** noting the commit SHA. Claude polls this file
on its next session start; the SHA presence is the green light to
ship the side_panel.js half.

If Codex wants to land the file-upload handler in the same commit as
the refactor, fine — just call out `SENSEI_FILL_FILE_INPUT handler` in
the commit body so Claude can grep for it.

## What this is NOT

- NOT a request for Codex to do anything besides the refactor they
  were already going to do, plus the three sections above.
- NOT a change to the security model — local file reads stay on the
  side_panel side, behind backend auth.
- NOT a Phase 2.2 redesign — this is the missing 2.1 slice only.

## Reference

- Commit `7fb259d` (backend half — read_local_file endpoint + resume_path envelope)
- Commit `693a34b` (deterministic acceptance — JOB_APP_CHECKLIST.md + job_app_smoke.html)
- `feedback_dual_agent_surgical_extract.md` — why the split exists
- `feedback_codex_multi_step_standard.md` — six-step pattern; this
  doc covers parser + executor; the audit + verify steps land with
  Claude's follow-on commit.
