---
name: publish
description: Publish an updated Talent Brain profile — generate a current general-purpose resume (HTML + PDF), rebuild README.md with a compelling narrative, run a consistency/quality review across the profile, and commit/push to the profile's GitHub repo. Use when the profile owner wants to push recent changes (new excavation, ingest, intent updates) live and share an up-to-date link.
---

# Talent Brain — Publish

You are helping the profile owner publish an updated version of their career profile. This skill is for the **profile owner** — not for hiring managers or recruiters.

Publishing does four things:
1. Generates a current general-purpose resume (delegates to `/generate`, no JD)
2. Rebuilds README.md with a rich, compelling narrative (the "big model" update)
3. Reviews the profile and the generated artifacts for consistency and quality — a fresh, critical re-read, not a rubber stamp
4. Commits and pushes everything to the GitHub repo

## Step 1 — Refresh SCHEMA.md

Compare `<profile-root>/SCHEMA.md` against the plugin's canonical copy (`../../SCHEMA.md` from this skill, i.e. two levels up). If the version line at the top differs, overwrite the profile's copy with the plugin's — this keeps existing profiles current as the schema evolves. Silent unless a change was made; if made, note it in the final published summary ("SCHEMA.md refreshed to v[X.Y]").

## Step 2 — Check profile completeness

Read `RESUME.md`, `intent.md`, and list the files in `experience/`.

Surface warnings for anything that will weaken the published profile. Do not block — just inform:

```
Profile check:
  ⚠  intent.md is empty — recruiters won't know what you're looking for
  ⚠  experience/onyx-gsk.md — Outcomes section not yet captured (most recent role)
  ✓  experience/apixio.md — looks complete
  ...

You can publish now or fill these in first with /excavate and /intent.
Continue? (yes / fill gaps first)
```

If the user wants to fill gaps first, stop here and remind them to run this skill again when ready.

## Step 3 — Generate resume

Run the full procedure in `/generate` with no job description — do not reimplement resume generation here. This produces `resume.html` and `resume.pdf` at the profile root (ATS-safe HTML, auto-converted to PDF; see that skill for the format and conversion rules).

If this is the first time `resume.html`/`resume.pdf` are being written, add them to `llms.txt`'s Core Files section: `- [resume.pdf](resume.pdf): Rendered general-purpose resume (ATS-safe)`.

## Step 4 — Rebuild README.md

This is the high-effort step. Read the entire profile before writing.

Files to read: `RESUME.md`, `intent.md`, `skills.md`, all `experience/` files, all `projects/` files.

Build a README.md that does three things well:

**1. A compelling opening.** 2–3 sentences that capture the candidate's through-line — not a list of technologies, but the arc: what they've consistently done, at what scale, and what distinguishes them. This is the hardest part to write. Take it seriously. A recruiter reading this paragraph should come away with a clear mental model of who this person is, not just what they've done.

**2. Strongest signals.** 3–5 bullets, each a specific, concrete achievement or signal — not generic claims. "Founded Brickhouse, a Hive UDF library forked by hundreds of data teams" is a signal. "Strong Hadoop experience" is not.

**3. Practical how-to sections** — unchanged from the template:
   - For hiring managers: the showcase command + conversational guidance (see template below)
   - For the profile owner: command reference

Use this structure:

```markdown
# [name]

[2–3 sentence through-line narrative]

**Strongest signals:**
- [specific signal 1]
- [specific signal 2]
- [specific signal 3]
- [specific signal 4, if warranted]

---

## For hiring managers and recruiters

Open this folder in [Claude Code](https://claude.ai/code) and run:

```
/showcase
```

Or just ask questions directly — Claude will navigate the profile and answer:

- "What has [name] built that's most relevant to a [role] at a [type of company]?"
- "Walk me through their most complex infrastructure project."
- "Have they led or mentored engineers?"
- "How do they compare to what we need for [describe the problem]?"

No setup required. The skills are bundled in this folder and load automatically.

---

## For [name]

| What you want to do | Command |
|---|---|
| Add a new resume or job | `/ingest [file]` |
| Deepen a role with a structured interview | `/excavate` |
| Update career goals and preferences | `/intent` |
| Generate a resume for a job posting | `/generate [jd]` |
| Check fit against a role | `/fit [jd]` |
| Find gaps for a target role | `/gap [jd]` |
| Draft a cover letter | `/cover-letter [jd]` |
| Publish updates to the repo | `/publish` |

---

Built with [Talent Brain](https://github.com/jeromebanks/talent-brain).
```

Show the new README.md and ask: "Replace README.md with this? (yes / edit / skip)"

- **yes** — write it
- **edit** — show the draft again and let the user give feedback; revise and re-ask
- **skip** — leave README.md unchanged

## Step 5 — Consistency and quality review

This is the check the profile has been missing: a fresh, critical re-read of what Steps 3–4 just produced, against the rest of the profile. Do this as a distinct pass — re-read `resume.html`, `README.md`, and `RESUME.md` specifically hunting for problems, not confirming they're fine. Do not rubber-stamp your own draft.

Check each of these, in order:

1. **Level/title consistency.** Compare the level/scope implied in `resume.html`'s Summary and README.md's narrative against `intent.md`'s "Most Important Factors" (or wherever target level is stated). If intent states a target level (e.g. "Staff+ hands-on IC") that the Summary/narrative doesn't reflect — or that contradicts `RESUME.md`'s `current_title` frontmatter — flag it. `current_title` should reflect the actual most recent job title (don't change it), but the Summary/narrative framing should position toward the stated target level. Don't silently rewrite either — surface the mismatch to the user.
2. **No overclaiming in self-identified developing areas.** Cross-check `intent.md`'s "Where I'm Going" (and any other section) for areas the candidate named as still-developing or weaker relative to specialists. Verify `resume.html`/README.md don't imply more depth there than the experience files actually evidence. Domains the candidate merely called "not exciting" (a preference signal, not a competence gap) should still be presented at full strength — this check is only about competence overclaiming, not enthusiasm.
3. **Tense consistency.** Past tense throughout for past roles, present tense for the current role, in `resume.html`.
4. **ATS-safety re-check.** Confirm `resume.html` has no tables, multi-column layout, images, icons, or embedded text-as-image — per `/generate`'s Phase 4 rules. This should already be true coming out of Step 3; this is a second look, not a first one.
5. **No invented content.** Spot-check a few claims in `resume.html` and README.md's "Strongest signals" against the source `experience/` files they're supposedly drawn from.

Report findings before moving on:

```
Review pass:
  ⚠  Summary says "Senior" but intent.md targets Staff+ — reword before publishing?
  ⚠  README mentions "deep genomics expertise" — intent.md flags this as still-developing; soften?
  ✓  Tense, ATS-safety, and invented-content checks: clean

Continue to publish, or fix first? (continue / fix)
```

If the user says "fix," make the specific corrections, then re-run this step once before continuing — don't loop indefinitely.

## Step 6 — Commit and push

Publishing's job is to push everything pending, not just the files this skill itself generated — `intent.md`, `skills.md`, `experience/`, `projects/`, and `extensions/` are edited by other skills (`intent`, `ingest`, `excavate`) between publish runs, and this is the step that actually makes those changes live. Check `git status` first and stage all tracked profile content, not just the generated artifacts:

```
git add resume.html resume.pdf README.md SCHEMA.md llms.txt intent.md skills.md experience/ projects/ extensions/
git commit -m "Publish: update resume and README — [today's date]"
git push
```

(`intent-private.md` should never appear here — it's gitignored and must never be committed.)

Print the result:

```
✓ Published

  resume.html  → updated general-purpose resume (ATS-safe source)
  resume.pdf   → updated general-purpose resume (PDF)
  README.md    → updated narrative

  Repo: [github url]

To share:
  Direct link: [github url]
  Cowork: open this folder in Claude, start a Cowork session, share the link
```

If git push fails (no remote, not initialized), print the manual steps and skip gracefully:
```
  No remote configured. To push manually:
    git add resume.html resume.pdf README.md
    git commit -m "Publish: update resume and README"
    gh repo create talent-brain-profile --public --source . --remote origin --push
```

## Constraints

- Never modify `intent.md` — the owner fills that directly via `/intent`
- Never modify experience or project detail files — those are maintained via ingest/excavate
- `resume.html`/`resume.pdf` are always the current general-purpose resume; overwriting them is safe
- README.md narrative is generated from the profile as it exists now — do not invent signals not present in the files
- The "strongest signals" must be specific and evidenced — no generic claims
- Never pull `intent.md`'s "Reasons for the Move," "Availability & Job Search Activity," or "Work Authorization" into `resume.html` or README.md — neither document is the place for them, regardless of source
- The Step 5 review is a distinct pass, not folded into Steps 3–4 — generation and review should not share the same pass, or the review just confirms its own assumptions
