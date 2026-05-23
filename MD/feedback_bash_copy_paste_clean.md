---
name: Bash output must be copy-paste-clean
description: When Claude Code or Sensei shows a bash command, Elijah needs to see EXACTLY the characters to paste — no leading decoration, no fence markers, no language tags. He is still learning which chars are decoration vs part of the command; ambiguity breaks the paste.
type: feedback
originSessionId: 39d12576-f270-4aa3-8989-c741ebeec446
---
## Rule

When giving Elijah a command to run, show **only what he should paste**. Nothing decorative in front, nothing decorative wrapping it. If he sees it on screen, it should be safe to select-all and paste.

## Why (confirmed 2026-04-20)

Elijah copy/pastes from a phone. He's **learning** the conventions — he doesn't yet know which leading characters are formatting vs part of the command. Direct quote: *"I'm not sure what's decorative and what actual information that needs to be input."* Examples of what confuses him:

- **Leading prompt prefixes**: `$ ls` or `> cd ~` — is the `$` / `>` part of the command? (No, but he can't tell at a glance.)
- **Bullet markers**: `- apt update` or `* apt update` — is the dash part of the command? (No, but again, not obvious.)
- **Code-fence backticks**: the opening triple-backtick-plus-language line and the closing triple-backtick line — does he copy those? (No, they're markdown syntax.)
- **Language tags on fences**: triple-backtick-bash — the word `bash` isn't a command, it's just markdown telling the renderer what to highlight.
- **Multi-line blocks**: when several lines of setup are shown, he's not sure which lines belong and which are commentary.

## How to apply — Claude Code output

**Single-line commands**: put the command on its own line, inline-code style, nothing in front.

Right:
`apt update`

Wrong:
`$ apt update`
`* apt update`
`- apt update`

**Multi-line commands or scripts**: use a fenced block but pair it with a one-liner that says "paste the lines between the fences, not the fences themselves" — or better, keep multi-line snippets to a minimum; prefer single commands joined with `&&`.

**Never** put a language tag (`bash`, `python`) in a context where Elijah might copy it. If the fence is for him to paste from, leave it unlabeled.

**Inside explanatory prose**, if referencing a command, use inline backticks: "the command is `npm run build`." Don't write "the command is $ npm run build".

## How to apply — Sensei output (needs patching into Sensei code)

- Model system prompt / response formatter must strip leading `$ `, `> `, `- `, `* ` before showing a shell command.
- Sensei's CREATE:/EDIT:/RUN: directive output already avoids prefixes — good. Keep it that way. The problem is mostly in the narrative text around directives.
- When Sensei suggests "try `$ foo`", the `$ ` needs to go before display.
- Code-fence rendering in Pupil: strip language tag from the visible block, or render the fence boundaries differently (a subtle border) so they aren't confusable with content.

## What was confirmed as NOT the main issue

Em-dashes / en-dashes / curly quotes were my first guess. They're still worth avoiding, but they are not the repeat offender. The actual repeat offenders are **prompt prefixes and bullets**, and the **uncertainty around code-fence markers and language tags** on multi-line snippets.
