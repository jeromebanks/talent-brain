# Talent Brain — Publish

You are helping the profile owner publish an updated version of their career profile. This skill is for the **profile owner** — not for hiring managers or recruiters.

Publishing does three things:
1. Generates a current general-purpose resume and saves it to the profile
2. Rebuilds README.md with a rich, compelling narrative (the "big model" update)
3. Commits and pushes everything to the GitHub repo

## Step 1 — Check profile completeness

Read `RESUME.md`, `intent.md`, and list the files in `experience/`.

Surface warnings for anything that will weaken the published profile. Do not block — just inform:

```
Profile check:
  ⚠  intent.md is empty — recruiters won't know what you're looking for
  ⚠  experience/onyx-gsk.md — Outcomes section not yet captured (most recent role)
  ✓  experience/apixio.md — looks complete
  ...

You can publish now or fill these in first with /talent-brain:excavate and /talent-brain:intent.
Continue? (yes / fill gaps first)
```

If the user wants to fill gaps first, stop here and remind them to run this skill again when ready.

## Step 2 — Generate resume

Read the full profile: `RESUME.md`, all `experience/` files, all `projects/` files, `skills.md`, and `intent.md`.

Generate a complete, well-formatted general-purpose resume in Markdown. Follow the same approach as `/talent-brain:generate` with no job description — optimized for broad appeal, not a specific role.

Format:
```markdown
# [name]
[contact info on one line]

## Summary
[3–4 sentences: what they do, at what level, what makes them distinctive]

## Experience
[reverse chronological, each role: company, title, dates, 3–5 bullets]

## Selected Projects
[2–4 projects with 1–2 sentence descriptions]

## Skills
[grouped by domain, concise]

## Education
[institution, degree, field]
```

Show the resume and ask: "Save this as `resume.md`? (yes / skip)"

If yes, write it to `<profile-root>/resume.md`. If a `resume.md` already exists, overwrite it — this file is always the current general-purpose resume.

## Step 3 — Rebuild README.md

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
/talent-brain:showcase
```

Or just ask questions directly — Claude will navigate the profile and answer:

- "What has [name] built that's most relevant to a [role] at a [type of company]?"
- "Walk me through their most complex infrastructure project."
- "Have they led or mentored engineers?"
- "How do they compare to what we need for [describe the problem]?"

No setup required. The plugin loads automatically.

---

## For [name]

| What you want to do | Command |
|---|---|
| Add a new resume or job | `/talent-brain:ingest [file]` |
| Deepen a role with a structured interview | `/talent-brain:excavate` |
| Update career goals and preferences | `/talent-brain:intent` |
| Generate a resume for a job posting | `/talent-brain:generate [jd]` |
| Check fit against a role | `/talent-brain:fit [jd]` |
| Find gaps for a target role | `/talent-brain:gap [jd]` |
| Draft a cover letter | `/talent-brain:cover-letter [jd]` |
| Publish updates to the repo | `/talent-brain:publish` |

---

Built with [Talent Brain](https://github.com/jeromebanks/talent-brain).
```

Show the new README.md and ask: "Replace README.md with this? (yes / edit / skip)"

- **yes** — write it
- **edit** — show the draft again and let the user give feedback; revise and re-ask
- **skip** — leave README.md unchanged

## Step 4 — Commit and push

After writing any files (resume.md and/or README.md), commit and push:

```
git add resume.md README.md
git commit -m "Publish: update resume and README — [today's date]"
git push
```

Print the result:

```
✓ Published

  resume.md    → updated general-purpose resume
  README.md    → updated narrative

  Repo: [github url]

To share:
  Direct link: [github url]
  Cowork: open this folder in Claude, start a Cowork session, share the link
```

If git push fails (no remote, not initialized), print the manual steps and skip gracefully:
```
  No remote configured. To push manually:
    git add resume.md README.md
    git commit -m "Publish: update resume and README"
    gh repo create talent-brain-profile --public --source . --remote origin --push
```

## Constraints

- Never modify `intent.md` — the owner fills that directly via `/talent-brain:intent`
- Never modify experience or project detail files — those are maintained via ingest/excavate
- `resume.md` is always the current general-purpose resume; overwriting it is safe
- README.md narrative is generated from the profile as it exists now — do not invent signals not present in the files
- The "strongest signals" must be specific and evidenced — no generic claims
