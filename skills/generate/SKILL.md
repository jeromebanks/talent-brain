---
name: generate
description: Generate a tailored resume for a specific job description, or a general-purpose resume if no JD is provided. Pulls from the full profile depth and positions the candidate's experience for the target role. Outputs an ATS-safe HTML file, auto-converted to PDF via whatever CLI tool is available (headless Chrome/Chromium, wkhtmltopdf, or pandoc), no manual steps required. Use when a candidate needs a resume document to send or upload.
---

# Talent Brain — Generate Resume

You are generating a resume document from the candidate's Talent Brain profile. The output is tailored to a specific role if a JD is provided, or optimized as a strong general-purpose resume if not.

**ATS-legibility beats cosmetics.** This tool exists partly to fight the failure mode where AI screeners misparse a visually "designed" resume and lose the signal in it. Every choice in this skill — layout, formatting, the conversion pipeline — optimizes for a parser reading the text cleanly, not for a human admiring the typography. Plain and correct beats polished and fragile.

## Inputs

- `/generate` — generate a general-purpose resume
- `/generate "senior ML engineer at fintech startup"` — generate tailored to a context description
- `/generate [file or URL]` — generate tailored to a specific job description

If a JD is provided as a file path or URL, read/fetch it first.

## Phase 1 — Load the full profile

Read all of the following before generating anything:
1. `RESUME.md` — index, summary, experience list
2. `intent.md` — direction and preferences (informs the summary and objective). Only "Most Important Factors," "What I'm Not Interested In," and "Where I'm Going" are resume material — never pull from "Reasons for the Move," "Availability & Job Search Activity," or "Work Authorization" into resume text. Those sections don't belong on a resume regardless of source.
3. `skills.md` — full capability taxonomy
4. **All** `experience/*.md` files — you need the depth, not just summaries
5. **All** `projects/*.md` files — select the most relevant

Unlike other skills, generate reads everything upfront. A resume that only draws from summaries will be generic and weak.

## Phase 2 — If a JD was provided: run fit mapping

Before generating, identify:
- Which requirements from the JD are strongly met → lead with these
- Which experience files are most relevant → feature more prominently
- Which projects are most relevant → include; deprioritize others
- Which skills should be surfaced → use the JD's vocabulary where accurate
- What the 2-line summary should emphasize → the candidate's strongest fit signal for this role

If no JD: identify the top 3 signals from the profile overall and build the summary around them.

## Phase 3 — Generate the content

### Content rules

- Target length: 1 page for < 10 years experience, 2 pages for longer careers. Flag if the output is running long.
- Contact line at top: name | location | email | linkedin | github (omit blanks). If `RESUME.md` frontmatter lists multiple emails, use the first one listed as primary — don't guess or default to whichever looks more "professional."
- Use the candidate's exact company names and titles from the profile — never paraphrase or invent.
- Dates: `MM/YYYY – MM/YYYY` or `MM/YYYY – Present`. Consistent format throughout.
- Consistent tense: past tense for past roles, present tense for the current role, throughout.

### Summary / Professional Profile (3–4 sentences)

Lead with the through-line: what this person is, what they're known for, what they're heading toward. If a JD was provided, open with the fit signal. Do not use generic filler ("results-driven professional", "dynamic leader"). Every sentence must contain a specific claim.

For targeted resumes: the summary should make the fit obvious in the first two sentences.

### Skills section

Pull from `skills.md`. For targeted resumes: surface skills that appear in the JD first, in the JD's vocabulary (where that vocabulary is accurate to the candidate's actual experience). Do not list skills the candidate doesn't have just because the JD asks for them.

Group by domain. Keep depth signals (`expert`, `proficient`) if the format allows; drop them if space is tight. Omit `historical` skills unless directly relevant.

### Experience section (reverse chronological)

For each role:
1. Read the full `experience/<slug>.md` — do not rely on the RESUME.md summary alone
2. For each `### Initiative` sub-section in Contributions, generate one resume bullet
3. For targeted resumes: select the most relevant initiatives first; omit or compress less relevant ones
4. Each bullet is synthesized from the atom fields: lead with `impact` if present, then `what`, with `stack` woven in naturally. The LLM picks the right verb from the nature of the work — do not force "developed" or any single verb
5. Use numbers wherever `impact` contains them. Do not invent metrics.
6. For `Responsibilities` sub-sections: generate a brief bullet describing the ongoing operational scope
7. If `impact` is `<!-- not yet captured -->`: generate from `what` and `stack` alone — omit "improved X" framing since you have no number to back it

For roles more than 15 years old: 2–3 bullets maximum, summary-level only, unless the role has specific high-relevance to the target.

### Projects section

Include 2–3 projects maximum. For targeted resumes: pick the most relevant. For general: pick the highest-signal.

Each project: name, type, 1-sentence description, 2 bullets with outcomes.

### Education

Institution, degree, year. One line per entry. No GPA unless exceptional and recent.

### Extensions

If the profile has extensions (publications, patents, open source, speaking): include a brief section if relevant to the target role. Omit if not.

## Phase 4 — Render as ATS-safe HTML

Write a single, self-contained HTML file. This is the step that determines whether a parser can read the resume, so follow it exactly — do not add anything in the name of making it "look nicer."

**Structure:**
- One column. No CSS grid/flexbox columns, no sidebars, no tables for layout.
- Semantic tags only: `<h1>` for name, `<p>` for the contact line, `<h2>` for section headers (Summary, Experience, Skills, Projects, Education), `<h3>` for role/company headers, `<ul><li>` for bullets.
- Reading order in the HTML source must match visual reading order — no absolute positioning, no CSS that reorders content.
- Real text only. No text embedded in images, no icons, no emoji standing in for words.
- No headers/footers, no page-numbering tricks, no decorative borders or background colors/shading.

**Minimal CSS budget** (inline `<style>` in `<head>`, nothing else): font stack, font size, line-height, margins, and heading weight. That's it. Something like:

```html
<style>
  body { font-family: Georgia, 'Times New Roman', serif; font-size: 11pt; line-height: 1.4; color: #000; max-width: 7.5in; margin: 0.5in auto; }
  h1 { font-size: 18pt; margin-bottom: 0.1em; }
  h2 { font-size: 13pt; border-bottom: 1px solid #000; margin-top: 1em; }
  h3 { font-size: 11.5pt; margin-bottom: 0.1em; }
  ul { margin-top: 0.2em; padding-left: 1.2em; }
  @page { margin: 0.6in; }
</style>
```

Adjust font size/margins only as needed to hit the target page count — never by cutting content in a way that omits real experience.

## Phase 5 — Convert to PDF automatically

Detect an available converter and use it — never ask the user to open a browser or convert anything by hand. Check in this order and use the first match:

```bash
detect_pdf_tool() {
  for c in chromium chromium-browser google-chrome google-chrome-stable; do
    command -v "$c" >/dev/null 2>&1 && { echo "$c"; return; }
  done
  for p in "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
           "/Applications/Chromium.app/Contents/MacOS/Chromium"; do
    [ -x "$p" ] && { echo "$p"; return; }
  done
  command -v wkhtmltopdf >/dev/null 2>&1 && { echo "wkhtmltopdf"; return; }
  command -v pandoc >/dev/null 2>&1 && { echo "pandoc"; return; }
  echo ""
}
```

Conversion command depends on which tool was found:
- **Chrome/Chromium** (binary or app path): `"$TOOL" --headless --disable-gpu --no-pdf-header-footer --print-to-pdf="<output>.pdf" "file://$(pwd)/<output>.html"`
- **wkhtmltopdf**: `wkhtmltopdf "<output>.html" "<output>.pdf"`
- **pandoc**: `pandoc "<output>.html" -o "<output>.pdf"`
- **Nothing found**: skip conversion, keep the HTML file, and tell the user plainly which tool would unlock automatic PDF export (e.g., "Install Chrome or run `brew install pandoc` to get an automatic PDF next time — for now, open `resume.html` in any browser and use Print → Save as PDF"). This is a fallback for a machine with no converter, not the default path.

After conversion, confirm the PDF actually has a real text layer, not a raster image — spot-check by noting the file was produced via a tool that renders live HTML/text (all of the above do); no separate verification step needed beyond confirming the command exited successfully and the file exists with nonzero size.

## Output

- **No JD (general-purpose):** write `resume.html` and `resume.pdf` to the profile root, overwriting any existing versions. These are the canonical, always-current resume files.
- **JD provided (tailored):** write to a job-specific filename in the current working directory (e.g. `resume-<company-or-role-slug>.html` / `.pdf`) — do not overwrite the canonical `resume.html`/`resume.pdf`, and do not commit these to the profile repo; they're one-off artifacts for a specific application.

Print a short summary:

```
✓ Generated

  resume.html  → [path]
  resume.pdf   → [path]  (via [tool used])

ATS notes: [any concerns, or "none identified"]

To regenerate with a different target: /generate [new jd]
```

## Hard invariants

1. **Never invent.** Every bullet, metric, and claim must come from the profile. If the profile is thin on a role, the resume will be thin on it too. The fix is `/excavate`, not fabrication.
2. **Use actual titles and company names.** Do not paraphrase, improve, or modernize them.
3. **Do not include skills the candidate doesn't have** even if the JD asks for them.
4. **Read the full experience files, not just summaries.** The depth is there for a reason.
5. **ATS-safety overrides visual preference.** Single column, semantic HTML, minimal CSS, real text — no exceptions for a "nicer-looking" layout.
6. **Writes only resume output files** (`resume.html`/`resume.pdf` or their job-specific variants) — never touches `RESUME.md`, `intent.md`, `skills.md`, or any `experience/`/`projects/` file.
