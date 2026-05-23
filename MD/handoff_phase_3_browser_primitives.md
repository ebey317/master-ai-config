# Handoff — Phase 3: missing browser primitives (parity with Claude in Chrome)

Date: 2026-05-14 evening (after the Drive-fail transcript)
Lane split: Codex owns `content_script.js`. This doc enumerates the
handler shapes needed for spec parity with Anthropic's Claude in
Chrome extension. Claude lane (mine) will land matching
typed_actions/parser/teaching pieces once the content_script
handlers commit.

## Why this exists

Elijah's transcript 2026-05-14 evening (Drive résumé folder
search) showed:

1. Our build emits one BROWSER_* per round and waits for the
   result — slow + noisy. Mitigated post-this-commit by BATCHING
   DISCIPLINE teaching in Modelfile + CLOUD_SYSTEM, but the model
   can only batch with the primitives it has.
2. Drive's accessibility tree lags its UI — BROWSER_READ keeps
   missing visible elements. Mitigated by GOOGLE DRIVE SPECIFICS
   teaching (prefer URL nav) but doesn't generalize to other SPAs.
3. Folder open needs double-click in Drive; we only emit single
   BROWSER_CLICK. Workaround: BROWSER_NAV to the folder URL.

Side-by-side, Anthropic's Claude in Chrome demonstrated the
spec-parity primitive set on the same task (transcript pasted by
Elijah): `browser_batch`, `Take screenshot`, `Find:` (semantic),
`Double-click`, `Wait`, `Extract page text`, `Scroll down`.

This handoff documents the four missing primitives so Codex can
land the content_script.js handlers as a focused commit. Claude
then ships the matching typed_actions + parser + Modelfile +
CLOUD_SYSTEM teaching + side_panel display in a follow-on.

## Primitive 1 — BROWSER_WAIT

Target: a duration string (`"2s"`, `"500ms"`) OR a wait
condition (`"selector:#submitButton"` waits until the selector
matches a live element, `"loadstate:complete"` waits for
document.readyState).

Handler in `content_script.js`:

```js
// Inside executeBrowserAction's switch:
if (kind === "BROWSER_WAIT") {
  const raw = String(action.target || "").trim();
  const durMatch = raw.match(/^(\d+)\s*(ms|s)?$/i);
  if (durMatch) {
    const ms = Number(durMatch[1]) * (durMatch[2]?.toLowerCase() === "s" ? 1000 : 1);
    await new Promise(r => setTimeout(r, Math.min(ms, 10000)));  // cap at 10s
    return { ok: true, waited_ms: ms };
  }
  if (raw.startsWith("selector:")) {
    const sel = raw.slice("selector:".length).trim();
    const deadline = Date.now() + 5000;  // 5s cap
    while (Date.now() < deadline) {
      if (document.querySelector(sel)) return { ok: true, found: sel };
      await new Promise(r => setTimeout(r, 100));
    }
    return { ok: false, error: "wait selector timeout" };
  }
  if (raw === "loadstate:complete" || raw === "complete") {
    if (document.readyState === "complete") return { ok: true };
    return new Promise(resolve => {
      const t = setTimeout(() => resolve({ ok: false, error: "loadstate timeout" }), 8000);
      window.addEventListener("load", () => { clearTimeout(t); resolve({ ok: true }); }, { once: true });
    });
  }
  return { ok: false, error: `unrecognized wait target: ${raw}` };
}
```

Hard caps (10s duration, 5s selector, 8s loadstate) prevent the
extension from hanging on a misguided wait.

## Primitive 2 — BROWSER_SCROLL

Target: a direction (`"down"`, `"up"`, `"top"`, `"bottom"`) OR a
selector (scroll element into view) OR a pixel delta (`"+800"`,
`"-400"`).

```js
if (kind === "BROWSER_SCROLL") {
  const raw = String(action.target || "").trim().toLowerCase();
  if (raw === "down")   { window.scrollBy({ top: window.innerHeight * 0.8, behavior: "smooth" }); }
  else if (raw === "up") { window.scrollBy({ top: -window.innerHeight * 0.8, behavior: "smooth" }); }
  else if (raw === "top") { window.scrollTo({ top: 0, behavior: "smooth" }); }
  else if (raw === "bottom") { window.scrollTo({ top: document.body.scrollHeight, behavior: "smooth" }); }
  else if (/^[+-]?\d+$/.test(raw)) { window.scrollBy({ top: Number(raw), behavior: "smooth" }); }
  else {
    const el = findElement(raw);
    if (!el) return { ok: false, error: "scroll target not found" };
    el.scrollIntoView({ block: "center", behavior: "smooth" });
  }
  await new Promise(r => setTimeout(r, 300));  // let scroll settle
  return { ok: true, scrolled: raw };
}
```

## Primitive 3 — BROWSER_DBLCLICK

Target: same shape as BROWSER_CLICK — CSS selector OR accessible
text via findElement's existing text-content fallback.

```js
if (kind === "BROWSER_DBLCLICK") {
  const el = findElement(action.target);
  if (!el) return { ok: false, error: "target not found" };
  el.scrollIntoView({ block: "center", behavior: "smooth" });
  // dispatch a real dblclick event, not two click()s — some apps
  // (Google Drive) only respond to native dblclick.
  el.dispatchEvent(new MouseEvent("dblclick", { bubbles: true, cancelable: true, view: window }));
  return { ok: true, dblclicked: action.target,
           page_context: pageContext({ includeVisibleText: false, includeInteractiveElements: false }) };
}
```

Drive uses dblclick to open folders; single click selects.

## Primitive 4 — BROWSER_FIND

The most useful and hardest. Target: a semantic descriptor
(`"button: Sign in"`, `"link: Resume folder"`, `"text: 07_Resume-Career"`).
Returns the BEST-MATCH selector for the dispatcher to use in the
next round. Solves the SPA aria-tree-lag problem (Drive
"I can see it but BROWSER_READ doesn't find it").

```js
if (kind === "BROWSER_FIND") {
  const raw = String(action.target || "").trim();
  const m = raw.match(/^(button|link|input|textbox|combobox|radio|checkbox|text|heading)\s*:\s*(.+)$/i);
  const role = m ? m[1].toLowerCase() : "any";
  const needle = (m ? m[2] : raw).trim().toLowerCase();

  // Walk all elements with accessible names matching needle.
  const candidates = [];
  document.querySelectorAll("button, a, input, select, textarea, [role], [aria-label]").forEach(el => {
    if (!isVisible(el)) return;
    const elRole = elementRole(el);
    if (role !== "any" && elRole !== role) return;
    const name = (elementName(el) || el.textContent || "").trim().toLowerCase();
    if (name.includes(needle)) {
      candidates.push({ el, name, selector: safeSelectorFor(el), exact: name === needle });
    }
  });
  // Prefer exact match, then shortest accessible name (least ambiguous).
  candidates.sort((a, b) => (b.exact - a.exact) || (a.name.length - b.name.length));
  if (!candidates.length) return { ok: false, error: `no match for "${needle}" (role=${role})` };
  const best = candidates[0];
  return {
    ok: true,
    found: { selector: best.selector, name: best.name, count: candidates.length },
    page_context: pageContext({ includeVisibleText: false, includeInteractiveElements: false })
  };
}
```

Model usage pattern:
```
BROWSER_FIND: text: 07_Resume-Career
BROWSER_DBLCLICK: <selector from previous round's found.selector>
```

The model gets the selector back in [PREVIOUS ROUND RESULTS] and
uses it deterministically.

## Claude-lane follow-on commits (after Codex's handler commit)

Once `content_script.js` exposes the four handlers above:

1. `typed_actions.py` — add the four kinds to BROWSER_KINDS, plus
   target-shape validators (duration regex, scroll direction enum,
   semantic-find role list).
2. `stt_server.py _api_parse_actions` — regex entries for the four
   directive prefixes (`BROWSER_WAIT:`, `BROWSER_SCROLL:`,
   `BROWSER_DBLCLICK:`, `BROWSER_FIND:`).
3. `Modelfile-master-ai` — add the four primitives to the DIRECTIVES
   block, plus one few-shot showing the BROWSER_FIND → next-round
   BROWSER_DBLCLICK pattern.
4. CLOUD_SYSTEM in `master_ai.py` — same teaching.
5. `side_panel.js` — kind→friendly-label mapping for new kinds in
   the action card header.
6. `test/loop_smoke.html` — add a "wait then scroll then dblclick"
   smoke checklist sister to LOOP_CHECKLIST.

## Coordination signal

When Codex's `content_script.js` commit lands with the four
handlers (each named `SENSEI_HANDLE_BROWSER_<KIND>` or matched in
`executeBrowserAction`'s switch), append a line to the bottom of
THIS file with the commit SHA. Claude polls this file on next
session start; the SHA presence is the green light to ship the
follow-on.

If Codex wants to split the work (e.g., ship WAIT + SCROLL first,
then DBLCLICK + FIND), fine — just call out which kinds are in
each commit in the body so Claude knows which follow-on to
schedule.

## Reference

- Commit `fa6ea87` (Modelfile PLAN-AS-BLOCK CONTRACT) and
  `4246ef8` (CLOUD_SYSTEM equivalent) — sibling teaching for the
  three-mode primitive set we already ship.
- Commit `ca86260` (Auto-mode button promotion) — the UX fix that
  unblocked Elijah's Drive session today.
- Transcript: Anthropic's Claude in Chrome doing the same Drive
  task with `browser_batch`, Find, Wait, Double-click. Pasted in
  Elijah's 2026-05-14 evening turn.
