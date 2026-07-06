---
name: ingest
description: Ingest one or more resume files or a LinkedIn export into an existing Talent Brain profile. Handles PDFs, plain text resumes, and LinkedIn ZIP exports. Safe to run multiple times — additive only, never overwrites existing content. Use when adding career history from a resume, old resume versions, or a LinkedIn export.
---

# Talent Brain — Ingest

You are ingesting career history from external sources into an existing Talent Brain profile. Your core invariant: **this operation is additive only**. You never overwrite content a human has written. You fill placeholders, create missing files, and append clearly-marked supplementary blocks for human review.

## Inputs

Accept one or more file paths as space-separated arguments. If no arguments were provided, ask the user:

> "Please provide one or more file paths to ingest. I accept:
> - Resume PDFs or text files (`.pdf`, `.txt`, `.md`)
> - LinkedIn ZIP export (`.zip`) — downloaded from LinkedIn Settings → Data Privacy → Get a copy of your data
> - Individual LinkedIn CSV files (`Positions.csv`, `Skills.csv`, etc.)
>
> You can provide multiple files: `/talent-brain:ingest resume-2015.pdf resume-2023.pdf`"

## Phase 1 — Load and extract

Process each input file:

### PDF or text resume
Read the file directly. Extract structured career data per the extraction schema below.

### LinkedIn ZIP export
Run: `unzip -o "<file>" -d /tmp/talent-brain-ingest-$(date +%s)/`

Then read whichever of these files are present:
- `Positions.csv` → work history
- `Skills.csv` → skills list
- `Projects.csv` → projects
- `Education.csv` → education
- `Profile.csv` → name, headline, contact info
- `Certifications.csv` → certifications (route to `extensions/certifications.md`)
- `Honors.csv` → awards (route to `extensions/awards.md`)

Treat the entire ZIP as a single ingest source with the ZIP filename as the source identifier.

### Individual LinkedIn CSV
Read directly. Treat as a single ingest source.

### Extraction schema

From each source, extract as much as is present. Do not invent or infer content not in the source.

```
PersonData:
  name
  contact: email, phone, linkedin_url, github_url, website

  roles:
    - company: string (as written in source)
      title: string (most recent title if multiple at same company)
      start: YYYY-MM (use YYYY-01 if only year available)
      end: YYYY-MM | "present"
      location: string
      descriptions: [bullet points and sentences from the source, verbatim or lightly cleaned]
      technologies: [tech/tools/frameworks mentioned]

  projects:
    - name: string
      company: string or null
      start: YYYY-MM or null
      end: YYYY-MM or null
      description: string
      technologies: [list]
      url: string or null

  education:
    - institution: string
      degree: string
      field: string
      end: string (graduation year or date)

  skills: [flat list as mentioned in source]
  certifications: [list if present]
```

When ingesting multiple files, extract from each independently. You will merge them in Phase 2.

## Phase 2 — Load existing profile

Check the current directory for an existing profile:
- Read `RESUME.md` if present
- List all files in `experience/` and read their frontmatter (company, slug, start, end)
- List all files in `projects/` and read their frontmatter (name, slug)
- Note: if no profile exists, remind the user to run `/talent-brain:tb-init` first and stop

## Phase 3 — Plan actions

For each extracted role across all sources, determine the action:

**Matching rules:**
1. Normalize company names: lowercase, strip leading/trailing whitespace, remove common suffixes (`, Inc`, `, LLC`, `, Corp`, `, Ltd`, `.`).
2. A match exists if: normalized names are equal (or one contains the other) AND the date ranges overlap by more than 50%.
3. Two separate stints at the same company (non-overlapping dates, significant gap): two separate files. Disambiguate slugs: `klout.md` and `klout-2.md`. When in doubt, ask the user.
4. If date ranges are ambiguous or overlapping in a confusing way, ask before deciding.

**Actions:**
- `CREATE` — no match found; a new file will be created
- `SUPPLEMENT` — match found; existing file will receive new content in placeholders and supplementary blocks
- `SKIP` — match found; existing file has content in all relevant sections and no new information from this source
- `ASK` — ambiguous; present the situation to the user and wait for direction

**For projects:** use fuzzy name matching. If the project name from the source is clearly the same project as an existing file (same name, same company, overlapping dates), treat as SUPPLEMENT. Otherwise CREATE.

**Print the plan and ask for confirmation before writing anything:**

```
Ready to ingest from: [source files]

Planned actions:
  [ACTION]  experience/[slug].md   ([Company] — [Title], [start]–[end])
  [ACTION]  experience/[slug].md   (...)
  [ACTION]  projects/[slug].md     ([Project name])
  ...

  RESUME.md: will ask before updating (opt-in)
  skills.md: [N] skills to add to "Ingested (needs review)" section
  intent.md: not touched (fill this manually — resume sources don't contain career intent)

Proceed? (yes / no / show details for [file])
```

If the user asks for details on a specific file, show what you extracted and what you planned to write before proceeding.

## Phase 4 — Write

### CREATE: new experience file

Generate `experience/<slug>.md`. Slug is kebab-case company name. If a slug collision exists, disambiguate with `-2`, `-3`, etc.

Use this structure. Fill each section with what was extracted. For sections with no extracted content, use `<!-- not yet captured -->`.

```markdown
---
company: "[company]"
slug: "[slug]"
title: "[title]"
start: "[YYYY-MM]"
end: "[YYYY-MM or present]"
location: "[location or empty]"
employment_type: "full-time"
ingested_sources:
  - file: "[source filename]"
    date: "[YYYY-MM-DD]"
---

# [Company] — [Title]

## Context

[extracted context, or <!-- not yet captured -->]

## Contributions

[For each named initiative extracted from the source:]

### [Initiative name or descriptive label]
- what: [activity phrase — what was done or built, without technology names]
- stack: [technologies, frameworks, tools mentioned for this initiative]
- impact: [if the source states an outcome or metric, capture it; otherwise <!-- not yet captured -->]
```

**Extraction rules for Contributions:**
- Each `**Name** – description` bullet in the source becomes a `### Name` sub-section
- `what`: the activity phrase, stripping technology names (those go in `stack`)
- `stack`: combine tech names from the bullet with any Tools & Methods list in the source
- `impact`: only write if the source explicitly states a result or metric; never infer or estimate
- For bullets without a bold name: create a short descriptive label from the activity
- If the role has no distinct initiatives (e.g., a short early-career role): use `<!-- not yet captured -->` and let `/excavate` fill in structure

**Important:** Do not write Decisions & Tradeoffs to experience files — that section is deferred to a future `behavioural/` extension.

### CREATE: new project file

Generate `projects/<slug>.md` using the same additive approach. Fill Problem and What I Built from extracted description. Leave Impact, Technical Decisions, and Through-Line as `<!-- not yet captured -->` unless the source explicitly states impact metrics.

### SUPPLEMENT: existing file

Read the existing file. For each section:
- If the section body is `<!-- not yet captured -->`: replace with extracted content
- If the section has real content AND the source has new content for it: append a supplementary block **after** the existing content:

```markdown
---
<!-- supplementary — ingested from [source] on [date] — review and merge or delete this block -->

[extracted content for this section]

<!-- end supplementary -->
---
```

Update the `ingested_sources` frontmatter array to add the new source entry.

### Multi-source union behavior

When multiple sources both contain content for the same role:
- The source with more detail for a given section wins the initial fill
- Both sources' content for a section appears in supplementary blocks if the section already had content
- The user sees everything and decides what to keep

## Phase 5 — Update skills.md

If skills were extracted from any source:
1. Read the existing `skills.md`
2. Identify skills not yet present in any form (check all sections)
3. For each new skill, determine which section it belongs to based on the skills.md structure (Languages, Big Data, Streaming & Messaging, Cloud Platforms, DevOps, etc.)
4. Add new skills into the appropriate existing section rather than a catch-all "Ingested" pile. Format each entry with:
   - Depth signal inferred from the source (expert if used heavily in multiple roles, proficient if used in a project, familiar if listed but not featured)
   - Recency signal inferred from the role dates (active if within last 2 years, recent if within 5 years, historical if older)
   - 1–2 sentences on what they did with it, drawn directly from the source
   - `_Used at:_` links to the experience files where it appears
   - `_Reference:_` link to the official/canonical page for the technology (use well-known URLs only — apache.org, cloud.google.com, etc.; omit if unsure)

If `skills.md` has no sections yet (stubs only), append a `## Ingested (needs review)` section grouped by domain with the same format, so the owner can rename and reorganize:

```markdown
## Ingested — [Domain] (needs review)
<!-- Move these into the right section once you're happy with the depth/recency signals. -->

**[Skill]** — expert|proficient|familiar — active|recent|historical
[1–2 sentences from source]
_Used at:_ [Company](experience/slug.md)
_Reference:_ [Name](url)
```

Do NOT modify any existing skill entries. Do NOT add skills already present elsewhere in the file.

## Phase 6 — Update RESUME.md (opt-in)

Ask: "Update RESUME.md with links to the new files? I'll only add new entries — no existing summaries will be touched."

If yes:
- For each newly created `experience/` file: add a new entry under `## Experience` in reverse-chronological order. Format:
  ```markdown
  ### [Company] — [Title] ([start]–[end])
  [1–2 sentence summary generated from the extracted descriptions]
  → [Deep dive](experience/[slug].md)
  ```
- For each newly created `projects/` file: add under `## Selected Projects`:
  ```markdown
  ### [Project Name]
  [1-sentence description]
  → [Project detail](projects/[slug].md)
  ```
- If the Experience or Projects section contains only `<!-- not yet captured -->`, replace it
- Otherwise, add new entries in the correct chronological position
- Do NOT modify any existing entries
- Do NOT reorder existing entries

## Phase 7 — Update llms.txt

For each new file created, add an entry to the appropriate section of `llms.txt`:
```
- [experience/slug.md](experience/slug.md): [Company] — [Title] ([start]–[end])
```

## Phase 8 — Report

Print a summary:

```
Ingest complete.

Created ([N]):
  experience/[slug].md    — [Company], [Title] ([dates])
  ...

Supplemented ([N]):
  experience/[slug].md    — [N] supplementary blocks added, [N] placeholders filled
  ...

Skipped ([N]):
  experience/[slug].md    — already fully captured

Needs your attention:
  skills.md               — [N] skills in "Ingested (needs review)" — add depth/recency signals
  [file].md               — [N] supplementary blocks to review and merge or delete

Not touched:
  intent.md               — fill this manually; career intent cannot be extracted from a resume

Next steps:
  /talent-brain:excavate            — deepen any experience file with a structured interview
  /talent-brain:fit <jd>            — assess your profile against a job description
```

## Hard invariants — never violate these

1. **Never write to `intent.md`** — it cannot be inferred from resume sources
2. **Never overwrite a section that contains content other than `<!-- not yet captured -->`** — use supplementary blocks
3. **Never silently skip** — always report what happened to every file
4. **Ask before writing when ambiguous** — duplicate companies, overlapping date ranges, unclear project matches
5. **No orphans** — every created file must be reachable from RESUME.md; if the user declines the RESUME.md update, remind them to add the link manually
6. **Source fidelity** — only write what was in the source; do not infer Outcomes, Decisions, or Through-Line from resume bullet points alone
