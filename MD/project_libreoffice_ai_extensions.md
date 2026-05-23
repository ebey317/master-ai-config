---
name: LibreOffice AI extensions
description: Sensei knows the local LibreOffice AI-extension options and prefers local Ollama/no-cloud for private documents
updated: 2026-04-26
---

# LibreOffice AI Extensions

Elijah is evaluating AI assistants for LibreOffice Writer/Calc on Madam-Mary.

## Local system facts

- LibreOffice is installed at `/usr/bin/libreoffice`.
- LibreOffice extension manager CLI is installed at `/usr/bin/unopkg`.
- Python-based LibreOffice extensions require `libreoffice-script-provider-python` in addition to `python3-uno`.
- Downloaded candidate extension: `/home/elijah/Downloads/LibreThinker.oxt`.
- Downloaded candidate extension: `/home/elijah/Downloads/loki-assistent.oxt`.
- `LibreThinker.oxt` is a valid zip/OXT extension package and contains Python extension code, sidebar config, manifest, and MPL license.
- First sandbox install attempt did **not** complete. `unopkg list` showed `All deployed user extensions: <none>` after the attempt.
- Failure reason from sandbox: LibreOffice could not enable the extension because UNO pipe connection failed inside the sandbox. A normal install then showed missing `libreoffice-script-provider-python`. This does not prove the extension is bad; it means LibreOffice's Python extension runtime must be present and install should happen from Elijah's normal terminal/GUI session.
- LibreOffice `.oxt` extensions are verified with `unopkg list --verbose`, not `apt list --installed`. Apt only shows system packages such as `libreoffice-writer`; it does not list user-installed Writer extensions.
- 2026-04-26 correction: Sensei checked apt packages and falsely concluded no Writer extension was found. That method is invalid for `.oxt` extension state.
- Latest deployment check: `unopkg remove org.librethinker.LibreThinkerExtension` reported no such extension deployed. Treat LibreThinker as not deployed unless `unopkg list --verbose` shows `Identifier: org.librethinker.LibreThinkerExtension` and `is registered: yes`.
- WENT audit, not memory: the only install attempt on disk so far was LibreThinker. It failed through `unopkg add` and a user-extension cache/profile mismatch path, after earlier missing Python runtime setup. This does **not** yet prove whether the failure is LibreThinker-specific or LibreOffice-profile-generic. After reconciling/cleaning the profile cache, retrying LibreThinker is a diagnostic step: if it works, the reconcile likely fixed the profile; if it fails, compare the new failure against the old one before blaming the extension.
- 2026-04-26 Loki WENT audit: `/home/elijah/Downloads/loki-assistent.oxt` is a valid OXT zip and declares identifier `org.loki.assistant`, version `2.0.7`, display name `LO AI-Assistant` / `LO KI-Assistent`. It declares `LibreOffice-minimal-version value="24.2"`. Madam-Mary currently has `LibreOffice 7.3.7.2`; `unopkg add --suppress-license /home/elijah/Downloads/loki-assistent.oxt` failed with `unsatisfied dependencies`. This is a LibreOffice-version blocker, not a registry/cache blocker and not proof the OXT is corrupt.

## LibreThinker notes

LibreThinker is an AI copilot for LibreOffice Writer. It supports rephrasing, summaries, proofreading, translation, and writing.

It can use:

- default free hosted models
- self-hosted Ollama, local or remote
- API-key providers such as OpenAI/ChatGPT, Mistral, Groq, etc.

For Ollama, its BYOK/Ollama settings use model IDs like:

```text
sh/ollama/qwen2.5:7b
```

Default Ollama endpoint:

```text
http://localhost:11434/api/chat
```

Sensei stance: LibreThinker is acceptable if Elijah explicitly chooses it and configures it for local Ollama, but do not assume it is local-only because it also supports hosted/default/cloud paths. Elijah prefers function and support over ideology: if LibreThinker works better, has clearer docs, active maintenance, and a reachable support path, it can beat a stricter local-only option.

## Operating rule

When Elijah asks which LibreOffice AI extension fits Master AI/Sensei values:

1. Prioritize function and support first: installability, reliability, usable Writer/Calc workflow, active maintenance, clear docs, and reachable support.
2. Prefer local Ollama/no-cloud configuration when the tool supports it.
3. Treat cloud-capable tools as opt-in only.
4. Check whether the extension is actually installed before saying it is installed.
5. Never use `apt list` to verify `.oxt` install state. Use `unopkg list --verbose`.
6. For OXT installs, prefer GUI install from LibreOffice (`Tools > Extension Manager > Add`) or a normal terminal:

```text
unopkg add --suppress-license /path/to/extension.oxt
```

7. After install, verify with:

```text
unopkg list --verbose
```

8. If retrying after a failed LibreThinker attempt, first reconcile/clean the user extension cache, then run `unopkg add`, then verify with `unopkg list --verbose`. Do not treat the retry result as a clean product verdict unless the new error path is separated from the previous cache/profile mismatch.
9. If a LibreOffice extension is installed for Ollama, configure it against Madam-Mary's local Ollama endpoint:

```text
http://localhost:11434/api/chat
```

Recommended model ID for LibreThinker local Ollama:

```text
sh/ollama/qwen2.5:7b
```

LibreThinker config file discovered from its source:

```text
/home/elijah/.config/libreoffice/4/user/config/LibreThinkerConfig.json
```

Expected local config:

```json
{
  "modelId": "sh/ollama/qwen2.5:7b",
  "apiKey": "",
  "modelUrl": "http://localhost:11434/api/chat"
}
```

## Loki notes

Loki / LO AI-Assistant supports Ollama as an offline provider, but the current OXT requires LibreOffice 24.2 or newer. Do not retry Loki on Madam-Mary's current apt LibreOffice 7.3.7 unless LibreOffice is upgraded first.

Discovered default config from the OXT:

```json
{
  "ai": {
    "mode": "online",
    "ollama": {
      "base_url": "http://localhost:11434",
      "model": "llama3.2:latest",
      "auto_install": false
    }
  }
}
```

If Loki becomes installable after LibreOffice upgrade, set it to Ollama/offline and use `base_url` `http://localhost:11434`; model can be `qwen2.5:7b` or another installed Ollama model in normal `name:tag` format.
