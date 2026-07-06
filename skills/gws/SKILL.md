---
name: gws
description: If the gws CLI is installed, use it to list and download files from Google Drive without loading file content into the conversation context. Use instead of MCP Google Drive connectors when downloading files locally — connectors read content into context and burn tokens rapidly. Not part of the core Talent Brain workflow — only relevant if the profile owner has gws set up locally.
---

# Google Workspace CLI (gws)

This skill assumes the `gws` CLI is already installed locally — it is personal infrastructure, not something every Talent Brain user will have. If `gws` is not on `PATH`, skip this skill and fall back to the MCP Google Drive connector or ask the user to provide files directly.

Use `gws` to interact with Google Drive from the shell. The critical rule: **always use `--output <path>` when downloading files**. This writes directly to disk and keeps file content out of the conversation context. Never use the MCP Google Drive connector to fetch file content — it reads everything into context and burns tokens.

## Listing files

Search for files by name pattern:

```bash
gws drive files list --params '{"q": "name contains \"resume\""}'
```

Auto-paginate across all results:

```bash
gws drive files list --params '{"q": "name contains \"resume\""}' --page-all
```

The response is JSON. Key fields: `id`, `name`, `mimeType`, `modifiedTime`.

Request only the fields you need to keep output small:

```bash
gws drive files list --params '{"q": "name contains \"resume\"", "fields": "files(id,name,mimeType,modifiedTime)"}' --page-all
```

## Downloading a file to disk

Always use `--output` to write to disk. File content never enters the conversation context.

**Important:** `gws` validates that `--output` is within the current working directory. Always `cd` to the destination directory first, then use a relative filename:

```bash
cd ~/path/to/destination
gws drive files get --params '{"fileId": "FILE_ID", "alt": "media"}' --output "filename.pdf"
```

**Never** call `gws drive files get` without `--output` when the goal is to save a file locally — without it, the response is captured as a tool result and the full file content lands in context.

## Exporting Google Docs / Sheets / Slides

Native Google Workspace files (Docs, Sheets, Slides) cannot be downloaded with `alt=media`. Use export instead:

```bash
# Export a Google Doc as PDF
gws drive files export --params '{"fileId": "FILE_ID", "mimeType": "application/pdf"}' --output ~/path/to/file.pdf

# Export a Google Doc as plain text
gws drive files export --params '{"fileId": "FILE_ID", "mimeType": "text/plain"}' --output ~/path/to/file.txt
```

## Safe pattern for bulk download

1. List files to get IDs and names (cheap — metadata only, nothing large enters context)
2. For each file, run a separate `gws drive files get ... --output <path>` Bash call
3. Confirm the file exists on disk before moving to the next

Never loop over files and read their content into context. Even a few multi-page PDFs will fill context and exhaust token budgets.

## Checking the API schema

```bash
gws schema drive.files.list
gws schema drive.files.get
gws schema drive.files.export
```

## Why not the MCP Google Drive connector?

The MCP tools (`mcp__claude_ai_Google_Drive__read_file_content`, `download_file_content`, etc.) return file content as tool results. Every file fetched is added to the conversation context window permanently. For a handful of resume PDFs this can consume tens of thousands of tokens, exhaust the session budget, and degrade model coherence. Use `gws` with `--output` instead — the shell writes to disk, zero tokens consumed for file content.
