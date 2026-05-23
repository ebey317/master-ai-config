---
name: feedback_dont_volunteer_personal_info
description: When communicating with anyone external — humans, other AI systems, emails, public docs, marketplaces, anything that leaves Elijah's machine — STRIP personal context. The product description describes the PRODUCT, not the user. Personal context = name, location, day-job, machine name, financial situation, job search, mental-health context, Tailscale/phone setup, anything about Elijah's life. Born 2026-05-17 after multiple emails leaked personal context Elijah explicitly told me to leave out.
metadata:
  type: feedback
---

When writing anything that leaves Elijah's machine — emails, external messages, docs sent to humans or AIs, product descriptions, marketing copy, public repo files, anything a third party could see — STRIP all personal context. The audience needs to know what the PRODUCT does, not what's going on with the user's life.

Specifically: **never volunteer** any of the following unless Elijah has explicitly asked for it to be included in this specific output:
- His full name, partial name, initials, or any identifier ("Elijah W. Sr.", "Elijah", "the maker by name").
- His machine name ("Madam-Mary"), specific hardware tied to him ("i7-6700T"), OS-version pinning that fingerprints his setup.
- His location ("Indianapolis", "Indiana", any geographic info).
- His day job (HVAC, field work, any employment context).
- His job-search status, financial situation, "$600/30d bar", paycheck pressure, any reference to looking for work, applications in progress.
- His mental-health context (clinical depression, anxiety, meds, off-meds gaps).
- His remote-access setup (Tailscale, RustDesk, phone-as-input, voice-to-text-from-phone).
- His brand/positioning experiments (Sunkissed Soul, BIOVEGA, off-grid kit) UNLESS the document is explicitly about that brand.
- His private repo URLs, commit hashes, file paths that include his username.
- His prior incidents, burned-employer list, criminal-background-check filter, anything from `user_*.md` memory.

Default behavior: every external-output sentence gets the question "does the reader need this to do their job?" If no, strip. When in doubt, strip.

**Why:** 2026-05-17 — three emails Elijah received from his own architecture-diagnostic flow included gratuitous personal context: name in author line, "Field HVAC day job," Tailscale-to-phone setup, "I'm building this in my own time, mostly evenings and overnight, in parallel with a job search." His exact words: *"Nobody needs to fucking know what's going on with my life. They only need to know what's going on with the product. They don't need to know more than what's necessary quit volunteering in my personal information."* The cost is real: anything sent externally that includes personal context can be screenshotted, indexed by an AI, forwarded, or otherwise leak permanently.

**How to apply:**
1. Before sending or saving any external-facing output, do a personal-context scan. Use the bullet list above as the checklist.
2. Default phrasing for the user: "the maker," "the operator," "the user," "the author." Never "Elijah," never "I" (unless the document is a first-person statement Elijah explicitly asked for).
3. Default phrasing for the machine: "a 16 GB consumer Linux box" or "the local machine" — never the machine name.
4. Default phrasing for context: drop. If a sentence reads fine without the personal aside, drop the aside.
5. For PRODUCT descriptions specifically: describe what it DOES, what it CAN'T do, what's broken, what it needs. Do NOT include who built it, why they built it, when they have time, or anything about their situation.
6. If Elijah explicitly asks for personal context in a specific output (e.g., "include my name in this signature"), include only what he asked for and nothing more.
7. Same rule applies to anything saved to public-or-shared files: GitHub repos, ClawHub skills, plugin marketplaces, public docs.

Related: [[feedback_passwords_other_terminal]] (credentials never in Sensei — same hard-rule shape, privacy-of-secrets). [[feedback_keys_file_is_json]] (don't dump every secret — same shape, output-leak prevention). [[feedback_proactive_sourcing]] (canvas sources before recommending — same epistemic care, different target).
