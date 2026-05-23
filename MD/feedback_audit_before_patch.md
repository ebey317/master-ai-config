---
name: Audit before patching config/data files
description: For any YAML/JSON/.env/config-file fix — run cat -A first, edit surgically (Edit not Write), verify with the file's canonical parser, diff against last-working before reasoning from scratch
type: feedback
originSessionId: bbd2f998-d0ee-4d15-ad76-2ed73466b414
---
When fixing a config or data file (profile.yaml, settings.json, .env, etc.), run this 4-step protocol BEFORE patching:

1. **Audit before patch** — `cat -A` or `od -c` on the affected lines. Visible-only review misses invisible whitespace: tabs in space-indented blocks, mixed indent levels, trailing CR (`^M`) from Windows-style paste, no-break-space, BOM.

2. **Surgical fix only** — Edit tool, not Write. Don't regenerate the file from scratch. Long config files (profile.yaml, settings.json) carry data outside the broken region; a full rewrite risks dropping it silently.

3. **Verify with the file's canonical parser** — `bash -n` and visual review aren't enough. Use:
   - YAML: `python3 -c "import yaml; yaml.safe_load(open('PATH'))"` — silent exit = clean
   - JSON: `python3 -m json.tool PATH > /dev/null` or `jq . PATH > /dev/null`
   - .env: `( set -a; source PATH; set +a )` in a subshell

4. **Diff against last-working** — `git log -- PATH`, `.bak` files, shell history. Finding what changed at the broken line is faster than re-reasoning the file structure from scratch.

**Why:** Born from the 2026-05-09 jobseeker profile.yaml break, where the same bug fired twice in one session — Elijah pasted values next to existing `""` quotes via voice-to-text. The first patch was rushed (read-then-Edit, no audit). The second time he stopped me and named the protocol explicitly. Each step exists because skipping it has burned us: visible-only review misses invisible chars; full rewrites drop unrelated data; `bash -n` doesn't catch parser-level errors; reasoning from scratch wastes tokens vs. diffing.

**How to apply:** Any time you're about to edit a config or data file because something broke. Especially if the same bug fires twice in a session — that's the cue to audit *harder*, not patch *faster*. Pairs with `feedback_long_session_verification.md` (long-session trigger) and `feedback_codex_multi_step_standard.md` (deterministic verify per layer).
