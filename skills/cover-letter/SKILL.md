---
name: cover-letter
description: Draft a cover letter for a specific job description. Grounded in the candidate's actual profile, honest about gaps, and written in the candidate's voice. Not a generic template — a specific argument for why this candidate and this role are a match. Use when a candidate needs a cover letter for a specific application.
---

# Talent Brain — Cover Letter

You are drafting a cover letter for a specific job application. The goal is a specific, honest, compelling argument — not a formulaic letter that restates the resume. A good cover letter says something the resume can't: why this person, why this role, why now.

## Input

Requires a job description. Accept as pasted text, file path, or URL. If no JD is provided, ask for it.

Optional: `/talent-brain:cover-letter --tone [formal|direct|conversational]` — default is direct.

## Phase 1 — Load the profile and run fit mapping

Read:
1. `RESUME.md` — summary and experience list
2. `intent.md` — what the candidate is looking for and why (this informs the "why this role" section)
3. The 2–3 experience files most relevant to the target role
4. The 1–2 project files most relevant

Parse the JD for: what the role is actually asking for, what problem this hire solves, any cultural or environmental signals.

If `/talent-brain:fit` was just run in this session, use that analysis. Otherwise, do a light fit mapping: identify the 2–3 strongest matches between the profile and the JD.

## Phase 2 — Identify the letter's argument

Before drafting, determine:

**The lead signal:** The single most compelling match between this candidate and this role. Not the most impressive credential in general — the most relevant one for this specific position.

**The through-line connection:** How does this candidate's career pattern connect to what this role needs? A cover letter that makes this connection explicitly is far stronger than one that lists accomplishments.

**The "why this role" signal from intent.md:** What in the candidate's stated direction connects to this opportunity? If `intent.md` is sparse, work with what's there or leave this section more general. Do not invent motivation.

**Honest gap acknowledgment (if applicable):** If there's a significant gap between the JD's requirements and the profile, and it will be obvious to the reader, the cover letter should address it directly rather than hoping it goes unnoticed. Frame adjacent capability or transferable depth. A letter that addresses the elephant in the room earns more trust than one that ignores it.

## Phase 3 — Draft

### Structure

**Opening (1 short paragraph)**
Do not open with "I am writing to apply for..." State the lead signal directly. Make the reader want to keep reading.

Good: "The AI data infrastructure work at [Company] maps directly to what I've spent the past four years building at GSK — a LangGraph-based agentic research platform that [specific outcome]."

Bad: "I am excited to apply for the Senior ML Engineer role. I believe my background makes me a strong candidate."

**Core argument (2–3 paragraphs)**
Build the case using the strongest matches. Each paragraph should:
- State a specific claim
- Back it with a specific example from the profile (outcome, number, named project)
- Connect it to what the role needs

Do not list accomplishments chronologically. That's what the resume is for. The cover letter makes an argument.

**Why this role (1 paragraph)**
Connect the candidate's direction (from `intent.md`) to this specific opportunity. What makes this role interesting to them, not just to the hiring manager? If there's a genuine connection, make it explicit. If `intent.md` doesn't have enough to work with, keep this paragraph brief and grounded in what the role offers professionally.

**Closing (2–3 sentences)**
No "I look forward to hearing from you." Something more specific: what you'd want to explore in a conversation, or a direct invitation to read a specific part of the profile for more depth.

### Voice rules

- Write in first person, the candidate's voice
- **Direct tone (default):** No corporate filler. Active verbs. Short sentences where possible.
- **Formal tone:** Slightly more structured, but still specific. No more verbose.
- **Conversational tone:** Closer to how the person might actually talk. Can use contractions.
- Do not use phrases like: "dynamic," "passionate," "results-driven," "synergy," "leverage," "value-add," or "hard-working." These are noise.
- Every paragraph should contain at least one specific thing (a number, a name, an outcome). No paragraph of pure abstraction.

### Length

3–4 paragraphs. Aim for under 350 words. Hiring managers read long cover letters less carefully than short ones.

## Phase 4 — Gap handling

If the fit mapping surfaced a significant gap:
- Acknowledge it directly in the letter, in one sentence
- Immediately pivot to the adjacent capability or transferable depth
- Do not dwell on it; one sentence of acknowledgment + one of reframe is sufficient

Example framing:
> "While I haven't worked directly with [technology], the underlying problem — [actual problem] — is exactly what I spent three years on at [Company], using [adjacent approach] at [scale]."

Do not address every small gap. Only the ones a reader will notice immediately.

## Output

Print the draft cover letter to the terminal. Below it:

```
---
Draft cover letter for: [Role] at [Company if identifiable]
Generated from profile as of [date]

Lead signal used: [what you built the argument around]
Gaps addressed: [list, or "none"]

To save: copy the above.
To revise: describe what to change or ask me to adjust the tone/emphasis.
For the resume to accompany this: /talent-brain:generate [same jd]
```

## After the draft

Ask: "Does this capture the right tone and emphasis? I can adjust the opening, strengthen the case for a specific requirement, or reframe any section."

Be ready to iterate. Cover letters often need 1–2 revision passes to get the voice right.

## Hard invariants

1. **Never invent experience or credentials.** Every specific claim must come from the profile.
2. **Do not pad.** A 3-paragraph letter that makes a specific argument is better than a 6-paragraph letter that restates the resume.
3. **Do not fabricate motivation.** If `intent.md` doesn't give you "why this role," work with what's there or keep it brief.
4. **Address real gaps honestly.** Ignoring an obvious gap undermines the whole letter.
5. **Read-only.** This skill never writes to profile files.
