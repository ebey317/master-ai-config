# Few-shot A/B - 2026-05-11

Status: not completed.

## What Happened

The first run was invalid because Codex sandbox networking blocked localhost
Ollama access:

```text
OLLAMA_ERROR: <urlopen error [Errno 1] Operation not permitted>
```

That produced `(no response)` for all prompts and must not be treated as a
real A/B result.

The second run was allowed to reach local Ollama, but it became CPU-bound and
was stopped before completion. Running the full 9-prompt x 2-pass harness on
this machine is too expensive for this cleanup/privacy round.

## Current State

- Few-shot code is wired.
- Sensei command exists: `few_shot on|off|status`.
- Current setting is restored to `FEW_SHOT=0`.
- No LoRA training was started.
- No valid A/B verdict exists yet.

## Useful Result From This Round

Before any A/B or LoRA work, a conservative harvest privacy gate was added:

- private-looking prompts/responses are skipped by `harvest.record()`
- old private-looking harvest entries are ignored by `harvest.few_shot()`
- image turns are not recorded into harvest from local vision calls
- contact data is redacted when formatting few-shot examples

Verification passed:

- `python3 -m py_compile harvest.py master_ai.py ab_few_shot.py test_harvest_privacy.py`
- `python3 -m unittest -q /home/elijah/scripts/test_harvest_privacy.py`
- `python3 /home/elijah/scripts/test_master_ai_parser.py` - 77 tests OK

## Recommendation

Do not turn `FEW_SHOT=1` on globally yet. For the next pass, test only 2-3
prompts from inside the normal Sensei/Claude environment, or change the harness
to use a much smaller/warmer local model with a hard per-call timeout.
