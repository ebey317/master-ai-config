# Tool-belt notes: unemployment Uplink code + general browser/email/Drive workflow

**Date:** 2026-06-12  
**Lesson:** Tools are the belt; the toolbox is the repeatable workflow. This doc records the exact sequence so the next session does not guess.

---

## 1. The unemployment/Uplink code loop

### Goal
Get the Indiana Uplink access code from email, then use it to log into unemployment.

### Exact tool chain
1. **Email bridge — find the account(s)**  
   `mcp__email-bridge__list_accounts` → see which accounts are configured and authenticated.

2. **Search the likely account**  
   The Uplink code comes from `dwd@subscription.in.gov`. Search terms that work:
   - `Uplink`
   - `Indiana`
   - `Department of Workforce Development`
   
   Use `mcp__email-bridge__search_inbox` on `gmail` first (that is where it landed today).

3. **Read the email**  
   `mcp__email-bridge__read_email(account="gmail", uid=<uid>)`  
   The access code is in the body: `Your Uplink Logon Access Code is ######`.

4. **Browser — make sure the right tab is active**  
   - `tab_list` to see tabs.
   - `tab_switch` to the Uplink/Indiana tab if it exists.
   - If it does not exist, `browse` to `https://uplink.in.gov/CSS/Logon2FA.htm`.

5. **X-ray before acting**  
   `read_full` to get selectors and field labels. Uplink uses generated/complex selectors, so the accessibility tree is essential.

6. **Fill the code**  
   Use `fill` with the `::` delimiter workaround because the loaded extension ignores the separate `text` value:  
   `fill(where="<selector> :: 800135", text="")`  
   (or use `fill_form` if there are multiple fields on the page).

7. **Click/Submit**  
   - If the button is a plain button, `click(what="<selector or visible text>")`.
   - If it is a submit button, the extension will `first_submit_pause`. Bypass options:
     - Pass `intercept_popup=true` in `click`.
     - Or navigate directly to the post-submit URL if known.

8. **Evidence**  
   `screenshot` after login to confirm state.

---

## 2. General Google search workflow (no backend)

- Going to `google.com`, typing, and clicking **Google Search** triggers `first_submit_pause`.
- Faster, repeatable path: use the `search` tool or `browse` directly to `https://www.google.com/search?q=<query>`.
- This avoids the submit-pause trap and returns results immediately.

Example: `search(query="Elijah McCoy")` landed on results with Wikipedia + overview.

---

## 3. Google Drive workflow (no backend connector)

- Drive's DOM is enormous. Raise the cap before reading:  
  `SENSEI_READ_FULL_MAX_CHARS=500000` in the server env.
- Use Drive's search URL instead of browsing folders:  
  `https://drive.google.com/drive/search?q=resume`
- `read_full` returns rows with full file names and selectors.
- Clicking folder icons in Drive is unreliable; searching by keyword is more stable.
- Resume folder found today: **Resume** (Desert sand color), containing:
  - `Elijah Wilkins Resume 5Year.docx/.pdf`
  - `Elijah Wilkins Resume Detailed.docx/.pdf`
  - `Elijah Wilkins Resume 2025.docx`
  - `AI Query - Elijah Wilkins Job Application Reference`
  - `Elijah Wilkins — Applications Log`

---

## 4. Active-tab discipline (the thing that breaks silently)

- `read_full`, `fill`, `click`, and `screenshot` all operate on the **active browser tab**.
- If a new tab was opened earlier, always:
  1. `tab_list`
  2. `tab_switch(tab_id="<id>")` to the target tab
  3. Then act.
- This is why the first `fill_form` test on the complex form returned empty — the active tab was a blank new tab, not the form tab.

---

## 5. Tool-scheme awareness (the toolbox)

Current Sensei surface after trimming:

- **Navigate:** `browse`, `search`, `tab_create`, `tab_switch`, `tab_close`, `tab_list`
- **X-ray:** `read`, `read_full`, `screenshot`
- **Act:** `click`, `fill`, `fill_form`, `double_click`, `hover`, `upload_file`
- **Timing/sequence:** `wait`, `scroll`, `batch`
- **Health:** `health`

Default plan mapping for common requests:

| Request | First move | Fallback |
|---|---|---|
| "go to X" / "open X" | `browse` or `tab_create` | `tab_switch` if already open |
| "search Google for X" | `search(query=X)` | `browse` to `google.com/search?q=X` |
| "what's on the page?" | `read_full` | `screenshot` |
| "fill this form" | `fill_form` | `fill` with `::` delimiter per field |
| "click submit" | `click(what="...", intercept_popup=true)` | Direct URL navigation |
| "check my email for X" | `list_accounts` → `search_inbox(account, query=X)` | `check_inbox` |
| "go to Google Drive and find Y" | `browse` to `drive.google.com/drive/search?q=Y` with large read cap | — |

---

## 6. Hard form test (Mary's test)

Built `/tmp/hardform.html` with 34 tricky fields: aria-label-only inputs, duplicate labels, selects with placeholders/optgroups, radios, consent + opt-out checkboxes, textareas, sensitive fields, disabled/readonly/hidden fields, and skill buttons.

Chain script: `/tmp/hard_test_chain.sh`

Result after tuning (latest hard run):
- Desired salary → `150000`
- Country → `af` (Afghanistan)
- Job title → `devops` (DevOps Engineer)
- Experience → `2` years, level `mid`
- Contact → `mail`
- Newsletter → checked
- Skills → `Go` clicked as button checkbox
- Sensitive/disabled/hidden/opt-out fields correctly skipped
- Radio buttons are now selected by **clicking the specific option**, not by setting value on the first radio

### 42-field checkbox stress test

Expanded `/tmp/hardform.html` to **42 fillable fields** with a new checkbox fieldset:
- certify, job alerts, license, relocate, available, authorized, remote, background check

Result:
```
discovered: 42 | filled: 34 | skipped: 8 | failed: 0 | verified: 34/34
```

The 8 skipped are the expected ones: opt-out, password/SSN/CC/CVV, disabled, readonly, and the skill checkbox group (only `Go` is clicked manually in the chain).

One manual reload note below.

---

## 7. `hover` tool reload note

`hover` was added back to the Sensei MCP tool belt and a `BROWSER_HOVER` handler was added to `~/projects/master-ai/sensei_extension/content_script.js`. Because Chrome extensions only load updated content scripts after a reload, **hover returns `unsupported` until the Sensei extension is reloaded**. After reload it will dispatch `mouseenter/mouseover/mousemove` on the target.

To reload: open `chrome://extensions`, find **Sensei**, and click the circular reload arrow.

---

## 8. First-submit pause handling

Any `<button type="submit">`, Google Search, Indeed Apply, etc., will pause on the first attempt. Two reliable bypasses:

1. **Direct URL:** bypass the click entirely (best for search forms).
2. **`intercept_popup=true`** in `click` (best when the click itself carries needed state).

Do not expect a normal `click` on a submit button to complete on the first try.

---

## 7. Today's Uplink access code

**800135** — captured from `dwd@subscription.in.gov` on `gmail` at 2026-06-12 16:26:11 -0600.

---

## 9. Google Drive file-ID extraction (the missing link)

Drive's accessibility tree gives row selectors, but the **file ID** lives in a `data-id` attribute on an element inside the first `<td>`. The clean workflow:

1. **Search Drive** by keyword to get a short list:
   ```
   browse(url="https://drive.google.com/drive/u/0/search?q=AI+Query+Elijah")
   ```
2. **Run `drive_inspect`** (new tool in Sensei belt) to get each row's CSS selector.
3. **`get_dom(selector=<row_selector>)`** returns the `<td>` outerHTML.
4. **Parse `data-id="..."`** from the HTML.
5. **Download docs** with the reliable export endpoint:
   ```
   https://docs.google.com/feeds/download/documents/export/Export?id=<data-id>&exportFormat=txt
   https://docs.google.com/feeds/download/documents/export/Export?id=<data-id>&exportFormat=docx
   https://docs.google.com/feeds/download/documents/export/Export?id=<data-id>&exportFormat=pdf
   ```
   This avoids the mobilebasic redirect problem and lands a local file in `~/Downloads`.
6. **Download PDFs** with:
   ```
   https://drive.google.com/uc?export=download&id=<data-id>
   ```

Found IDs today:
- **AI Query doc:** `1IhFP75OAehPMojsxyi1WOrb5BI2YSxfQad6-cesof74`
- **Applications Log:** `1Dp1IPUSXoY7mXM1FNTMtpfNZdGH_YxRqvs3bYaj6Bpc`
- **Resume 5Year PDF:** `10keYh7NfxkIPScVV6or8_GUniRDUwWx1`

---

## 10. DOM schema: Google Drive search-result row

```json
{
  "role": "row",
  "name": "<file name>",
  "text": "<file name> <date> <owner> <folder> More actions (Alt+A)",
  "selector": "div#\\:<id> > ... > tbody:nth-of-type(1) > tr:nth-of-type(<n>) > td:nth-of-type(1)",
  "href": "",
  "aria_label": "",
  "is_folder": false,
  "kind": "unknown"
}
```

Inside the `<td>` HTML:
- File icon / thumbnail: `<div class="FAGDGb" aria-hidden="true" data-id="<FILE_ID>">`
- File name container: `<div class="JxSEve" aria-label="<file name> Google Docs">`
- Name span: `<span class="WQJtxb"><strong class="DNoYtb"><file name></strong></span>`

So the canonical extraction is:
1. Match the row by `name` from `drive_inspect`.
2. `get_dom` the row selector.
3. Regex `data-id="([^"]+)"` → file ID.

---

## 11. Resume-based job search

When Elijah says "find jobs based off my résumé":

1. **Locate the résumé** in Drive → `Resume` folder.
2. **Download the PDF** via `uc?export=download&id=<data-id>`.
3. **Extract text** with `pdftotext <file> -` (no Python PDF deps needed).
4. **Build keyword rotations** from the résumé:
   - Primary: HVAC, HVAC install, HVAC service, facilities maintenance, industrial maintenance, sheet metal
   - Secondary: warehouse, forklift, general labor, maintenance technician
   - Avoid: apartment maintenance, CDL/diesel, sales, senior/lead (except HVAC install lead), software/IT
5. **Run searches** on:
   - Honest Jobs (`https://app.honestjobs.com/job-seeker/dashboard/job-search`)
   - Indeed (`https://www.indeed.com/jobs?q=<kw>&l=Indianapolis%2C+IN`)
   - ZipRecruiter (`https://www.ziprecruiter.com/jobs-search?search=<kw>&location=Indianapolis%2C+IN`)
6. **Filter each posting** against AI Query rules:
   - IN-fit role type
   - Pay floor $45k/yr (~$21.63/hr top-of-range)
   - Indianapolis area or remote
   - Not in DO-NOT-RE-APPLY list
   - Fresh (<30 days, prefer <7)
7. **Open posting → apply → verify confirmation email → log it.**

Résumé highlights from `Elijah Wilkins Resume 5Year.pdf`:
- 5+ years HVAC install/service, sheet-metal fabrication, facilities maintenance
- EPA Type I & II, Made Ready Technician, blueprint/layout, forklifts/lifts
- Indianapolis-based, open to remote, 1st shift preferred
- Target: $50k+/yr, minimum $45k/yr

**Automated helpers:** see `~/projects/master-ai-config/MD/job_application_runbook.md` for the small-model step-by-step and the three new subagents (`profile_fetcher`, `posting_inspector`, `application_logger`) in `~/scripts/subagents/`.

---

## 12. Indeed Apply chain (captured 2026-06-12)

A repeatable, end-to-end flow that worked for three verified applications today.

### 12.1 Load the rules

Run the `profile_fetcher` subagent once per session:

```python
import sys, os
sys.path.insert(0, os.path.expanduser("~/projects/master-ai"))
from subagent_registry import run as run_sa
profile = run_sa("profile_fetcher")["profile"]
```

This loads `AI Query - Elijah Wilkins Job Application Reference.txt`, the Applications Log, and the résumé path.

### 12.2 Search and qualify

Open Indeed by direct URL to avoid the first-submit pause:

```
browse(url="https://www.indeed.com/jobs?q=HVAC%20Installer&l=Indianapolis%2C%20IN")
```

For each result, collect `title`, `company`, `location`, `pay`, `apply_url`, and `#jobDescriptionText` HTML, then call:

```python
import json
task = json.dumps({
    "title": "...", "company": "...", "location": "...",
    "pay": "...", "description_html": "...", "apply_url": "..."
})
result = run_sa("posting_inspector", task=task, context={"profile": profile})
```

Proceed only if `result["qualified"]` is `true`.

### 12.3 Apply

1. `tab_list` → `tab_switch` to the posting tab.
2. Click `#indeedApplyButton` with `execute_js` `click_selector` (this bypasses the first-submit pause):
   ```
   execute_js(command="click_selector", selector="#indeedApplyButton", all_frames=true)
   ```
3. Fill with `fill_form(profile=...)`. Dry-run first on long forms.
4. Submit with `execute_js` `click_selector` on the final `button[type='submit']`.

### 12.4 Verify

Confirmation emails today landed in **AOL** (`ewilkinssr@aol.com`) from `indeedapply@indeed.com`. Search there and read the matching subject before logging.

### 12.5 Log

```python
entry = json.dumps({
    "date": "2026-06-12",
    "company": "...",
    "title": "...",
    "location": "...",
    "pay": "...",
    "source": "Indeed",
    "description_summary": "...",
    "contact": "Contact not provided in posting; follow via Indeed Apply.",
    "confirmation": {"account": "AOL", "sender": "indeedapply@indeed.com", "subject": "Indeed Application: ..."},
    "apply_url": "...",
    "status": "VERIFIED"
})
run_sa("application_logger", task=entry, context={"profile": profile})
```

### 12.6 Key gotchas

- **Active-tab discipline:** `read_full`, `fill`, `click`, and `screenshot` operate on the active tab. Always `tab_switch` first.
- **First-submit pause:** Normal `click` on `#indeedApplyButton` pauses. Use `execute_js click_selector` or `click(..., intercept_popup=true)`.
- **No confirmation = no log.** Only log entries with a confirmation email in AOL/Gmail.
- **DO-NOT-RE-APPLY:** The `posting_inspector` checks the company list; still scan the log header manually when in doubt.

### 12.7 One-command driver for the small model

For hands-off repetition, use the driver script instead of step-by-step browser tools:

```bash
# dry-run: see which postings are qualified
python3 ~/scripts/job_application_driver.py --query "HVAC Installer" --count 3

# actually apply
python3 ~/scripts/job_application_driver.py --query "HVAC Installer" --count 3 --apply
```

The driver enforces the 4-tab maximum, handles search/qualify/apply, and returns a JSON report. After it runs, the small model only needs to verify the AOL confirmation email and call `application_logger` for each applied job. Full details in `~/projects/master-ai-config/MD/job_application_runbook.md` §13.
