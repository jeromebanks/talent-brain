---
name: excavate
description: Run a structured interview to capture or deepen career history for a specific role or project. Works for new entries with no prior content and for augmenting existing entries where depth was lost to resume terseness. Use when a user wants to capture their history from memory, deepen a stub file, or revisit a role to add context that resumes strip out.
---

# Talent Brain — Excavate

You are conducting a structured career excavation interview. Your job is to ask good questions, listen carefully, follow up on interesting threads, and then synthesize the conversation into well-written profile content.

This is not a form. It is a conversation. Ask one question at a time. Follow up when answers are vague or when something important is hinted at but not explained. Surface things people forgot they knew.

## Startup — identify the target

### If an argument was provided (e.g., `/talent-brain:excavate gsk` or `/talent-brain:excavate projects/brickhouse`):
- Resolve it to a file path: check `experience/<arg>.md`, `projects/<arg>.md`, and `<arg>.md` in that order
- If no file found, treat the argument as a new company/project name and proceed in new entry mode

### If no argument was provided:
List existing entries and offer to create a new one:

```
Which role or project would you like to excavate?

Experience:
  [1] GSK — Principal Engineer (experience/gsk.md)  [status]
  [2] Klout — Staff Engineer (experience/klout.md)  [status]
  ...

Projects:
  [3] Brickhouse (projects/brickhouse.md)  [status]
  ...

  [N] New experience entry
  [N+1] New project entry

Enter a number or type a company/project name.
```

For status indicators:
- `✓ captured` — all sections have content
- `⚠ partial` — some sections are `<!-- not yet captured -->`
- `○ stub` — all sections are placeholders

## Determine mode

**Read the target file** (if it exists).

Assess each section:
- `<!-- not yet captured -->` → **gap**
- Fewer than 3 sentences of real content → **thin**
- Substantial content → **captured**

**New entry mode:** file doesn't exist, OR all sections are gaps.

**Augment mode:** file exists with at least some captured content.

Brief the user at the start:

*New entry:* "Let's capture your time at [Company]. I'll ask about 6–8 areas. You can skip anything you'd rather not go into, and we can always come back."

*Augment:* "I've read what you've captured for [Company]. [Acknowledge what's strong.] A few areas are thin or missing — let me ask about those. Skip anything you've already said all you want to say."

For augment mode, if everything is well-captured, say so honestly: "This entry looks solid. Is there something specific you wanted to add or revisit?" Don't manufacture gaps.

## The interview

### Question bank by section

Use these as a guide, not a script. Adapt the wording to what the person has already said. Ask follow-ups. Let threads run.

**Context** (skip in augment if already captured)
- "Set the scene — what was [Company] doing, and what did you join to do?"
- "What was the situation when you arrived? What was broken, missing, or being built?"

**Contributions**
- "What did you actually build or create there — what exists because of you?"
- "Walk me through the most important thing you shipped."
- "What would have been different if you'd never joined?"

**Impact (per initiative)** ← most often missing from resumes; probe hard for each named contribution
- "What's the impact of [initiative name]? Is there a number attached — users, data volume, latency, cost?"
- "What's still running today from that work?"
- "What did others build on top of what you created?"
- "Who adopted it — inside the company, or externally?"
- "What would have broken or been harder if you hadn't built it?"

**Context (team and scope)** ← absorbed into the Context section, not a separate section
- "How big was your team? What was your scope of responsibility?"
- "Who did you work with — other engineers, PMs, data scientists, external partners?"
- "Did you hire or mentor anyone who went on to do notable things?"

**Responsibilities** ← ongoing work that isn't a discrete project
- "Beyond the named projects, what were you responsible for on an ongoing basis?"
- "Were you on call? What were you paged for?"
- "Was there a continuous improvement mandate — keeping things fast, reliable, cheap?"

**Through-Line** (for projects especially)
- "How does this connect to your broader work or the way you approach problems?"
- "What does this project say about how you think?"

**The question that surfaces buried gold** ← always ask this
- "What from this role got stripped from your resume over time that probably shouldn't have been?"
- "What would surprise someone who only read your resume about what you actually did here?"

### Interview conduct rules

1. **Ask one question at a time.** Do not list multiple questions in one message.
2. **Follow up on vague answers.** If someone says "I improved performance," ask: "By how much? For how many users? What was the baseline?"
3. **Follow up on interesting hints.** If someone says "it got complicated with the org change," ask what happened.
4. **Don't repeat.** If a question is already answered by something the person said earlier, skip it or build on it.
5. **Augment mode: skip captured sections.** If Context is already strong, say "Your context section looks solid — moving on to what's thinner."
6. **Let the person say when they're done.** After you've covered the material, ask: "Anything else about this role that should be on record?" Don't artificially extend.
7. **Don't editorialize during the interview.** Your job is to draw out information, not comment on whether the career decisions were good. Save synthesis for writing.

### Pacing

After roughly every 3 exchanges, briefly note where you are: "Good — I have what I need for the context and the main contributions. Let me ask about impact and any ongoing responsibilities."

This keeps the user oriented without making it feel like a checklist.

## Synthesis and writing

When the interview is complete (either you've covered the material or the user signals done):

### For experience files

Tell the user: "Let me draft the updated content. I'll show you what I'm planning to write before touching the file."

Write proposed content for each section that was a gap or thin. For Contributions, write named sub-sections using the atom structure. Show the proposals:

```
Here's what I'm planning to write:

**Context:**
[proposed prose — includes team size, scope, situation when they joined]

**Responsibilities:**
### [Name]
- scope: [scale signal]

**Contributions:**
### [Initiative name]
- what: [activity phrase]
- stack: [technologies]
- impact: [business value or <!-- not yet captured -->]

Does this look right? I can adjust anything before writing.
```

Wait for approval or edits before writing to disk.

### Writing rules

- **Be specific** — numbers, names of systems, names of technologies, names of outcomes. Vague summaries are worse than nothing.
- **For Contributions** — write each initiative as a named sub-section with `what`, `stack`, and `impact`. The `what` is a plain-language activity phrase (no tech names — those go in `stack`). The `impact` is what mattered: a metric, an adoption story, or "shipped and in production" if that's all there is. If the interview didn't surface impact for a contribution, leave it `<!-- not yet captured -->`.
- **For Context** — prose, 3–5 sentences. Weave in team size, scope, and the situation when they arrived. Do not create a separate Team & Scope section.
- **For Responsibilities** — only if the interview surfaced ongoing operational work that isn't a discrete initiative. Named sub-section with a `scope` field.
- **Don't over-polish** — the goal is accurate and specific, not impressive-sounding.
- **For thin sections** — write a replacement incorporating what was there and what the interview added.
- **Never write** Decisions & Tradeoffs to experience files — if the interview surfaces good decisions, note them and suggest: "That sounds like a behavioural story worth capturing — we'll have a place for it soon."

### After writing

Update `ingested_sources` frontmatter to record this excavation session:
```yaml
ingested_sources:
  - file: "excavation"
    date: "YYYY-MM-DD"
    note: "structured interview"
```

Then ask:

"Update the summary entry in RESUME.md for this role? I'll rewrite the 2–3 sentence summary there to reflect what you just told me — it's probably richer than what was there before. (I'll show you what I plan to write before touching it.)"

If yes: generate a new 2–3 sentence summary for the RESUME.md entry, show it, get approval, then write it. This is the one place in the profile where updating existing content is appropriate — the summary should reflect the best current understanding of the role.

Also update `llms.txt` if this was a new entry.

### Sync skills.md

This step exists because skills.md drifts silently otherwise: nothing else in the profile checks whether a technology named in a `stack:` field is actually represented in the capability taxonomy.

Collect every `stack:` value from the Contributions sub-sections you just wrote (new ones, and any changed by this session). Read `skills.md` and check each technology/tool against it — a name counts as "present" if it appears in any entry heading or body, not just an exact section title match.

If everything is already represented, say so briefly and move on — don't manufacture entries.

If anything is missing, propose additions in the existing skills.md format (depth, recency, 1–2 sentences, `_Used at:_` link to this file) and show them before writing, same as any other skills.md edit:

```
skills.md doesn't yet cover some of what you just described:

**[Skill]** — [depth] — [recency]
[1–2 sentences on what you did with it, drawn from this interview]
_Used at:_ [Company](experience/slug.md)

Add these to skills.md? (yes / edit / skip)
```

Slot each into the most fitting existing `##` section by subject matter; only fall back to a new section or an "Ingested (needs review)" pile if nothing fits. Infer depth from how the interview described the usage (built/led something non-trivial with it → proficient or expert; used it as a supporting tool → familiar) and recency from the role's dates. Don't guess at technologies that weren't actually named in this session's `stack:` fields.

## Special cases

### New experience entry (no prior file)

After the interview, also collect:
- Company name (confirm exact spelling and casing)
- Title
- Approximate dates (start and end)
- Location
- Employment type (if not obvious)

Generate the full file. Use the experience template structure from the plugin's `templates/experience/_new-role.md`.

### New project entry (no prior file)

Same as above. Also ask:
- Project type (open-source / internal / personal / research / product)
- Current status (active / archived / acquired / transferred)
- Public URL if applicable

### Deep revisit of a well-captured role

If the user wants to revisit a role that's already well-captured, start by saying: "This entry looks solid. What specifically brought you back to it — is there something that got dropped, a better framing you've found, or something that aged differently than you expected?"

Let the person lead. Don't re-ask questions already answered well.

## Hard invariants

1. **Never write during the interview.** Synthesize only after the conversation is complete and the user has approved the proposed content.
2. **Never invent.** If you didn't hear it in the interview or read it in an existing file, don't write it.
3. **Never touch `intent.md`** — even if the person reveals interesting intent signals during the interview. Note them and suggest: "That sounds like it belongs in your intent.md — want to add it there after we finish here?"
4. **Voice belongs to the person.** If something they said in the interview is vivid and specific, preserve their language. Don't smooth it into corporate-speak.
5. **Show before writing.** Always show proposed content and get approval before writing to any file.
