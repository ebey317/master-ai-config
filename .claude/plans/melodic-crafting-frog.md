# Dezzy Zero — Research Intel + Next Actions

*Updated 2026-05-23 after 3-agent broad GitHub sweep*

---

## What We Have (Current Stack — Wired & Working)

| File | Status |
|------|--------|
| `~/scripts/skill_runtime.py` | ✅ Step machine runtime |
| `~/scripts/skills/apply-job-session/recipe.py` | ✅ 1928-line skill, adapters for Indeed + ZipRecruiter v1 |
| `~/scripts/browser_bridge.py` | ✅ BROWSER_* → sensei HTTP bridge |
| `~/scripts/run_apply_skill.py` | ✅ Outer loop: skill_runtime ↔ browser_bridge |
| `~/.master_ai_profile.json` | ✅ Elijah's profile (chmod 600) |
| `~/.master_ai_drive_refs.json` | ✅ Drive refs skeleton (chmod 600, PLACEHOLDERs) |
| `~/MD/secretary_context.md` | ✅ Secretary identity doc |

**Dry run verified:** `python3 run_apply_skill.py --dry-run <url>` walks all steps to log_session INTERRUPT.

---

## 🏆 Best Finds — 3-Agent Research Sweep

### Directly Parallel Projects (Study & Mine)

| Repo | Why It Matters |
|------|---------------|
| [career-ops](https://github.com/santifer/career-ops) | Claude Code + 14 skill modes + 45+ company portals + Playwright + PDF gen. Closest parallel to us. Mine portal scanning arch + sub-agent orchestration. |
| [job-apply-plugin](https://github.com/neonwatty/job-apply-plugin) | Claude Code plugin for LinkedIn, Greenhouse, Ashby, Workday. Same stack, same target boards. Mine per-ATS adapter + field selector maps. |
| [job-applier-agent](https://github.com/theaayushstha1/job-applier-agent) | Playwright MCP + form fill + local-first + user confirmation gates. Mirrors our recipe.py design. |
| [Jobs_Applier_AI_Agent_AIHawk](https://github.com/feder-cr/Jobs_Applier_AI_Agent_AIHawk) | Largest community, battle-tested form-fill logic. Best selector maps + anti-bot delay patterns. |
| [auto-apply-bot](https://github.com/LuisMIguelFurlanettoSousa/auto-apply-bot) | Uses Playwright MCP directly as browser layer. Telegram notifications. Scoring. |
| [job-application-bot-by-ollama-ai](https://github.com/lookr-fyi/job-application-bot-by-ollama-ai) | Ollama (local LLM, same as CLAF) for fill decisions. Semantic dedup filter. |
| [claude-quickstarts browser demo](https://github.com/anthropics/claude-quickstarts) | Official Anthropic Playwright browser tool implementation. Reference. |

### Claude Code Infrastructure (Steal Patterns)

| Repo | Pattern |
|------|---------|
| [awesome-claude-code-workflows](https://github.com/ithiria894/awesome-claude-code-workflows) | 16 categories: Virtual Engineering Teams, Plan-Build-Review pipelines, Autonomous Loops, Context/Memory ledgers |
| [claude-code-hooks-mastery](https://github.com/disler/claude-code-hooks-mastery) | Hook lifecycle: SessionStart, PreToolUse, PostToolUse, Notification. Exit code 2 = block. |
| [karanb192/claude-code-hooks](https://github.com/karanb192/claude-code-hooks) | Production hook examples: block-dangerous-commands, auto-stage, notify-permission |
| [ralph-claude-code](https://github.com/frankbria/ralph-claude-code) | Autonomous loop with dual-condition exit (≥2 completion signals + explicit EXIT_SIGNAL) + circuit breaker |
| [AgentManager](https://github.com/simonstaton/AgentManager) | Parent-child agent hierarchy (max depth 3, 20 children), pub/sub message bus, shared markdown context |
| [wshobson/agents](https://github.com/wshobson/agents) | 191 agents, 155 skills, 102 commands. Three-tier model routing: Opus=critical, Sonnet=execution, Haiku=fast ops |
| [awesome-claude-code-subagents](https://github.com/VoltAgent/awesome-claude-code-subagents) | 100+ specialized subagents, tool-gated access pattern |

### Browser Layer Options (Ranked)

| Option | Bot Resistance | Auth Reuse | JS/CDP | Status |
|--------|---------------|-----------|--------|--------|
| **Glance MCP** ([DebugBase/glance](https://github.com/DebugBase/glance)) | ✅ Real browser | ✅ Existing session | ✅ 30 tools | Not installed |
| **Chrome DevTools MCP** ([ChromeDevTools](https://github.com/ChromeDevTools/chrome-devtools-mcp)) | ✅ Real browser | ✅ | ✅ 43+ tools | Not installed |
| **Browser MCP** ([browsermcp/mcp](https://github.com/browsermcp/mcp)) | ✅ Real browser | ✅ | ✅ CDP | Not installed |
| **Playwright MCP** ([microsoft](https://github.com/microsoft/playwright-mcp)) | ⚠️ Detectable | ❌ New session | ✅ | Not installed |
| **Sensei bridge** (current) | ✅ Real browser | ✅ | ❌ No JS | **Wired** |
| **Skyvern** | ✅ Vision bypass | ✅ | ✅ | Not installed |

**Decision:** Sensei is wired. Browser MCP / Glance is the upgrade path — same real-browser model, adds JS execution + no read-cap.

---

## 🔑 Immediate Blockers

### 1. Drive File IDs
`~/.master_ai_drive_refs.json` has PLACEHOLDER URLs. Live runs with `--no-skip-drive` will abort.

**Fix (30 seconds):** User opens each Google Doc → copies URL from address bar → pastes here.
Format: `https://docs.google.com/document/d/**{THIS_PART}**/edit`

Three IDs needed:
- AI Query — Job Application Reference
- Elijah Wilkins — Applications Log  
- Resume Detailed

**Workaround until then:** All runs use `--skip-drive` (default ON). Stub rules used. Works fine.

### 2. Indeed v2 Selectors
recipe.py adapter_indeed has TODO markers — selectors for click_apply, fill_form, upload_resume phases not captured yet. Need live Indeed Smart Apply page inspection.

**Fix:** Open an Indeed Smart Apply form in browser → DevTools → capture selectors → paste into recipe.py.

---

## 🪝 Hooks to Build (New Capability)

Claude Code hooks aren't wired yet. These are high-leverage for Dezzy Zero:

```json
// ~/.claude/settings.json additions:
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "BROWSER_SUBMIT",
      "hooks": [{"type": "command", "command": "~/.claude/hooks/pre_submit_gate.sh"}]
    }],
    "PostToolUse": [{
      "matcher": "Bash",
      "hooks": [{"type": "command", "command": "~/.claude/hooks/log_application.sh"}]
    }],
    "Notification": [{
      "hooks": [{"type": "command", "command": "~/scripts/speak.sh \"{{message}}\""}]
    }]
  }
}
```

Hooks to write:
1. `pre_submit_gate.sh` — blocks BROWSER_SUBMIT unless `DEZZY_SUBMIT_APPROVED=1` env var set
2. `log_application.sh` — appends to `~/MD/applications_log_local.md` on apply completion
3. `notify_done.sh` — calls speak.sh when session ends

---

## 🤖 Subagent Upgrade Path

Current: monolithic recipe.py handles everything.  
Upgrade: specialized subagents, orchestrated by skill steps.

| Subagent | Model | Function |
|----------|-------|----------|
| `job-researcher` | Haiku | Search Indeed/ZIP for matching listings |
| `form-analyzer` | Sonnet | Read form DOM, classify fields, map to profile |
| `cover-letter-writer` | Sonnet | Generate tailored cover letter from resume + JD |
| `dedup-checker` | Haiku | Check against applications log, filter duplicates |
| `apply-executor` | Sonnet | Execute BROWSER_FILL chain from form map |
| `session-logger` | Haiku | Write session result to Drive log |

Model routing (from wshobson/agents): Opus=critical decisions, Sonnet=execution, Haiku=fast/cheap ops.

---

## 📋 Indeed Selector Map (From AIHawk + Community)

For recipe.py v2 TODO sections:

```
# Indeed Smart Apply
#indeedApplyButton              — Apply button
input[name="firstName"]         — first name
input[name="lastName"]          — last name  
input[name="email"]             — email
input[name="phone"]             — phone
input[type="file"]              — resume upload
button[type="submit"]           — submit

# Greenhouse
#first_name, #last_name, #email, #phone
input[name="resume"]            — resume upload
#submit_app                     — submit

# Lever
input[name="name"]              — full name
input[name="email"]             — email
input[name="phone"]             — phone

# LinkedIn Easy Apply
.jobs-apply-button              — Easy Apply trigger
input[id*="phoneNumber"]        — phone
```

**Note:** Selectors vary by form version. Always verify live with DevTools before hardcoding.

---

## Anti-Bot Rules (From AIHawk + Camoufox + indeed_bot)

- Never exceed **50 apps/day on Indeed** (200/day LinkedIn = marketing, real limit ~50)
- Random delays 2–8s between actions (browser_bridge WAIT handler already does this)
- Use real browser sessions (sensei does ✅, Browser MCP will ✅)
- Camoufox (Firefox fork, best stealth) as fallback if Indeed blocks
- Vary field-fill order slightly across sessions

---

## Execution Order — Next Actions

**P0 (Unblock live runs):**
1. Get Drive file IDs → update `~/.master_ai_drive_refs.json`
2. Run one live Indeed application: `python3 run_apply_skill.py <real_indeed_url>`
3. Capture Indeed Smart Apply selectors via DevTools → update recipe.py

**P1 (Browser upgrade):**
4. Install Browser MCP or Glance MCP
5. Wire into Claude Code MCP settings
6. Add `BROWSER_JS` directive to browser_bridge.py

**P2 (Hooks):**
7. Write `pre_submit_gate.sh`
8. Write `log_application.sh`
9. Wire into `~/.claude/settings.json`

**P3 (Scale):**
10. Batch queue: 7 URLs/day fed to run_apply_skill.py
11. Subagent split for cover-letter-writer + form-analyzer
12. Skyvern/Camoufox fallback if bot-blocked

---

## Verification

```bash
# Dry run (always safe)
python3 ~/scripts/run_apply_skill.py --dry-run "https://www.indeed.com/viewjob?jk=TEST"

# Live run (bridge must be alive)
python3 ~/scripts/run_apply_skill.py "https://www.indeed.com/viewjob?jk=REAL_JK"

# Resume after operator review
python3 ~/scripts/run_apply_skill.py --resume SESSION_ID

# List sessions
python3 ~/scripts/run_apply_skill.py --list-sessions
```
