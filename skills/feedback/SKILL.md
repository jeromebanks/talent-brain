---
name: feedback
description: File feedback or a bug report about the Talent Brain plugin. Creates a GitHub issue on the talent-brain repo. Use when something didn't work as expected, a skill gave a wrong result, or you have a suggestion for improvement.
---

# Talent Brain — Feedback

You are collecting feedback about the Talent Brain plugin and filing it as a GitHub issue. Make this as low-friction as possible — one command, a short conversation, done.

## Collect feedback

Ask in a single message:

> "What happened? A few sentences is fine — what were you trying to do, what went wrong or fell short, and which skill were you using (if relevant)?
>
> If you'd rather just type it out quickly, go ahead — I'll clean it up and file it."

Let them be as brief or detailed as they want. Don't ask follow-up questions unless something critical is missing (like: you have no idea what skill they were using or what the problem was).

## Categorize

Based on what they said, determine the issue type:
- **bug** — something broke or gave a wrong result
- **improvement** — it worked but could be better
- **question** — unclear how something works
- **schema** — the profile schema needs a new field or extension
- **new skill** — request for a skill that doesn't exist

## File the issue

Use `gh issue create` against the talent-brain repo:

```
gh issue create \
  --repo jeromebanks/talent-brain \
  --title "[type] [short description]" \
  --body "[body]" \
  --label "[bug|enhancement|question]"
```

**Title format:**
- Bug: `[bug] ingest fails to parse LinkedIn ZIP with nested folders`
- Improvement: `[improvement] excavate should ask about team size earlier`
- Suggestion: `[suggestion] add support for publications extension`

**Body format:**
```
## What happened
[what they described]

## Skill
[which skill, or "general / not skill-specific"]

## Steps to reproduce (if bug)
[if they provided them, or "not provided"]

## Expected behavior
[if clear from context]

---
*Filed via /talent-brain:feedback*
```

If `gh` is not available or not authenticated, print the issue content and the manual URL:
```
gh not available. File this manually at:
https://github.com/jeromebanks/talent-brain/issues/new

Title: [title]

Body:
[body]
```

## Confirm

After filing, print:
```
✓ Filed: [issue URL]
Thanks — this helps improve the plugin.
```

## Hard invariants

1. **One question, then file** — don't interrogate them. If you have enough to write a useful issue, write it.
2. **Clean up but don't distort** — fix grammar and format their words into the issue template, but don't change what they said.
3. **Always show the issue title and URL** after filing so they can follow it.
