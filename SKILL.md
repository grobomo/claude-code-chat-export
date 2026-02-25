---
name: chat-export
description: Export Claude Code conversations to styled HTML with search, landing page, and raw text export
keywords: [export, conversation, chat, session, html, history, transcript, archive, backup]
invocation: /chat-export
arguments: Optional JSONL path or session name. Auto-detects current session if omitted.
---

# Chat Export Skill

Export Claude Code JSONL conversations to polished terminal-styled HTML pages with full-text search, expandable tool calls, screenshot galleries, and raw text export.

## Usage

```
/chat-export                          # Export current session
/chat-export --landing                # Regenerate landing page only
/chat-export path/to/session.jsonl    # Export specific JSONL file
/chat-export --all                    # Export all sessions for current project
```

## What It Does

1. Parses Claude Code JSONL conversation files (two-pass: collect tool results, then build turns)
2. Generates self-contained HTML with:
   - Terminal dark theme (Cascadia Code, #0C0C0C background)
   - Golden scarab beetle logo in title bar
   - 2-row sticky header: logo + title + Export TXT button / project path + centered search
   - Expandable tool calls with input/output details
   - NPP-style Find All search with hit highlighting
   - Resizable search results panel
   - Screenshot gallery (if screenshot dir exists)
   - Export Raw TXT button for plain text download
   - Clickable project path to open in file explorer
3. Updates manifest.json with export metadata
4. Regenerates landing page (index.html) with search across all exports

## Output Structure

```
~/Downloads/claude-exports/
  index.html              # Landing page - all exports searchable
  manifest.json           # Export metadata
  moltbot/
    ddei-session.html     # Individual export
  chat-export/
    skill-dev.html
```

## Auto-Detection

- **Session**: Current active JSONL from ~/.claude/projects/
- **Project name**: Derived from JSONL path (last directory component)
- **Branch**: From `git rev-parse --abbrev-ref HEAD`
- **Session name**: First user message (truncated to 60 chars)
- **Project path**: Working directory shortened to ~/relative

## How Claude Should Use This Skill

When the user says "export this chat", "save this conversation", "export session to html":

1. Run `python3 {base_dir}/export.py` with appropriate args (base_dir is provided by Claude Code when the skill loads)
2. Report the output path and file size
3. Open the exported HTML in the default browser
