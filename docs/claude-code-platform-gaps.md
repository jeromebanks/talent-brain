# Claude Code Platform Gaps — Observations from Talent Brain

Discovered while building Talent Brain (June 2026). Recorded as a reference for future Claude Code app development.

## The core problem

Claude Code has a plugin *format* but not a plugin *runtime*. There is no auto-install, no portable entry point, and no concept of "run this app from anywhere." Every onboarding flow has to work around these absences manually.

The analogy is npm before `package.json` existed.

## Specific gaps

**1. No auto-install from project `settings.json`**
If `.claude/settings.json` declares a plugin, Claude Code does not prompt the user to install it. The declaration is silently ignored unless the plugin was already installed globally. This breaks the "clone repo, open, everything works" flow.

**2. Two skill locations, different semantics**
- `skills/<name>/SKILL.md` — plugin format, only loads when plugin is installed globally via marketplace
- `.claude/skills/<name>/SKILL.md` — auto-loads whenever the directory is opened, no install needed

Developers need to maintain both (or symlink them) if they want skills to work in both the marketplace-install path and the clone-and-open path.

**3. No portable entry point**
There is no way to ship a Claude Code app that users run from anywhere (e.g., `talent-brain init`). The user must already be in a directory that has the right `.claude/skills/` or plugin installed. This makes onboarding inherently high-friction.

## Workaround we built in Talent Brain

- `skills/<name>/SKILL.md` — canonical source of truth (plugin format)
- `.claude/skills/<name>/` — symlinks to `../../skills/<name>` so skills auto-load when the repo is opened
- `init` skill copies skills into the generated profile folder so profiles are self-contained
- Profile `.claude/settings.json` references the GitHub plugin as a fallback for marketplace users

This works but is brittle infrastructure that the platform should handle.

## What to file with Anthropic

Auto-install from project `settings.json` is the missing primitive. If opening a directory with a plugin declaration triggered a one-time "trust and install?" prompt, most of this complexity disappears.

## Related future work

A `new-app` meta-skill (separate repo) that scaffolds new Claude Code apps with the correct two-location skill structure from the start, so future developers don't rediscover this. Not part of Talent Brain.
