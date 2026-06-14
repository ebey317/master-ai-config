# Job-application runbook: Indeed Apply (small-model edition)

**Goal:** Open an Indeed search result, qualify it against Elijah's rules, apply, verify the confirmation email, and log it — using only the trimmed Sensei tool belt and the three job-application subagents.

**Files this runbook depends on:**
- `~/projects/master-ai/subagent_registry.py` (loads subagents automatically)
- `~/scripts/subagents/profile_fetcher.py`
- `~/scripts/subagents/posting_inspector.py`
- `~/scripts/subagents/application_logger.py`
- `~/Downloads/AI Query - Elijah Wilkins Job Application Reference.txt`
- `~/Downloads/Elijah Wilkins — Applications Log.txt`
- `~/Downloads/Elijah Wilkins Resume 5Year.pdf`

---

## 0. Load the profile once per session

This gives you a compact profile dict and the DO-NOT-RE-APPLY list.

```python
import sys
sys.path.insert(0, os.path.expanduser("~/projects/master-ai"))
from subagent_registry import run as run_sa
profile_result = run_sa("profile_fetcher")
profile = profile_result["profile"]
```

If `profile_result["ok"]` is not true, stop and read the error.

---

## 1. Find postings on Indeed

Use a direct URL search so you never hit the Google/Indeed first-submit pause.

### Tool call
```json
{
  "tool": "browse",
  "args": {
    "url": "https://www.indeed.com/jobs?q=HVAC%20Installer&l=Indianapolis%2C%20IN"
  }
}
```

### Rotation queries (one per run)
- `HVAC%20Installer`
- `HVAC%20Service%20Technician`
- `Facilities%20Maintenance%20Technician`
- `Industrial%20Maintenance%20Technician`
- `Sheet%20Metal%20Installer`
- `Warehouse%20Forklift%20Indianapolis`

After the page loads, `read_full` to see the result list.

---

## 2. Extract one posting's data

For each job card you want to evaluate, capture:

| Field | Where it comes from |
|-------|---------------------|
| `title` | Job card heading or detail page `<h1>` |
| `company` | Company name below the title |
| `location` | Location string (e.g. "Indianapolis, IN") |
| `pay` | Salary snippet (e.g. "$35–$45 an hour") |
| `apply_url` | The job-card URL / `vjk` link |
| `description_html` | `outerHTML` of `#jobDescriptionText` |

If the description is not visible on the search page, open the posting first:

```json
{
  "tool": "tab_create",
  "args": {
    "url": "<apply_url>"
  }
}
```

Then:

```json
{
  "tool": "get_dom",
  "args": {
    "selector": "#jobDescriptionText"
  }
}
```

---

## 3. Qualify the posting with the subagent

Build a JSON task and call `posting_inspector`.

```python
import json
import datetime
task = json.dumps({
    "title": "<title>",
    "company": "<company>",
    "location": "<location>",
    "pay": "<pay>",
    "description_html": "<description html>",
    "apply_url": "<apply_url>"
})
result = run_sa("posting_inspector", task=task, context={"profile": profile})
```

### Decision rules
- If `result["qualified"]` is `true`: continue to apply.
- If `false`: skip. `result["reason"]` tells you why.
- If `result["ok"]` is false: fix the input JSON and retry.

---

## 4. Apply on Indeed

### 4.1 Active-tab check
Before every action, make sure the posting tab is active:

```json
{"tool": "tab_list"}
{"tool": "tab_switch", "args": {"tab_id": "<posting_tab_id>"}}
```

### 4.2 Click the Apply button (first-submit bypass)

The regular `click` tool pauses on submit-like buttons. Use `execute_js` with `click_selector` instead.

```json
{
  "tool": "execute_js",
  "args": {
    "command": "click_selector",
    "selector": "#indeedApplyButton",
    "all_frames": true
  }
}
```

If that fails, fallback:

```json
{
  "tool": "click",
  "args": {
    "what": "#indeedApplyButton",
    "intercept_popup": true
  }
}
```

Wait for the application modal to appear:

```json
{"tool": "wait", "args": {"seconds": 3}}
```

### 4.3 Fill the application

Use `fill_form` with Elijah's profile. It handles most fields automatically.

```json
{
  "tool": "fill_form",
  "args": {
    "profile": {
      "full_name": "Elijah Wilkins",
      "first_name": "Elijah",
      "last_name": "Wilkins",
      "email": "ewilkinssr@aol.com",
      "phone": "317-760-9436",
      "address": "5113 E 32nd St, Indianapolis, IN 46218",
      "city": "Indianapolis",
      "state": "IN",
      "zip": "46218",
      "country": "United States",
      "authorized_us": true,
      "requires_sponsorship": false,
      "has_cdl": false,
      "willing_drug_test": true,
      "willing_background_check": true,
      "drivers_license_valid": true,
      "felony": false,
      "desired_salary": 50000,
      "min_hourly": 21.63,
      "resume_path": "/home/elijah/Downloads/Elijah Wilkins Resume 5Year.pdf"
    }
  }
}
```

Review the `fill_form` dry-run first if the form is long:

```json
{
  "tool": "fill_form",
  "args": {
    "dry_run": true,
    "profile": { ... }
  }
}
```

Fix any individual fields with `fill`:

```json
{
  "tool": "fill",
  "args": {
    "where": "#input-phone :: 317-760-9436",
    "text": ""
  }
}
```

### 4.4 Upload résumé if asked

```json
{
  "tool": "upload_file",
  "args": {
    "selector": "input[type='file']",
    "file_path": "/home/elijah/Downloads/Elijah Wilkins Resume 5Year.pdf"
  }
}
```

### 4.5 Submit the application

Look at the modal for the final submit button. Use `execute_js` `click_selector` on its selector.

```json
{
  "tool": "execute_js",
  "args": {
    "command": "click_selector",
    "selector": "button[type='submit']",
    "all_frames": true
  }
}
```

If `fill_form` has a `submit` arg and you trust it, you can pass `"submit": true`.

---

## 5. Verify the confirmation email

All three successful applications today confirmed in the **AOL** inbox from `indeedapply@indeed.com`.

```json
{
  "tool": "mcp__email-bridge__search_inbox",
  "args": {
    "account": "aol",
    "query": "FROM indeedapply@indeed.com",
    "limit": 10
  }
}
```

Find the matching subject (e.g. `"Indeed Application: <job title>"`). Read it to confirm:

```json
{
  "tool": "mcp__email-bridge__read_email",
  "args": {
    "account": "aol",
    "uid": "<uid>"
  }
}
```

Do not log the application until the confirmation email is found.

---

## 6. Log the verified application

Build the entry JSON and call `application_logger`.

```python
import json
entry = json.dumps({
    "date": datetime.date.today().isoformat(),
    "company": "<company>",
    "title": "<title>",
    "location": "<location>",
    "pay": "<pay>",
    "source": "Indeed",
    "description_summary": "<1-2 sentence summary>",
    "contact": "Contact not provided in posting; follow via Indeed Apply.",
    "confirmation": {
        "account": "AOL",
        "sender": "indeedapply@indeed.com",
        "subject": "Indeed Application: <title>"
    },
    "apply_url": "<apply_url>",
    "status": "VERIFIED"
})
log_result = run_sa("application_logger", task=entry, context={"profile": profile})
```

Check `log_result["ok"]` is true. If false, fix the error and retry.

---

## 7. Close the posting tab and repeat

```json
{"tool": "tab_close", "args": {"tab_id": "<posting_tab_id>"}}
```

Switch back to the Indeed search tab and pick the next result.

---

## Quick checklist (say this to yourself before each apply)

1. Profile loaded? ✅
2. Posting qualified by `posting_inspector`? ✅
3. Correct active tab? ✅
4. Clicked `#indeedApplyButton` with `execute_js`? ✅
5. Form filled and submitted? ✅
6. Confirmation email in AOL? ✅
7. Logged with `application_logger`? ✅

---

## Common failure modes

| Symptom | Fix |
|---------|-----|
| `click` on Apply does nothing / pauses | Use `execute_js` `click_selector` on `#indeedApplyButton` |
| Modal opens but fields stay empty | Use `fill_form` with the profile JSON, or `fill` with `::` delimiter |
| Action happens on the wrong page | `tab_list` → `tab_switch` to the posting tab |
| No confirmation email | Wait 30–60 seconds, search AOL again; do not log until found |
| `posting_inspector` says "No IN-fit keyword matched" | Check that `description_html` is the full `#jobDescriptionText` block |
| Log entry duplicated | The logger skips duplicate DO-NOT-RE-APPLY lines, but always read the tail of the log to be sure |

---

## Minimal working example (one posting end-to-end)

```python
import json, sys, os
sys.path.insert(0, os.path.expanduser("~/projects/master-ai"))
from subagent_registry import run as run_sa

profile = run_sa("profile_fetcher")["profile"]

task = json.dumps({
    "title": "Experienced HVAC New Construction Installer",
    "company": "Price Point Comfort, LLC",
    "location": "Indianapolis, IN",
    "pay": "$60,000 a year",
    "description_html": "<div id='jobDescriptionText'>...</div>",
    "apply_url": "https://www.indeed.com/jobs?vjk=..."
})
print(run_sa("posting_inspector", task=task, context={"profile": profile}))
# → qualified: true → apply, then verify email, then log.
```

---

## 13. One-command driver (recommended for the small model)

If the browser workflow above is too much for the small model, use the driver script instead. It does the search, qualification, and apply loop for you. The small model only has to run one Bash command and then verify/log each result.

### 13.1 Dry-run (find qualified postings, do not apply)

```bash
python3 ~/scripts/job_application_driver.py --query "HVAC Installer" --count 3
```

Output is JSON. Look for entries with `"status": "QUALIFIED_DRY_RUN"`.

### 13.2 Apply to N qualified postings

```bash
python3 ~/scripts/job_application_driver.py --query "HVAC Installer" --count 3 --apply
```

Output JSON will have `"status": "APPLIED_AWAITING_EMAIL"` and an `"expected_subject"` for each successful application.

### 13.3 After the driver finishes — verify and log

For each job where `"applied": true`:

1. Search AOL for the expected subject:
   ```json
   {
     "tool": "mcp__email-bridge__search_inbox",
     "args": {
       "account": "aol",
       "query": "FROM indeedapply@indeed.com SUBJECT \"Indeed Application: <title>\"",
       "limit": 5
     }
   }
   ```
2. Read the matching email.
3. Call `application_logger` with the confirmation details.

### 13.4 Driver tab budget

The driver enforces a hard 4-tab maximum by trimming oldest tabs before it starts and by closing each posting tab after it finishes. Do not manually open extra tabs while the driver is running.

### 13.5 Driver limitations

- It cannot verify AOL email by itself (that still uses the assistant's email MCP tools).
- It will stop at the first `count` qualified postings.
- If Indeed changes its DOM, the extraction selectors may need updating in `~/scripts/job_application_driver.py`.
