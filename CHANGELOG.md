# Changelog

## [0.2.0] — 2026-07-06

### Skills
- `intent` — guided conversation to capture career preferences, writes `intent.md`
- `publish` — generate a current resume, rebuild README.md, refresh SCHEMA.md, commit and push
- `gws` — Google Workspace CLI helper for listing/downloading Drive files without loading content into context
- `feedback` — file a bug report or suggestion as a GitHub issue on this repo

### Schema
- Bumped to 1.1 — see `SCHEMA.md` changelog for full detail. Highlights: `experience/<slug>.md` restructured to `Context` / `Responsibilities` (optional) / `Contributions` with `what`/`stack`/`impact` atoms; documented the "expanded atom" pattern for deep employer-internal initiatives; `resume.md` added to the file tree and `llms.txt` manifest contract.

### Fixes
- `tb-init`'s skill-copy step no longer nests `.claude/skills/` one level too deep on repeat runs
- Renamed all `/talent-brain:init` doc references to the correct `/talent-brain:tb-init`
- `ingest`'s generated `_Used at:_` links now resolve correctly from `skills.md` at the profile root
- Right-sized "plugin loads automatically" claims — the actual mechanism is bundled `.claude/skills/`, not the inert `plugins` key in `settings.json`
- `tb-init` now generates a `.gitignore` for every new profile (`source/`, `.serena/`, local settings)
- Fixed skill-invocation examples across all docs and skills: the only documented install path (clone the repo, open the folder — no plugin install) loads skills bare via `.claude/skills/`, so every example now reads `/skill-name` instead of the incorrect `/talent-brain:skill-name`, which only applies to a marketplace-plugin install this project doesn't currently document

## [0.1.0] — 2026-06-08

Initial release.

### Skills
- `tb-init` — scaffold a new profile with stub files
- `ingest` — populate from resume PDFs and LinkedIn ZIP exports; additive across multiple sources
- `excavate` — structured interview for new entries and augmenting existing ones
- `showcase` — persuasive interactive pitch for hiring managers
- `fit` — evidence-backed fit assessment against a job description
- `gap` — classify gaps as hard / profile / framing for a target role
- `generate` — tailored resume document from full profile depth
- `cover-letter` — argument-first cover letter with honest gap handling

### Schema
- `RESUME.md` index with llms.txt pattern
- `experience/`, `projects/`, `skills.md`, `intent.md` structure
- `extensions/` for publications, patents, speaking, certifications, press, awards, open-source
- `SCHEMA.md` agent contract with invariants and conventions
