# Privacy Gate Handoff - 2026-05-11

Goal: prevent private docs/photos/personal data from leaking into harvest,
few-shot, or future fine-tune datasets.

## Landed

- `harvest.record()` now skips private-looking prompt/response pairs.
- `harvest.few_shot()` ignores private-looking legacy harvest entries.
- `harvest.format_few_shot()` redacts basic email, phone, and SSN patterns.
- `ask_local()` and `ask_local_stream()` no longer record image turns into
  harvest when `image_path` is present.
- New focused tests live at `/home/elijah/scripts/test_harvest_privacy.py`.

## Conservative Private Signals

The harvest privacy gate treats these as private:

- paths under `~/Pictures`, `~/Documents`, `~/Downloads`, `~/Desktop`,
  and `~/jobseeker`
- `.ssh`, `.gnupg`, `.aws/credentials`, `.master_ai_keys`, `.netrc`
- image file paths
- terms such as resume, cover letter, job application, tax, W-2, 1099,
  bank statement, SSN, medical, password, credential, API key, token,
  private key, driver's license, and passport
- common secret/token value patterns

## Still Needed

- Add the same privacy policy to any LoRA dataset builder before export.
- Add a cloud-send guard for turns that contain READ-injected private file
  content.
- Extend READ fence policy if the product should block broad reads of
  `~/Documents`, `~/Pictures`, `~/Downloads`, and `~/Desktop` by default.
- Add a short `privacy status` command so the user can see these protections
  from Sensei.
