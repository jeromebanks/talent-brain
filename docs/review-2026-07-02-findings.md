# Talent Brain — Quality & Consistency Review (2026-07-02)

Scope: all plugin docs and 12 skills in `~/dev/talent-brain`, plus the live profile at
`~/talent-brain-profile`. Companion doc: `review-2026-07-02-strategy.md` (feasibility and
feature recommendations).

Findings are ordered by severity within each section.

---

## A. Bugs in the plugin

### A1. tb-init's skill copy produces a broken nested directory (HIGH)

`skills/tb-init/SKILL.md` Step 4 says:

```
cp -rL .claude/skills <full-path>/.claude/skills
```

When the destination directory already exists, `cp` copies the source *into* it. The live
profile shows exactly this failure:

```
~/talent-brain-profile/.claude/skills/
├── publish/SKILL.md          ← correct level (manually copied later)
└── skills/                   ← wrong: nested one level too deep
    ├── tb-init/ cover-letter/ fit/ ingest/ feedback/ gap/ intent/ excavate/ generate/ showcase/
```

Consequence: **10 of 12 skills do not load when the profile folder is opened in Claude
Code.** The profile is not self-contained, despite README.md, CLAUDE.md, and the plugin
README all promising it is. `gws` is also absent from the copy entirely.

Fix in the skill: copy per-skill or use `mkdir -p <path>/.claude && cp -RL .claude/skills
<path>/.claude/` semantics that are idempotent; verify the result with
`ls <path>/.claude/skills/*/SKILL.md`. Fix the live profile by flattening
`.claude/skills/skills/*` up one level and adding `gws`.

### A2. Documented command name doesn't exist (HIGH)

The init skill is named `tb-init`, so the real command is `/talent-brain:tb-init`. But:

- plugin `README.md` ("Run `/talent-brain:init`")
- plugin `CLAUDE.md` first-response welcome message (verbatim quote)
- `CHANGELOG.md` (lists a skill named `init`)

all say `/talent-brain:init`. A new user following the README types a command that doesn't
resolve (or resolves to the *built-in* `/init`, which scaffolds a CLAUDE.md — a confusing
failure mode). Either rename the skill or fix every doc; if `tb-init` was chosen to avoid
colliding with the built-in `/init` in the clone-and-open path, the docs must say
`tb-init`.

### A3. ingest writes broken skill links (MEDIUM)

`skills/ingest/SKILL.md` Phase 5 template:

```
_Used at:_ [Company](../experience/slug.md)
```

`skills.md` lives at the profile **root**, so `../experience/…` escapes the profile. The
live `skills.md` now contains ~50 broken relative links (every `_Used at:_` line).
Fix the template to `experience/slug.md` and repair the profile file.

### A4. publish/SKILL.md has no frontmatter (MEDIUM)

Every other skill has `name:` + `description:` YAML frontmatter; `publish` has none. It
shows up in the skill index with the raw H1 ("Talent Brain — Publish") as its description,
which gives Claude no trigger guidance. Add frontmatter matching the house style.

### A5. `.claude/settings.json` plugin declaration is inert, but docs promise auto-install (MEDIUM)

Your own `docs/claude-code-platform-gaps.md` documents that the `plugins` key in a project
settings.json does nothing today. Yet the generated profile README tells recruiters "No
setup required. The plugin loads automatically," and the plugin README says the same for
GitHub visitors. Combined with A1 (the copied skills are broken), a recruiter opening the
profile today gets **neither** path. Until the platform gap closes, the honest claim is:
"skills are bundled in this folder and load automatically" — and A1 must be fixed for even
that to be true.

---

## B. Schema violations and drift in the live profile

### B1. Project files missing required frontmatter (MEDIUM)

SCHEMA.md marks `type` and `status` as required for `projects/*.md`. Both
`projects/brickhouse.md` and `projects/satisfaction.md` lack both fields, and both use
`end: null` (not a schema value — use `"present"` or omit). Any agent following the schema
contract ("all fields marked required must be present for skills to function") is entitled
to choke here.

### B2. Orphan file: `experience/prior-experience.md` (MEDIUM)

Listed in llms.txt but **not linked from RESUME.md**, violating Invariant 1 ("No
orphans... the index is the agent's navigation source of truth"). It's also now redundant —
all 12 roles it indexes have their own files. Recommend deleting it, removing the llms.txt
entry, and retargeting the two `skills.md` entries that point at it (Java, C++) to the
actual role files (bea-plumtree, dicarta, timber-hill, lehman…).

### B3. Profile SCHEMA.md is a stale copy (MEDIUM)

The profile's SCHEMA.md predates the atom-structure revision (missing the "Old-format
Contributions" tolerance clauses, among ~27 modified hunks). Both claim version "1.0".
Two problems: (a) schema changed materially without a version bump; (b) there is no
mechanism to refresh the copy in existing profiles. Recommend: bump to 1.1 with a schema
changelog section, and make `/talent-brain:publish` (or a new update step) re-copy
SCHEMA.md from the plugin.

### B4. onyx-gsk.md uses an undocumented structure (LOW, but decide deliberately)

The three big contributions (nf-forge, Phenomics, Cellpose3D) carry H4 sub-sections —
`#### Problem / What I Built / Technical Decisions / Through-Line` — i.e., the *project
file* schema embedded inside an experience file. The schema's tolerance clause covers extra
H2 sections, not this. The content itself is excellent (the best in the profile); the issue
is only that the pattern is invisible to the contract. Either document it in SCHEMA.md as
an "expanded atom" (an initiative may optionally carry the project sub-sections), or split
these into `projects/` files — though your recorded design decision says projects/ is for
public/cross-employer work only, so documenting the expanded-atom pattern is the
consistent choice.

### B5. RESUME.md — the index is the weakest layer (HIGH as a content gap)

After a complete 21-role excavation, the index still has `<!-- not yet captured -->` for
**Summary, Interests & Intent, Core Competencies, and Skills** — the four sections every
skill and every human reads first. `updated:` says 2026-06-25 though excavation ran to
07-01. Every consumer (fit, showcase, a recruiter skimming GitHub) currently hits the
richest profile in existence fronted by an empty landing section. This is the single
highest-value content fix available. (Strategy doc proposes folding an "index refresh"
into publish.)

### B6. Small profile inconsistencies (LOW)

- **Timber Hill title**: RESUME.md header says "Programmer/Analyst / Tech Lead";
  frontmatter and llms.txt say "Programmer/Analyst". Body supports Tech Lead — update
  frontmatter/llms.txt, per "body is truth."
- **llms.txt leftover boilerplate**: identity line ends with the template instruction
  "Update this as your profile develops." — visible to every agent that reads the manifest.
- **LinkedIn URL** in RESUME.md frontmatter is the legacy `/pub/…/1/77/832` format; use the
  modern `/in/…` URL.
- **`resume.md`** (written by publish) is not in the SCHEMA.md file tree or the llms.txt
  manifest contract — add it or agents can't discover the rendered resume.
- **intent.md is 100% placeholders** — already tracked in HANDOFF.md; it's the file the
  whole product pitch calls "the most valuable in the profile."

---

## C. Data-hygiene / privacy risks

### C1. `source/` resumes one `git add .` from a public repo (HIGH)

The profile repo is **public**. `source/` (28 resume PDFs/DOCXs spanning 1997–2025, with
contact details) and `.serena/` are untracked, and there is **no .gitignore**. tb-init
Step 6 itself instructs `git add .`. Add a `.gitignore` (`source/`, `.serena/`, `_dev/`)
to the profile now, and make tb-init generate one in every new profile.

### C2. Personal material in the public plugin repo (MEDIUM)

Untracked in the plugin root: `HANDOFF.md` (personal session state), the LASTA and Klout
Topics PDFs (profile source material — they belong in the profile's `source/`, feeding the
future publications extension). Also `INIT.md` is Jerome-specific ("Build Jerome's …
profile") inside a repo the README tells strangers to clone — move to the profile's `_dev/`
or delete (its content is superseded by PRODUCT.md + SCHEMA.md + HANDOFF.md).

### C3. `visibility:` is decorative (LOW)

intent.md carries `visibility: "public"` but no skill reads or enforces it; the schema
roadmap acknowledges privacy controls are future work. Fine — but the intent skill tells
users "privacy controls are on the roadmap," so keep that promise visible in the roadmap
section.

---

## D. Doc/skill consistency nits

1. **CHANGELOG.md is stale**: lists `init` (now `tb-init`); omits `intent`, `publish`,
   `gws`, `feedback`; still 0.1.0 despite the schema restructure (atoms, Responsibilities)
   — that's at least a 0.2.0.
2. **Template vs schema frontmatter mismatch**: `templates/RESUME.md` and the tb-init
   embedded RESUME.md use singular `email:`/`website:`; SCHEMA 1.0 specifies `emails:`
   (list) and `websites:` (list of {url,label}). The live profile follows the schema;
   the templates and tb-init don't.
3. **Two sources of truth for generated files**: tb-init embeds full CLAUDE.md/README.md
   text inline; `templates/` holds parallel versions; publish embeds a third README
   variant. `templates/CLAUDE.md` has already drifted (much thinner than the tb-init
   version). Make skills read from `templates/` and delete the inline copies.
4. **showcase references removed sections**: "Navigate to Team & Scope sections and
   Decisions & Tradeoffs sections in the experience files" — both were removed from the
   schema (explicitly "no longer written by skills"). Update to Context/Responsibilities/
   Contributions atoms.
5. **gap skill uses stale names**: refers to "`tb-fit` Phase 1" alongside
   `/talent-brain:fit`.
6. **ingest temp path**: `/tmp/talent-brain:ingest-$(date +%s)/` — a colon in a path is
   asking for trouble with some tools; use a hyphen. (Also: your own environment mandates a
   scratchpad dir over /tmp.)
7. **feedback skill title conventions** show `tb-ingest`/`tb-excavate` prefixes that no
   longer match skill names.
8. **README FAQ** says "the profile carries its own plugin configuration" for the GitHub
   share path — overstates, per A5.
