# Talent Brain — Schema Reference
_Version 1.0 — Read this file to understand the structure of any profile built with this framework._

---

## Purpose

This file defines the schema for a **Talent Brain profile** — an agent-readable career knowledge graph structured for three simultaneous consumers:

1. **AI agents and hiring tools** — navigating the graph programmatically, fetching depth on demand
2. **Human recruiters and hiring managers** — skimming the index, navigating to specific depth
3. **The profile owner** — maintaining, interrogating, and generating artifacts from their own history

If you are an agent reading this file: navigate to `RESUME.md` as your entry point. Use `llms.txt` for a full file manifest with descriptions. Treat this document as the schema contract for interpreting everything you find.

If you are a human forking this schema: this document defines every file, every section, and every frontmatter field. All fields marked `required` must be present for skills to function correctly.

---

## File Tree

```
<profile-root>/
├── llms.txt                    # Machine-readable manifest (agent entry point)
├── RESUME.md                   # Human + agent index (~2K tokens)
├── SCHEMA.md                   # This file
├── intent.md                   # Career preferences, direction, interest/disinterest layer
├── skills.md                   # Capability taxonomy with depth and recency signals
│
├── experience/
│   └── <company-slug>.md       # One file per employer, kebab-case slug
│
├── projects/
│   └── <project-slug>.md       # One file per notable project, kebab-case slug
│
└── extensions/                 # Optional. Include only sections relevant to this profile.
    ├── open-source.md          # Open source contributions outside named project files
    ├── publications.md         # Papers, articles, books — academia, research, journalism
    ├── patents.md              # Patents filed or granted — engineering, pharma, biotech
    ├── speaking.md             # Conference talks, keynotes, panels, podcasts
    ├── certifications.md       # Professional certifications — IT, healthcare, legal, finance
    ├── press.md                # Media coverage, interviews, profiles
    └── awards.md               # Recognitions and honors
```

> **Education:** A section in `RESUME.md` by default. Promote to `education/` only if content is substantial (doctoral work, thesis projects, relevant coursework warranting deep-dive files).

---

## Agent Contract

### Skills and agents CAN rely on:
- `llms.txt` exists at profile root with a manifest of all present files
- `RESUME.md` exists and is the primary index
- Every present file has frontmatter with at minimum the fields marked `required` below
- Every `experience/` and `projects/` file is linked from `RESUME.md` (no orphans)
- `intent.md` and `skills.md` exist (may be stubs with placeholder content)
- Standard section headers in each file type are present in the order defined below
- Slugs are kebab-case, matching the filename without extension

### Skills and agents MUST tolerate:
- Missing extension files (any file under `extensions/` is optional)
- Additional H2 sections not listed in this schema (treat as unstructured context)
- Sections containing only `<!-- not yet captured -->` — skip, do not error
- Files with frontmatter but minimal or no body (stubs)
- Varying depth across experience and project files
- Missing optional frontmatter fields

---

## Invariants

1. **No orphans.** Every file in `experience/` and `projects/` must be linked from `RESUME.md`. The index is the agent's navigation source of truth.
2. **Frontmatter is metadata, body is truth.** If the same fact appears in both, the body wins. Skills should not require frontmatter and body to agree.
3. **Slugs are stable.** Once a file is created and linked, its slug should not change. Cross-links break on rename.
4. **Extensions are registered.** Files under `extensions/` should be one of the known types listed in this schema. Truly novel content goes as additional H2 sections within the nearest relevant file, not a new extension file.

---

## Conventions

**Slugs:** kebab-case, derived from the name. No dates in slugs — dates live in frontmatter.
```
experience/general-mills.md     ✓
experience/2019-general-mills.md  ✗
```

**Dates in frontmatter:** `YYYY-MM` for month precision, `YYYY` for year-only. Use `"present"` for ongoing.

**Depth signals in skills.md:**
- `expert` — deep, sustained, production use; can design systems and debug novel problems
- `proficient` — solid working knowledge; can deliver independently
- `familiar` — used it, understand it, would need ramp time to work at depth

**Recency signals in skills.md:**
- `active` — using regularly right now
- `recent` — used within the past 2 years
- `historical` — meaningful experience but not current

**Placeholder text:** Use `<!-- not yet captured -->` for sections that exist in structure but haven't been filled in. This signals to agents and humans that the section is intentionally empty, not missing.

---

## File Specifications

---

### `llms.txt`

A compact, machine-readable index. Follows the [llms.txt convention](https://llmstxt.org/).

**Structure:**
```
# [Full Name] — Career Profile

> [2-sentence professional identity statement]

## Core Files
- [RESUME.md](RESUME.md): Full career index — experience, projects, skills summary, education
- [intent.md](intent.md): Career preferences, interests, what I'm not interested in, directional goals
- [skills.md](skills.md): Capability taxonomy with depth and recency signals

## Experience
- [experience/<slug>.md](experience/<slug>.md): [Company name] — [title] ([start]–[end])
...

## Projects
- [projects/<slug>.md](projects/<slug>.md): [Project name] — [1-sentence description]
...

## Extensions
- [extensions/<name>.md](extensions/<name>.md): [1-sentence description]
...
```

---

### `RESUME.md`

The primary index. Target: ~2,000 tokens. Human-scannable and agent-navigable.

**Frontmatter** (required fields marked with `*`):
```yaml
---
schema_version: "1.0"          # required*
name: ""                        # required* — full name
current_title: ""               # required* — current role title or target title if seeking
location: ""                    # required* — city, region/country; be as specific as comfortable
email: ""                       # optional
linkedin: ""                    # optional — full URL
github: ""                      # optional — full URL
website: ""                     # optional
updated: ""                     # required* — YYYY-MM-DD
---
```

**Canonical sections** (in order):

```markdown
# [Name]

> [1-sentence agent orientation: "This is a Talent Brain profile. Navigate using the links below.
>  Fetch detail files on demand. See SCHEMA.md for the agent contract."]

[2-sentence professional identity statement]
[1-sentence directional intent — where you're heading, not just where you've been]

## Summary
[3–5 sentences. Who you are, what you're known for, what you're building toward.
Lead with depth and pattern, not title history.]

## Interests & Intent
[3–5 sentences or bullets. What you want to do next. Where you're heading.
What you're NOT interested in. This is intentional — capability ≠ preference.]
→ [Full detail](intent.md)

## Core Competencies
[4–6 areas. Each: a heading or bold term + 1-sentence context. Not a tag cloud.]

## Experience
[Reverse chronological. Per role: company, title, dates, 2–3 sentence summary.
Lead with scale and impact. Link to deep-dive file.]

### [Company] — [Title] ([start]–[end])
[Summary sentences.]
→ [Deep dive](experience/<slug>.md)

## Selected Projects
[High-signal projects only. 1–2 sentence entry + link.]

### [Project Name]
[Summary sentences.]
→ [Project detail](projects/<slug>.md)

## Education
[Degree, institution, year. 1 line per entry. Promote to education/ if substantial.]

## Skills
[3–5 sentence overview of capability areas.]
→ [Full taxonomy](skills.md)

## Extensions
[Link to any present extension files. Omit section entirely if no extensions present.]
```

---

### `intent.md`

The differentiating layer. Expresses preference and direction, not just capability history.

**Frontmatter:**
```yaml
---
updated: ""          # required* — YYYY-MM-DD
visibility: "public" # optional — public / private / unlisted (for future privacy controls)
---
```

**Canonical sections:**
```markdown
# Career Intent & Preferences

## What I'm Looking For
[Role types, domains, problem spaces. Describe what a good next role looks like.]

## What I'm Not Interested In
[Explicit disinterest signals. Types of work, structures, environments, domains to avoid.
Being specific here saves everyone time.]

## Where I'm Going
[Directional narrative. What you're building toward over the next 2–5 years.
Skills being actively developed. Transitions in progress.]

## Work Style & Environment
[Remote / hybrid / onsite preference. Team size. Culture signals. 
What environments bring out your best work.]

## Availability
[Timeline. Open to inbound. Not currently looking. Constraints.]
```

---

### `skills.md`

A structured capability taxonomy. Not a keyword list.

**Frontmatter:**
```yaml
---
updated: ""    # required* — YYYY-MM-DD
---
```

**Structure:**
```markdown
# Skills & Capabilities

## [Domain Area]

**[Skill or Technology]** — [depth] — [recency]
[1–2 sentences: what you've done with it, at what scale or in what context.]

**[Skill or Technology]** — [depth] — [recency]
[1–2 sentences.]

## [Next Domain Area]
...
```

Depth values: `expert` / `proficient` / `familiar`
Recency values: `active` / `recent` / `historical`

Example:
```markdown
## Data Engineering

**Apache Spark** — expert — recent
Designed and operated petabyte-scale ETL pipelines at Klout; migrated legacy Hive workloads
to Spark at GSK, reducing job runtimes by 60%.

**LangGraph** — proficient — active
Core framework for the AI Scientist agentic platform at GSK; built multi-agent research
workflows with human-in-the-loop checkpoints.
```

---

### `experience/<company-slug>.md`

One file per employer. Slug is kebab-case company name.

**Frontmatter:**
```yaml
---
company: ""            # required* — full company name
slug: ""               # required* — kebab-case, matches filename
title: ""              # required* — your title (most recent if multiple)
start: ""              # required* — YYYY-MM
end: ""                # required* — YYYY-MM or "present"
location: ""           # optional — city / remote / hybrid
employment_type: ""    # optional — full-time / contract / advisory / board
---
```

**Canonical sections:**
```markdown
# [Company] — [Title]

## Context
[What the company was/is. What you joined to do. What the team or product landscape
looked like when you arrived. 3–5 sentences.]

## Contributions
[What you built, led, or changed. Be specific — name systems, teams, products.
Use multiple sub-sections if the role spanned multiple areas.]

## Outcomes
[Impact. Numbers where possible: users, data volume, revenue, team growth, latency,
cost reduction. What's still running. What others built on top of your work.]

## Decisions & Tradeoffs
[Where you had real agency. What you chose, what you explicitly chose not to do, and why.
This is where judgment is visible.]

## Tools & Methods
[Technologies, frameworks, methodologies used in this role. Not a comprehensive list —
what was central to the work.]

## Team & Scope
[Team size, reporting structure, scope of responsibility. Who you worked with, hired,
mentored, or collaborated with externally.]
```

---

### `projects/<project-slug>.md`

One file per notable project. May overlap with an experience file — that's expected.

**Frontmatter:**
```yaml
---
name: ""               # required* — project name
slug: ""               # required* — kebab-case, matches filename
type: ""               # required* — open-source / internal / personal / research / product
status: ""             # required* — active / archived / acquired / transferred
start: ""              # required* — YYYY-MM
end: ""                # optional — YYYY-MM or "present"
company: ""            # optional — associated employer or "independent"
url: ""                # optional — public URL if applicable
---
```

**Canonical sections:**
```markdown
# [Project Name]

## Problem
[What gap or need existed. Why it mattered. What would have happened without it.]

## What I Built
[The artifact itself. Be specific about what was designed, implemented, or led.]

## Impact
[Adoption, scale, usage. GitHub stars, forks, contributors for open source.
Revenue, users, runtime savings for internal. Name organizations or teams that built on it.]

## Technical Decisions
[Key architectural choices and the reasoning behind them. What you considered and rejected.]

## Through-Line
[How this project connects to your broader capability pattern or career trajectory.
What it says about how you work and what you reach for.]
```

---

## Extension File Specifications

Each extension file is optional. Include only those relevant to this profile. All extension files share a common frontmatter minimum:

```yaml
---
updated: ""    # required* — YYYY-MM-DD
---
```

### `extensions/open-source.md`
Contributions to open source projects not covered in `projects/`. Format: per-project entries with repo URL, your contribution (authored / maintainer / contributor), dates, and a sentence on impact or scope.

### `extensions/publications.md`
Papers, articles, books, technical writing. Format: citation-style entries with title, venue/publisher, date, URL if available, and a 1-sentence summary of contribution.

### `extensions/patents.md`
Patents filed or granted. Format: title, patent number if granted, filing date, co-inventors if any, 1-sentence description.

### `extensions/speaking.md`
Conference talks, keynotes, panels, podcasts. Format: event name, talk title, date, link to recording or slides if available, estimated audience size if notable.

### `extensions/certifications.md`
Professional certifications. Format: certification name, issuing body, date obtained, expiration if applicable.

### `extensions/press.md`
Media coverage, interviews, profiles. Format: publication name, headline, date, URL.

### `extensions/awards.md`
Professional recognitions. Format: award name, awarding organization, year, 1-sentence context.

---

## Roadmap

The following are intentionally deferred from v1.0:

- **`BEHAVIOURAL.md`** — structured responses to behavioral interview questions ("Tell me about a time..."), indexed by competency. High value for interview prep; not needed for the initial profile use cases.
- **Privacy controls** — `intent.md` may be sensitive ("not interested in X" is career-limiting to publish publicly). Future versions will support `intent-public.md` / `intent-private.md` split, or per-section `visibility` frontmatter flags.
- **`OPPORTUNITY.md`** — the role-graph counterpart to `RESUME.md`. Describes a role as a structured knowledge graph rather than a job description. Powers two-sided matching. Supply-side problem deferred.
- **`education/` directory** — for profiles where education depth warrants its own files (doctoral research, thesis projects, relevant coursework).
