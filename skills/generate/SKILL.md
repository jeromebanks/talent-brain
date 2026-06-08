---
name: generate
description: Generate a tailored resume for a specific job description, or a general-purpose resume if no JD is provided. Pulls from the full profile depth and positions the candidate's experience for the target role. Outputs clean Markdown suitable for conversion to PDF or copy-paste into an ATS. Use when a candidate needs a formatted resume document.
---

# Talent Brain — Generate Resume

You are generating a resume document from the candidate's Talent Brain profile. The output is tailored to a specific role if a JD is provided, or optimized as a strong general-purpose resume if not.

## Inputs

- `/talent-brain:generate` — generate a general-purpose resume
- `/talent-brain:generate "senior ML engineer at fintech startup"` — generate tailored to a context description
- `/talent-brain:generate [file or URL]` — generate tailored to a specific job description

If a JD is provided as a file path or URL, read/fetch it first.

## Phase 1 — Load the full profile

Read all of the following before generating anything:
1. `RESUME.md` — index, summary, experience list
2. `intent.md` — direction and preferences (informs the summary and objective)
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

## Phase 3 — Generate the resume

### Format rules

- Output clean Markdown. Use `---` for section breaks, `###` for role headers.
- Target length: 1 page for < 10 years experience, 2 pages for longer careers. Flag if the output is running long.
- No photos, no graphics, no icons — these don't survive ATS parsing.
- Contact line at top: name | location | email | linkedin | github (omit blanks).
- Use the candidate's exact company names and titles from the profile — never paraphrase or invent.
- Dates: `MM/YYYY – MM/YYYY` or `MM/YYYY – Present`. Consistent format throughout.

### Summary / Professional Profile (3–4 sentences)

Lead with the through-line: what this person is, what they're known for, what they're heading toward. If a JD was provided, open with the fit signal. Do not use generic filler ("results-driven professional", "dynamic leader"). Every sentence must contain a specific claim.

For targeted resumes: the summary should make the fit obvious in the first two sentences.

### Skills section

Pull from `skills.md`. For targeted resumes: surface skills that appear in the JD first, in the JD's vocabulary (where that vocabulary is accurate to the candidate's actual experience). Do not list skills the candidate doesn't have just because the JD asks for them.

Group by domain. Keep depth signals (`expert`, `proficient`) if the format allows; drop them if space is tight. Omit `historical` skills unless directly relevant.

### Experience section (reverse chronological)

For each role:
1. Read the full `experience/<slug>.md` — do not rely on the RESUME.md summary alone
2. Write 3–5 bullet points that lead with outcomes, not activities
3. For targeted resumes: surface the bullets most relevant to the JD first; deprioritize or omit bullets that don't speak to the target role
4. Every bullet should follow: **result or outcome** → what you did → at what scale
5. Use numbers wherever they exist in the profile. Do not invent metrics.
6. Use active verbs: built, designed, led, reduced, shipped, established — not "responsible for" or "helped with"

For roles more than 15 years old: 2–3 bullets maximum, summary-level only, unless the role has specific high-relevance to the target.

### Projects section

Include 2–3 projects maximum. For targeted resumes: pick the most relevant. For general: pick the highest-signal.

Each project: name, type, 1-sentence description, 2 bullets with outcomes.

### Education

Institution, degree, year. One line per entry. No GPA unless exceptional and recent.

### Extensions

If the profile has extensions (publications, patents, open source, speaking): include a brief section if relevant to the target role. Omit if not.

## Phase 4 — ATS pass

After generating, review the output for ATS compatibility:
- Are keywords from the JD (where accurate) present in the text?
- Are there any tables, special characters, or formatting that won't survive plain-text parsing?
- Are all dates in consistent parseable format?

Note any ATS concerns at the end of the output.

## Output

Print the full resume in Markdown to the terminal. Then:

```
---
Generated from profile as of [date].

ATS notes: [any concerns, or "none identified"]

To save: copy the above, or redirect to a file.
To convert to PDF: paste into a Markdown editor (Typora, Obsidian, Marked 2) and export,
or use: pandoc resume-output.md -o resume.pdf (requires pandoc + LaTeX)

To regenerate with a different target: /talent-brain:generate [new jd]
```

Do not automatically write files.

## Hard invariants

1. **Never invent.** Every bullet, metric, and claim must come from the profile. If the profile is thin on a role, the resume will be thin on it too. The fix is `/talent-brain:excavate`, not fabrication.
2. **Use actual titles and company names.** Do not paraphrase, improve, or modernize them.
3. **Do not include skills the candidate doesn't have** even if the JD asks for them.
4. **Read the full experience files, not just summaries.** The depth is there for a reason.
5. **Read-only.** This skill never writes to profile files.
