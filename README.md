# Talent Brain

Build a rich, navigable career profile that you and AI agents can both explore.

Instead of a flat resume PDF, Talent Brain creates a structured set of files capturing your real experience in depth — what you built, the decisions you made, what's still running, and where you're heading. Then use Claude to generate targeted resumes, assess your fit for a role, or walk a recruiter through your background interactively.

---

## What you need

**To build your own profile:**
- [Claude Code](https://claude.ai/code) — desktop app or IDE extension (Mac, Windows, Linux)
- Or **Claude Cowork** — browser-based, no install required

**To view or interact with someone else's profile (recruiters, hiring managers):**
- **Claude Cowork** — the profile owner shares a link and you join their session, no setup needed

A GitHub account is helpful for sharing your profile but not required to get started.

---

## Get started

**1. Clone this repo and open it in Claude Code:**

```
git clone https://github.com/jeromebanks/talent-brain
```

Open the `talent-brain` folder in Claude Code. All skills load automatically — no plugin installation needed.

**2. Run init:**

```
/talent-brain:tb-init
```

Claude will ask for your name, title, and location, then create your profile at `~/talent-brain-profile`, initialize git, and optionally push it to GitHub. Takes about 2 minutes.

**3. Open your profile folder:**

Open `~/talent-brain-profile` in Claude Code (or Cowork). All Talent Brain skills are available there — your profile is self-contained.

---

## Fill it in

**From an existing resume:**
```
/talent-brain:ingest resume.pdf
```
Accepts PDFs and LinkedIn data exports (`.zip` from LinkedIn's "Get a copy of your data"). You can run it on multiple files — older resumes often have detail that got trimmed from newer ones, and ingest takes the best from each.

**From memory:**
```
/talent-brain:excavate
```
Claude interviews you about a role: what you built, what the impact was, what decisions you made. About 10 minutes per role. This is where depth that never makes it onto a resume gets captured.

**Capture your intent:**
```
/talent-brain:intent
```
A short conversation about what you actually want next — and what you're not interested in. Claude writes `intent.md` from your answers. This is the most valuable file in the profile and the one no resume can express.

---

## Use it

**Am I a good fit for this role?**
```
/talent-brain:fit
```
Paste a job description when prompted. Returns an honest, evidence-backed assessment.

**What's missing from my profile for this role?**
```
/talent-brain:gap
```
Separates genuine gaps from things that are probably in your history but not captured yet.

**Generate a resume for a specific role:**
```
/talent-brain:generate
```
Paste a job description. Returns a tailored resume drawn from your full profile depth.

**Write a cover letter:**
```
/talent-brain:cover-letter
```
Makes a specific argument for your candidacy — not a generic template.

---

## Share with a recruiter or hiring manager

**Claude Cowork (easiest — no install required on their end):**

Open your profile folder in Claude Code and start a Cowork session. Share the link. The recruiter joins and can ask questions in plain language — Claude answers from your profile. They don't need an account or any setup.

To start the showcase:
```
/talent-brain:showcase
```

Claude opens with a pitch built around your strongest signals, then takes questions.

**Via GitHub:**

If your profile is on GitHub, anyone who clones it and opens the folder in Claude Code gets Talent Brain automatically — the skills are bundled in `.claude/skills/` and load without any plugin install. They run `/talent-brain:showcase` and go.

---

## FAQ

**Do I need to know how to code?**
No. If you can open a folder in Claude Code and follow prompts, you can build a profile.

**Is my profile private?**
By default yes — it lives on your machine. You control whether to push it to GitHub and whether the repo is public or private.

**Can I use this with a private GitHub repo?**
Yes. The profile works the same way whether it's local, a private repo, or a public one.

**What about the `llms.txt` file in my profile?**
That's for developers building hiring tools that need to read profiles programmatically. You don't need to do anything with it.

---

## Contributing

Issues and PRs welcome at [github.com/jeromebanks/talent-brain](https://github.com/jeromebanks/talent-brain).

The schema is intentionally extensible — different industries have different needs (publications, patents, portfolios). If your field needs something not covered, open an issue.

---

MIT License
