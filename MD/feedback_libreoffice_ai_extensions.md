---
name: LibreOffice AI extension stance
description: Prefer functional, supported LibreOffice AI extensions; LibreThinker failure was unopkg/profile-cache diagnostic, not a verdict; Loki requires LibreOffice 24.2+ so it fails on Madam-Mary's 7.3.7 with unsatisfied dependencies; verify with unopkg and configure local Ollama when practical
updated: 2026-04-26
---

Elijah is evaluating LibreOffice AI assistants for Writer/Calc on Madam-Mary. His priority is function and support first: the extension should install cleanly, work well, have active maintenance, useful docs, and a reachable support path. Local Ollama/no-cloud is preferred when practical, but do not choose a worse tool solely because it sounds more private.

System facts:

- LibreOffice exists at `/usr/bin/libreoffice`.
- Extension CLI exists at `/usr/bin/unopkg`.
- Python-based LibreOffice extensions require `libreoffice-script-provider-python` in addition to `python3-uno`. If a Python `.oxt` unpacks but fails while enabling/registering `*.py`, check this package before blaming the download.
- Candidate file `/home/elijah/Downloads/LibreThinker.oxt` exists and is a valid OXT zip.
- LibreThinker was NOT installed during the first 2026-04-26 attempt. `unopkg list` showed no deployed user extensions. The sandboxed install failed at LibreOffice UNO pipe enablement, then normal install revealed the missing Python script provider. That is setup/dependency friction, not proof the extension is bad.
- LibreOffice extensions are NOT Debian packages. `apt list --installed | grep -i libreoffice` only verifies LibreOffice system packages; it cannot prove a `.oxt` Writer extension is installed.
- Correct extension verification command: `unopkg list --verbose`. If it says `All deployed user extensions: <none>`, the extension is not installed, even if apt shows LibreOffice packages.
- 2026-04-26 live correction: Sensei incorrectly checked `apt list` and concluded no extension was found. The right conclusion is: apt was the wrong verification tool.
- Latest verified state after cleanup attempt: `unopkg remove org.librethinker.LibreThinkerExtension` said no such extension deployed. LibreThinker config exists, but the extension is not deployed until `unopkg list --verbose` shows `Identifier: org.librethinker.LibreThinkerExtension` and `is registered: yes`.
- WENT audit, not memory: LibreThinker is the only `.oxt` install attempt seen on disk. The failure went through `unopkg add` plus LibreOffice user-extension cache/profile mismatch, after Python runtime dependency friction. That does not prove whether LibreThinker itself is bad or whether the LibreOffice profile was in a bad extension-cache state. If retrying, reconcile/clean the user extension cache first, run `unopkg add`, verify with `unopkg list --verbose`, and configure local Ollama at `http://localhost:11434/api/chat`. Compare the fresh failure path to the old one before making a verdict.
- Loki WENT audit: `/home/elijah/Downloads/loki-assistent.oxt` is valid, but its manifest requires LibreOffice 24.2+. Madam-Mary has LibreOffice 7.3.7.2, and `unopkg add` failed with `unsatisfied dependencies`. Treat that as a LibreOffice-version blocker, not a registry-cache problem and not a corrupt OXT.

LibreThinker stance:

- It is an AI copilot for LibreOffice Writer: rephrase, summarize, proofread, translate, write.
- It supports hosted/default models, local/remote Ollama, and API-key cloud vendors.
- Therefore do NOT assume LibreThinker is local-only. It is acceptable if it functions better or has stronger support, especially if configured for local Ollama.
- For local Ollama, use model ID shape `sh/ollama/qwen2.5:7b` and endpoint `http://localhost:11434/api/chat`.

Operating rule:

When asked about LibreOffice AI extensions, check installed state before claiming success. Rank by function and support first, then prefer local Ollama/no-cloud configuration where practical. Treat cloud-capable paths as opt-in only.

Never verify `.oxt` installation with `apt list`. Use `unopkg list --verbose`.

LibreThinker local Ollama config lives at:

`/home/elijah/.config/libreoffice/4/user/config/LibreThinkerConfig.json`

Expected values:

- `modelId`: `sh/ollama/qwen2.5:7b`
- `apiKey`: empty string
- `modelUrl`: `http://localhost:11434/api/chat`
