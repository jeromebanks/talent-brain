# Talent Brain

This is the Talent Brain plugin repository.

## On first response in every new conversation

Check whether `RESUME.md` exists in the current directory.

- If it does **not** exist: before doing anything else, tell the user exactly this:

  > Welcome to Talent Brain. Run `/talent-brain:init` to create your career profile — it takes about 2 minutes.

- If it **does** exist: this is a profile folder that happens to have the plugin checked out nearby. Orient normally.

## For contributors and maintainers

Skills are in `skills/<name>/SKILL.md`. Schema is in `SCHEMA.md`. Profile templates are in `templates/`.
