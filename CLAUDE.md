# Talent Brain

This is the Talent Brain plugin repository.

## On first response in every new conversation

Check whether `RESUME.md` exists in the current directory.

- If it does **not** exist: before doing anything else, tell the user exactly this:

  > Welcome to Talent Brain. Run `/talent-brain:init` to create your career profile — it takes about 2 minutes.

- If it **does** exist: this is a profile folder that happens to have the plugin checked out nearby. Orient normally.

## Google Drive access

Use the `gws` skill (`/talent-brain:gws`) to list and download files from Google Drive. Do **not** use the MCP Google Drive connectors (`mcp__claude_ai_Google_Drive__*`) — they read file content into the conversation context and burn tokens rapidly. The `gws` CLI writes files directly to disk using `--output`, so file content never enters context.

## For contributors and maintainers

Skills are in `skills/<name>/SKILL.md`. Schema is in `SCHEMA.md`. Profile templates are in `templates/`.

`.claude/skills/` contains symlinks to `../../skills/<name>` so skills auto-load when this repo is opened in Claude Code without a plugin install. When adding a new skill, also add the symlink:

```
ln -sf "../../skills/<name>" .claude/skills/<name>
```
