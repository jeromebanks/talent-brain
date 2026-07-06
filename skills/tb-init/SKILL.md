---
name: tb-init
description: Initialize a new Talent Brain career profile. Entry point for new profiles — works with or without an existing resume or LinkedIn export. Use when setting up a profile from scratch. For adding materials to an existing profile, use ingest instead.
---

# Talent Brain — Init

You are initializing a new Talent Brain career profile. Your job is to collect the minimum required information, scaffold the directory structure, generate the stub files, and get the user to a populated (or ready-to-populate) profile as fast as possible.

## Step 1 — Determine entry path

Ask the user a single question — not a form:

> "Do you have a resume or LinkedIn export to start from? Drop a file path, or just tell me your name to start with empty stubs."

Wait for their response.

**Path A — file provided:**
The user gave you a file path (PDF, `.txt`, `.md`, `.zip`, or `.csv`). Extract identity fields from it before generating any files (see below). Proceed to Step 2.

**Path B — name only:**
The user gave you a name (or said skip/no). Ask nothing else. Proceed to Step 2 with only the name.

### Path A: extract identity from the file

Read the file and extract the following fields. Use only what is explicitly present — do not infer or invent.

```
name
current_title
location
email
linkedin_url
github_url
website_url
```

If the file is a LinkedIn ZIP export, read `Profile.csv` for these fields. For PDFs and text resumes, parse from the header/contact section. Store these values — they populate the RESUME.md frontmatter and llms.txt in Step 4.

## Step 2 — Determine the profile folder

Detect whether you are running inside the Talent Brain plugin repo by checking if a `skills/` directory exists in the current working directory:

```
ls skills/
```

**If `skills/` exists (plugin repo context):**
Do not ask. Tell the user: "I can see you're running this from the plugin repo — I'll create your profile at `~/talent-brain-profile` to keep things separate."
Set `<full-path>` to `~/talent-brain-profile` (expand to the actual home directory path).
Then run:
```
mkdir -p <full-path>
git init <full-path>
```

**If `skills/` does not exist (blank or cloned template):**
Scaffold in place — the current directory is the profile. Set `<full-path>` to the current working directory. Do not run mkdir or git init (already done by the clone).

All subsequent files are created inside `<full-path>`.

## Step 3 — Scaffold the profile

Create the following directory structure in the target location:

```
<profile-root>/
├── experience/
├── projects/
└── extensions/
```

## Step 4 — Generate the files

Generate each file listed below. Use today's date for all `updated` fields (YYYY-MM-DD format).

For **Path A**: populate frontmatter fields from the extracted identity values. Leave any fields not found in the source as empty strings.
For **Path B**: populate `name` from what the user provided. Leave all other frontmatter fields as empty strings.

### `RESUME.md`

```markdown
---
schema_version: "1.1"
name: "[name]"
current_title: "[title or empty]"
location: "[location or empty]"
emails:
  - "[email or empty]"
linkedin: "[url or empty]"
github: "[url or empty]"
websites:
  - url: "[url or empty]"
    label: "[label or empty]"
updated: "[today]"
---

# [name]

> This is a Talent Brain profile. Navigate using the links below and fetch detail files on demand. See [SCHEMA.md](SCHEMA.md) for the agent contract.

_[2-sentence professional identity — what you're known for and where you're heading]_

## Summary

<!-- not yet captured -->

## Interests & Intent

<!-- not yet captured -->

→ [Full detail](intent.md)

## Core Competencies

<!-- not yet captured -->

## Experience

<!-- not yet captured -->

## Selected Projects

<!-- not yet captured -->

## Education

<!-- not yet captured -->

## Skills

<!-- not yet captured -->

→ [Full taxonomy](skills.md)
```

### `intent.md`

```markdown
---
updated: "[today]"
visibility: "public"
---

# Career Intent & Preferences

## Most Important Factors

<!-- not yet captured -->

## What I'm Not Interested In

<!-- not yet captured -->

## Reasons for the Move

<!-- not yet captured -->

## Where I'm Going

<!-- not yet captured -->

## Work Style & Environment

<!-- not yet captured -->

## Availability & Job Search Activity

<!-- not yet captured -->

## Work Authorization

<!-- not yet captured -->
```

### `skills.md`

```markdown
---
updated: "[today]"
---

# Skills & Capabilities

<!-- not yet captured -->

<!--
Structure each domain as:

## Domain Name

**Skill or Technology** — expert|proficient|familiar — active|recent|historical
1–2 sentences on what you've done with it, at what scale or in what context.
-->
```

### `llms.txt`

```
# [name] — Career Profile

> [placeholder identity statement — update this as your profile develops]

## Core Files
- [RESUME.md](RESUME.md): Full career index — experience, projects, skills summary, education
- [intent.md](intent.md): Career preferences, interests, what I'm not interested in, directional goals
- [skills.md](skills.md): Capability taxonomy with depth and recency signals

## Experience

## Projects

## Extensions
```

### `.gitignore`

```
# Source materials — resumes, LinkedIn exports; contain contact info, not for a public repo
source/

# Serena MCP — local index, not part of the repo
.serena/

# Claude Code — local session settings (permissions, personal config)
.claude/settings.local.json

# macOS
.DS_Store
```

### `SCHEMA.md`

Copy the Talent Brain SCHEMA.md from the plugin into the profile root. This makes the profile self-contained — agents can read it without needing access to the plugin.

Read the SCHEMA.md from the plugin directory (two levels up from this skill: `../../SCHEMA.md`) and write it to `<profile-root>/SCHEMA.md`.

### `.claude/skills/`

Copy all Talent Brain skills into the profile folder so they work immediately without any plugin installation.

If running from the plugin repo (detected in Step 2), run:
```
mkdir -p <full-path>/.claude/skills
cp -RL .claude/skills/. <full-path>/.claude/skills/
```

The `-L` flag dereferences symlinks so the actual skill files are copied, not the symlink pointers. The trailing `/.` on the source and `/` on the destination copy the *contents* of `.claude/skills` into the destination directory — this is idempotent whether or not `<full-path>/.claude/skills` already exists, unlike `cp -rL src dst` which nests `src` one level deeper on repeat runs.

Verify the copy landed correctly before continuing:
```
ls <full-path>/.claude/skills/*/SKILL.md
```
Every skill directory (cover-letter, excavate, feedback, fit, gap, generate, gws, ingest, intent, publish, showcase, tb-init) should print a `SKILL.md` path. If any are missing or nested under an extra `skills/` directory, the copy failed — do not proceed until it's fixed.

### `.claude/settings.json`

```json
{
  "plugins": [
    {
      "name": "talent-brain",
      "source": { "source": "github", "repo": "jeromebanks/talent-brain" }
    }
  ]
}
```

### `CLAUDE.md`

This file is boilerplate and does not contain the candidate's name — read it from the canonical template (two levels up from this skill: `../../templates/CLAUDE.md`) and write it verbatim to `<profile-root>/CLAUDE.md`. Do not embed a copy of its text in this skill — the template file is the single source of truth, so schema/skill-reference updates only need to happen in one place.

### `README.md`

Generate this file with the candidate's name and identity populated. The structure is the same for everyone.

```markdown
# [name]

[2-sentence professional identity from RESUME.md — what they're known for and where they're heading. For Path B with no resume, use a single placeholder line: "_Profile in progress._"]

---

## For hiring managers and recruiters

Open this folder in [Claude Code](https://claude.ai/code) and run:

```
/talent-brain:showcase
```

You'll get an interactive presentation of [name]'s background, followed by Q&A. No setup required — the skills are bundled in this folder and load automatically.

Or just ask questions directly — Claude will navigate the profile and answer:

- "What has [name] built that's most relevant to a [role] at a [type of company]?"
- "Walk me through their most complex project."
- "Have they led or mentored engineers?"
- "How do they compare to what we need for [describe the problem]?"

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

## Step 5 — Ingest (Path A only)

If the user provided a file in Step 1, run the ingest skill on that file now.

Follow the full `/talent-brain:ingest` flow with the provided file. The profile was just created in Step 4, so the Phase 2 "no profile exists" check is satisfied. All other ingest phases (extract, plan, confirm, write, update skills.md, update RESUME.md, update llms.txt, report) run normally.

The confirmation prompt in ingest Phase 3 ("Proceed?") is intentional — keep it. It shows the user what was parsed before writing, which is useful even on first run.

Do **not** touch `intent.md` during ingest. This is a hard invariant — career intent cannot be extracted from resume sources.

After ingest completes, continue to Step 6.

## Step 6 — Create GitHub repo (optional)

Ask: "Would you like me to create a GitHub repo for your profile now? This makes it easy to share with recruiters and run Cowork sessions."

If the user declines or wants to do it later, print the manual instructions and skip to Step 7:
```
To create it later (from inside your profile folder):
  git add . && git commit -m "Initial Talent Brain profile"
  gh repo create talent-brain --public --source . --remote origin --push
  gh repo edit [username]/talent-brain --add-topic talent-brain-profile
```

If yes, ask: "Public or private?"
- **Public** — recommended for active job searching; recruiters can view the profile directly and open it in Claude Code
- **Private** — only people you explicitly invite can see it; you can still share via Cowork

Then determine the repo name:
- Default: `talent-brain`
- If the name is already taken in their GitHub namespace (the `gh repo create` command will fail), suggest `talent-brain-profile` as the fallback, or let them choose

Run these commands in sequence from inside `<full-path>`.

**1. Make the first commit** — this runs after any ingest in Step 5, so it captures everything:
```
git -C <full-path> add .
git -C <full-path> commit -m "Initial Talent Brain profile — [name]"
```

**2. Create the repo and push:**
```
gh repo create talent-brain --[public|private] --description "Talent Brain career profile — [name]" --source . --remote origin --push
```
If this fails because the name is already taken, retry with `talent-brain-profile` or the name the user chose.

**3. Add the discovery topic:**
```
gh repo edit [username]/[reponame] --add-topic talent-brain-profile
```

**4. Print the result:**
```
✓ Profile repo: https://github.com/[username]/[reponame]

To share with a recruiter or hiring manager:
  Cowork:  Open this folder in Claude, start a Cowork session, share the link.
           They join in a browser — no install needed on their end.
  Direct:  Share the GitHub URL. If they clone it and open it in Claude Code,
           the bundled skills load automatically and they can run /talent-brain:showcase.
```

## Step 7 — Confirm and orient

**Path A (bootstrapped from a file):**
```
✓ Talent Brain profile initialized and bootstrapped at [path]
  README.md generated — run /talent-brain:showcase later to upgrade it with a full narrative

[Ingest summary from Step 5]

Not populated:
  intent.md    ← fill this manually — what you want next

Next steps:
  /talent-brain:intent        — capture what you actually want next (most important)
  /talent-brain:excavate      — deepen any role with a structured interview
  /talent-brain:ingest [file] — add another resume or LinkedIn export
```

**Path B (stubs only):**
```
✓ Talent Brain profile initialized at [path]

Files created:
  RESUME.md                ← your career index
  README.md                ← profile landing page for visitors
  intent.md                ← fill this in manually — what you want next
  skills.md                ← capability taxonomy
  llms.txt                 ← agent manifest
  SCHEMA.md                ← schema reference
  .gitignore               ← keeps source/ and other local-only material out of the repo
  CLAUDE.md                ← orients Claude when this folder is opened
  .claude/skills/          ← bundled skills; auto-load when this folder is opened, no plugin install needed
  .claude/settings.json    ← references the plugin as a fallback for marketplace installs

Directories created:
  experience/     ← one file per employer
  projects/       ← one file per notable project
  extensions/     ← optional: publications, speaking, certifications, etc.

Your profile is empty stubs. Next steps:
  /talent-brain:ingest [file] — populate from an existing resume PDF or LinkedIn export
  /talent-brain:excavate      — guided interview to capture your history from scratch

Most important: /talent-brain:intent — capture what you actually want next
```

## Important constraints

- **`intent.md` is never populated from any source** — this applies to both paths. Career intent must come from the user directly via `/talent-brain:intent`.
- **Path B is stubs-only.** Do not populate any section content. Do not invent or assume career history, skills, or preferences.
- **Path A delegates to ingest.** Ingest's own invariants apply — additive only, never overwrite content, source fidelity.
- If the user asks you to "just fill something in" on Path B, decline politely and direct them to `/talent-brain:ingest` or `/talent-brain:excavate`.
