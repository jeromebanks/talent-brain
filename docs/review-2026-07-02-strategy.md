# Talent Brain — Feasibility & Recommendations (2026-07-02)

Companion to `review-2026-07-02-findings.md` (concrete bugs and inconsistencies). This doc
is the step-back view: is the approach sound, what's worth adding, what's worth cutting.

---

## 1. Overall assessment

The core bet — *depth is lost by flat resumes; capture it once, in structured Markdown, and
let an LLM re-project it per audience* — is sound, and the profile proves it. The
onyx-gsk.md file alone contains more usable signal (GPU utilization 1.75%→100%, 2h11m→43m
runtimes, the Zarr merge-correctness bug, the MCP registry design iteration) than any
2-page resume could carry. The excavate → atoms → generate pipeline is the real product,
and it already works.

Where the vision outruns reality is the **consumption side**. Be clear-eyed about who
actually reads this thing:

| Consumer | Realistic today? |
|---|---|
| You + your own Claude (generate, fit, gap, cover-letter) | **Yes — this is the product.** |
| A hiring manager you're already talking to, via Cowork/showcase | Plausible — late-funnel differentiator, and the demo itself is a signal for agentic-engineering roles |
| A recruiter cold-opening your GitHub repo in Claude Code | Rare. Recruiters live in ATS/LinkedIn; asking them to open a repo is a big ask |
| ATS / hiring-agent screeners fetching llms.txt | Not yet. Screeners parse uploaded PDFs; none crawl candidate-hosted graphs |
| Tier 2 (OPPORTUNITY.md, two-sided matching) | Correctly deferred. Classic two-sided bootstrap problem with no supply-side incentive solved |

**Implication:** don't judge the project by whether strangers adopt the profile format.
Judge it by whether *your* applications get better. The strongest bridge between the tiers
is cheap and missing: put the profile URL prominently in every generated resume and cover
letter ("Full interactive profile: github.com/jeromebanks/talent-brain-profile — open in
Claude Code and ask it anything"). The PDF is the distribution channel the profile lacks;
let the flat artifact advertise the deep one. A curious hiring manager following that link
is exactly the self-selecting audience showcase was built for.

A second honest caveat: a candidate-authored, candidate-hosted profile has a trust ceiling
for evaluators — showcase is your agent speaking on your behalf. The evidence-only
invariants in fit/showcase are the right mitigation; keep them strict, because the moment
one claim is unverifiable-and-wrong the whole graph is discounted.

---

## 2. Highest-priority actions (this week, mostly profile not plugin)

1. **Fix the broken skill install** (findings A1/A2) — the "self-contained profile" claim
   is the product's front door and it's currently false.
2. **Run `/talent-brain:intent`** — your own docs call intent.md the most valuable file;
   it's 100% placeholders after three weeks of excavation. Everything downstream (fit,
   cover-letter "why this role", showcase positioning) is running with the differentiating
   layer empty.
3. **Fill the RESUME.md index sections** (Summary, Core Competencies, Skills overview,
   Interests & Intent pointer). The depth exists; the landing layer doesn't. Every consumer
   reads this first.
4. **Add the profile .gitignore** (finding C1) before anything else touches git in a
   public repo.
5. **Dogfood `/talent-brain:generate` against a real JD** (already on HANDOFF's list) —
   it's the flagship skill and it has never been run end-to-end on the finished profile.

---

## 3. Features worth adding

### 3a. `/talent-brain:check` — a profile linter (highest ROI new skill)

Today's review found: missing required frontmatter, an orphan file, ~50 broken relative
links, a stale schema copy, stale `updated:` dates, llms.txt/RESUME.md disagreements, and
leftover template text — every one mechanically detectable. A read-only skill that
validates the SCHEMA.md invariants and prints a fix-list would have caught all of it, and
it makes the schema contract *enforceable* rather than aspirational. Run it automatically
at the start of publish. This is also the feature that makes the format credible to anyone
else adopting it.

### 3b. PDF/HTML rendering for generate

`generate` currently ends with "paste into Typora or run pandoc" — the weakest moment of
the flagship workflow, right at the point of maximum user intent. Have generate (or a
`render` step) write the Markdown to a file and produce a clean single-page HTML (easy to
print-to-PDF, zero toolchain assumptions) or drive pandoc/typst when available. Job
applications end in an upload box; the product should end there too.

### 3c. An `applications/` workspace — make the job search stateful

fit, gap, generate, and cover-letter are all one-shot: results scroll away in the
terminal. A real search is a pipeline over weeks. Add a lightweight convention —
`applications/<company-role-slug>/` holding `jd.md`, `fit.md`, `resume.md`,
`cover-letter.md`, `status.md` — and have the four JD-consuming skills offer to save into
it. This converts four disconnected tools into a system, gives fit/gap a place to be
re-read before an interview, and costs almost nothing (a folder convention + "save this?"
prompts). Keep it gitignored or private by default.

### 3d. `extensions/publications.md`

Already identified in HANDOFF: the LASTA and Klout Topics acknowledgements have no home.
The extension type exists in the schema; create the file, link it from RESUME.md and
llms.txt, and move the two PDFs from the plugin repo root into the profile's `source/`.
Cheap, and it's evidence-grade material (published research papers) currently invisible.

### 3e. Interview prep (`behavioural/`) — keep deferred, but it's the next big consumer

The excavated atoms (especially Decisions-flavored material like the Zarr merge bug, the
Vertex→Nextflow re-architecture, the MCP one-tool-vs-many iteration) are exactly what
behavioral/system-design interviews ask for. When Tier-1 polish is done, a
`/talent-brain:prep <jd>` that assembles STAR-shaped stories *from existing atoms* (not new
excavation) is the highest-value next skill — it monetizes depth you already captured.

### 3f. Resume-span policy in generate

21 roles over 37 years is a genuinely unusual asset and a genuinely real ageism exposure.
`generate` already caps roles >15 years old at 2–3 bullets; go further and make it an
explicit option (default: full detail for last ~15 years, then a single "Earlier
experience: financial trading systems, enterprise middleware, early internet — details in
profile" line with the profile URL). The deep graph means nothing is lost — that's the
whole point of the architecture, so let the resume exploit it.

---

## 4. Things to change

- **Single source of truth for generated files** — tb-init and publish embed full
  CLAUDE.md/README.md bodies inline while `templates/` holds diverging parallel copies.
  Skills should read templates; drift has already happened (findings D3).
- **Schema versioning discipline** — the atom restructure shipped inside "1.0". Bump to
  1.1, add a schema changelog, and give publish the job of refreshing each profile's
  SCHEMA.md copy. Profiles in the wild (even just yours) are otherwise permanently pinned
  to their init-date schema.
- **Right-size the claims** — README/templates promise auto-loading plugins and
  zero-setup recruiter experiences the platform can't deliver yet (your own
  platform-gaps doc says so). Under-promise: "skills are bundled in the folder; open it
  in Claude Code."
- **Move personal material out of the plugin repo** — INIT.md, HANDOFF.md, and the two
  research PDFs are Jerome-the-user artifacts inside Jerome-the-maintainer's public repo.
  The plugin repo should look like a product; the profile repo (or its `_dev/`) is where
  project-state lives.
- **Document the "expanded atom" pattern** that onyx-gsk.md invented (H4
  Problem/Built/Decisions/Through-Line under a contribution), or agents can't rely on it.
  It's a good pattern — deep-dive-in-place for employer-internal work that can't be a
  public project file — it just needs to be in the contract.

---

## 5. Things to omit or stop doing

- **Don't build OPPORTUNITY.md yet** (you already know this — reaffirming). Nothing on the
  Tier-1 list depends on it, and no one will author the supply side.
- **Drop `prior-experience.md`** — superseded by the 12 individual files it indexes; it
  now only exists to violate the no-orphans invariant.
- **Reconsider `gws` as a shipped plugin skill** — it's personal infrastructure (assumes a
  CLI nobody else has) inside a public plugin. Either scope its description ("if the gws
  CLI is installed…") or move it to your personal skill set. Same question, softer, for
  how much of PRODUCT.md's manifesto belongs in the repo users clone versus a blog post —
  though as a positioning artifact for *you* it arguably earns its place.
- **Stop maintaining CHANGELOG.md by hand if it lags** — a stale changelog is worse than
  none; either update it as part of releases or fold history into git tags.

---

## 6. One structural thought for later

The plugin currently conflates two products: (a) a **schema + contract** (SCHEMA.md,
llms.txt convention, invariants) and (b) a **set of Claude skills** that operate on it.
The schema is the durable, forkable, standard-shaped thing; the skills are one
implementation. If Tier 2 ever matters, it's the schema that other tools would adopt —
consider making SCHEMA.md's versioned life (changelog, migration notes, validation rules)
first-class now, since that's cheap while there's one profile in the world and painful
later. The `/check` linter in 3a is the first concrete step: a schema with a validator is
a standard; a schema without one is a wish.
