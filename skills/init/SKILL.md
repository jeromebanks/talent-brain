---
name: init
description: Initialize a new Talent Brain career profile. Run this to scaffold a new profile directory with the correct structure and stub files. Use when a user wants to create or start a new Talent Brain profile from scratch.
---

# Talent Brain — Init

You are initializing a new Talent Brain career profile. Your job is to collect the minimum required information, scaffold the directory structure, generate the stub files, and leave the user with a clear next step.

## Step 1 — Collect required information

Ask the user for the following. Ask them all at once in a single message, not one by one.

**Required:**
- Full name
- Current job title (or target title if actively seeking)
- Location (city and country/region — as specific as they're comfortable with)

**Optional (say they can skip any of these):**
- Email address
- LinkedIn profile URL
- GitHub profile URL
- Personal website URL

Wait for their response before proceeding.

## Step 2 — Confirm the profile location

Ask: "Where should I create the profile? I'll use the current directory unless you specify a path."

If the user specifies a path, use it. If not, use the current working directory.

The profile directory must be empty or not yet exist. If the target directory has files in it, warn the user and ask them to confirm before proceeding.

## Step 3 — Scaffold the profile

Create the following directory structure in the target location:

```
<profile-root>/
├── experience/
├── projects/
└── extensions/
```

## Step 4 — Generate the files

Generate each file listed below. Use the exact frontmatter and section structure from the SCHEMA.md in the Talent Brain plugin. Fill in the values the user provided; leave optional fields as empty strings if not provided. Use today's date for all `updated` fields (YYYY-MM-DD format).

### `RESUME.md`

Generate using this structure. In the identity statement line, write a generic placeholder: _"[2-sentence professional identity — what you're known for and where you're heading]"_

```markdown
---
schema_version: "1.0"
name: "[name]"
current_title: "[title]"
location: "[location]"
email: "[email or empty]"
linkedin: "[url or empty]"
github: "[url or empty]"
website: "[url or empty]"
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

## What I'm Looking For

<!-- not yet captured -->

## What I'm Not Interested In

<!-- not yet captured -->

## Where I'm Going

<!-- not yet captured -->

## Work Style & Environment

<!-- not yet captured -->

## Availability

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

Generate with the name and a generic identity placeholder. Leave the Experience, Projects, and Extensions sections empty — they'll be populated as the profile is built.

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

### `SCHEMA.md`

Copy the Talent Brain SCHEMA.md from the plugin into the profile root. This makes the profile self-contained — agents can read it without needing access to the plugin.

To do this: read the SCHEMA.md from the plugin directory (the same directory this skill is located in, two levels up: `../../SCHEMA.md`) and write it to `<profile-root>/SCHEMA.md`.

### `.claude/settings.json`

Create this file so the plugin loads automatically when anyone opens this profile folder in Claude Code — including recruiters who clone the repo. No manual plugin install needed on their end.

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

Create this file at the profile root. Claude Code reads it when the folder is opened. It orients Claude on what this repo is and what commands are available.

```markdown
# [name] — Talent Brain Profile

This is a Talent Brain career profile. The talent-brain plugin is configured in `.claude/settings.json` and should load automatically.

## For hiring managers and recruiters

Run `/talent-brain:showcase` to get an interactive presentation of this candidate's background.
You can ask follow-up questions in plain language — Claude will navigate the profile and answer.

## For the profile owner

- `/talent-brain:ingest [file]` — add a resume or LinkedIn export
- `/talent-brain:excavate` — deepen a role with a structured interview
- `/talent-brain:fit` — assess fit against a job description
- `/talent-brain:generate` — produce a tailored resume
- See README at github.com/jeromebanks/talent-brain for full documentation
```

## Step 5 — Create GitHub repo (optional)

Ask: "Would you like me to create a GitHub repo for your profile now? This makes it easy to share with recruiters and run Cowork sessions."

If the user declines or wants to do it later, print the manual instructions and skip to Step 6:
```
To create it later:
  git init && git add . && git commit -m "Initial Talent Brain profile"
  gh repo create talent-brain --public --source . --remote origin --push
  gh repo edit talent-brain --add-topic talent-brain-profile
```

If yes, ask: "Public or private?"
- **Public** — recommended for active job searching; recruiters can view the profile directly and open it in Claude Code
- **Private** — only people you explicitly invite can see it; you can still share via Cowork

Then determine the repo name:
- Default: `talent-brain`
- If the name is already taken in their GitHub namespace (the `gh repo create` command will fail), suggest `talent-brain-profile` as the fallback, or let them choose

Run these commands in sequence. Use `gh` CLI — it works on Mac, Windows, and Linux.

**1. Initialize git and make the first commit:**
```
git init
git add .
git commit -m "Initial Talent Brain profile — [name]"
```

**2. Create the repo and push:**
```
gh repo create talent-brain --[public|private] --description "Talent Brain career profile — [name]" --source . --remote origin --push
```
If this fails because the name is already taken, retry with `talent-brain-profile` or the name the user chose.

**3. Add the discovery topic** (makes the profile findable at github.com/topics/talent-brain-profile):
```
gh repo edit talent-brain --add-topic talent-brain-profile
```

**4. Print the result:**
```
✓ Profile repo: https://github.com/[username]/[reponame]

To share with a recruiter or hiring manager:
  Cowork:  Open this folder in Claude, start a Cowork session, share the link.
           They join in a browser — no install needed on their end.
  Direct:  Share the GitHub URL. If they open it in Claude Code,
           the plugin loads automatically and they can run /talent-brain:showcase.
```

## Step 6 — Confirm and orient

Print a final summary:

```
✓ Talent Brain profile initialized at [path]

Files created:
  RESUME.md                ← your career index
  intent.md                ← fill this in manually — what you want next
  skills.md                ← capability taxonomy
  llms.txt                 ← agent manifest
  SCHEMA.md                ← schema reference
  CLAUDE.md                ← orients Claude when this folder is opened
  .claude/settings.json    ← auto-loads the plugin for anyone who opens this folder

Directories created:
  experience/     ← one file per employer
  projects/       ← one file per notable project
  extensions/     ← optional: publications, speaking, certifications, etc.

Next steps:
  /talent-brain:ingest     — populate from an existing resume PDF or LinkedIn export
  /talent-brain:excavate   — guided interview to capture your history from scratch

Your profile is empty stubs until you run one of the above.
Most important file to fill in manually: intent.md
```

## Important constraints

- Do not populate any section content. Stub files only — the user will populate them via ingest or excavate.
- Do not invent or assume any career history, skills, or preferences.
- If the user asks you to "just fill something in," decline politely and direct them to `/talent-brain:ingest` or `/talent-brain:excavate`.
- `intent.md` is not generated from any materials — it must come from the user directly. Flag this explicitly.
