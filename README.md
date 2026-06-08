# Talent Brain

An agent-readable career knowledge graph for Claude Code.

Build a structured profile of your career history — experience, projects, skills, and where you're heading — that both humans and AI agents can navigate, query, and generate documents from.

The profile follows the [llms.txt](https://llmstxt.org/) pattern: a compact index (`RESUME.md`) with links to deep-dive files fetched on demand. Designed to survive AI screening, answer recruiter questions, and give a hiring agent more signal than any PDF.

---

## Install

```bash
npm install -g talent-brain
```

Or add to your Claude Code settings:

```json
{
  "plugins": [
    {
      "name": "talent-brain",
      "source": { "source": "npm", "package": "talent-brain" }
    }
  ]
}
```

---

## Quick start

**1. Initialize a new profile**

```
/talent-brain:init
```

Creates the profile directory structure and stub files. Takes ~2 minutes. You'll need: your name, current title, location, and optional contact links.

**2. Populate from existing resumes**

```
/talent-brain:ingest resume.pdf
/talent-brain:ingest resume-2015.pdf resume-2023.pdf linkedin-export.zip
```

Handles PDFs, plain text, and LinkedIn ZIP exports. Safe to run multiple times — additive only, takes the union across all sources.

**3. Deepen with a structured interview**

```
/talent-brain:excavate
/talent-brain:excavate gsk
```

A conversational interview that surfaces what resumes strip out: outcomes, tradeoffs, what's still running, what got adopted. Works for new entries and for augmenting thin existing ones.

**4. Fill in your intent**

Open `intent.md` and fill it in manually. This is the one file no tool writes — it contains what you actually want next, which no resume can express.

---

## Skills

| Skill | What it does |
|---|---|
| `/talent-brain:init` | Scaffold a new profile |
| `/talent-brain:ingest [files]` | Populate from resume PDFs or LinkedIn export |
| `/talent-brain:excavate [role]` | Structured interview — new entry or augment existing |
| `/talent-brain:showcase ["context"]` | Persuasive pitch for a hiring manager, with interactive Q&A |
| `/talent-brain:fit [jd]` | Evidence-backed fit assessment against a job description |
| `/talent-brain:gap [jd]` | What to develop, surface, or reframe for a target role |
| `/talent-brain:generate [jd]` | Tailored resume document from full profile depth |
| `/talent-brain:cover-letter [jd]` | Argument-first cover letter, handles gaps honestly |

Job descriptions can be pasted text, a file path, or a URL.

---

## Profile structure

```
your-profile/
├── llms.txt              # Agent manifest — lists all files with descriptions
├── RESUME.md             # Index — human + agent entry point (~2K tokens)
├── SCHEMA.md             # Schema reference for agents and contributors
├── intent.md             # What you want next (fill this manually)
├── skills.md             # Capability taxonomy with depth + recency signals
├── experience/
│   └── <company>.md      # One deep-dive per employer
├── projects/
│   └── <project>.md      # One deep-dive per notable project
└── extensions/           # Optional: publications, patents, speaking, certifications, press, awards, open-source
```

The profile is plain Markdown. Host it on GitHub so agents and humans can fetch any file by URL.

See [SCHEMA.md](SCHEMA.md) for the full schema specification.

---

## The showcase use case

Share your profile with a hiring manager or recruiter:

```
/talent-brain:showcase "senior data infrastructure role"
```

Opens an interactive session where they can ask any question about your background — Claude navigates the profile and answers from the evidence. More signal than a PDF in less time.

---

## For hiring agents and recruiters

The profile is designed for agent ingestion. Point any LLM at `RESUME.md` (or `llms.txt`) and it can navigate to the relevant depth. The schema is defined in `SCHEMA.md`.

```
Fetch: https://raw.githubusercontent.com/<user>/<profile>/main/llms.txt
```

---

## Contributing

This project is early. Issues and PRs welcome at [github.com/jeromebanks/talent-brain](https://github.com/jeromebanks/talent-brain).

The schema is intentionally extensible — industries have different needs (publications, patents, portfolios). If your field needs something not covered, open an issue.

---

## License

MIT
