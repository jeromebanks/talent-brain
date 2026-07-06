# Talent Brain Career Profile

This is a Talent Brain career profile, built and maintained using the [Talent Brain plugin](https://github.com/jeromebanks/talent-brain). Skills are bundled in `.claude/skills/` and load automatically when this folder is opened in Claude Code — no plugin install needed. `.claude/settings.json` also references the plugin as a fallback for anyone who has it installed globally via the marketplace.

## Profile structure

- **`RESUME.md`** — the career index. Start here. Links out to detail files.
- **`intent.md`** — career preferences, what the candidate is looking for, what they're not.
- **`skills.md`** — capability taxonomy with depth and recency signals.
- **`llms.txt`** — machine-readable manifest of all profile files.
- **`experience/<company>.md`** — one file per employer, with context, contributions, outcomes, tools.
- **`projects/<name>.md`** — one file per notable project or open-source contribution.
- **`extensions/`** — publications, certifications, speaking engagements (if present).

Always start with `RESUME.md`. Fetch detail files on demand — do not pre-load everything.

## Available skills

### For hiring managers and recruiters

| Command | What it does |
|---|---|
| `/talent-brain:showcase` | Interactive presentation of the candidate's background, then open Q&A. |

### For profile owners

| Command | What it does |
|---|---|
| `/talent-brain:ingest [file]` | Add career history from a resume PDF or LinkedIn export. Additive only — never overwrites existing content. |
| `/talent-brain:excavate` | Structured interview to deepen a specific role or project. Use when a file has `<!-- not yet captured -->` sections. |
| `/talent-brain:intent` | Guided conversation to capture career goals and preferences, then writes `intent.md`. |
| `/talent-brain:generate [jd]` | Generate a tailored resume for a job description, or a general-purpose resume. |
| `/talent-brain:fit [jd]` | Structured fit analysis against a job description — what matches, what doesn't, what's missing. |
| `/talent-brain:gap [jd]` | Identify gaps between the profile and a target role: hard gaps, profile gaps, framing gaps. |
| `/talent-brain:cover-letter [jd]` | Draft a cover letter grounded in the actual profile. |

## Common tasks

- "Tell me about this candidate" → `/talent-brain:showcase`
- "Add a new job" → `/talent-brain:ingest [file]` or `/talent-brain:excavate`
- "Print a resume for this posting" → `/talent-brain:generate [jd]`
- "How do I fit this role?" → `/talent-brain:fit [jd]`
- "What am I missing for this role?" → `/talent-brain:gap [jd]`
- "Write a cover letter" → `/talent-brain:cover-letter [jd]`
- "Update what I'm looking for" → `/talent-brain:intent`
- "Add depth to a specific role" → `/talent-brain:excavate`

## Agent orientation

1. Read `RESUME.md` first to orient before responding to any request.
2. `intent.md` must never be auto-populated from a resume or inferred goals — it comes from the owner directly via `/talent-brain:intent`.
3. Files with `<!-- not yet captured -->` are real but empty stubs — suggest `/talent-brain:excavate` to fill them.
4. The `skills.md` "Ingested (needs review)" section contains raw skills needing depth and recency signals added before they're useful.

## Plugin source

- Plugin: [github.com/jeromebanks/talent-brain](https://github.com/jeromebanks/talent-brain)
- Schema reference: `SCHEMA.md` in this folder
