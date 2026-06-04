# save-screenshot — Claude Code skill

A [Claude Code](https://claude.ai/code) skill that persists `mcp__Claude_in_Chrome__computer` screenshots to real image files on disk.

## The problem

`mcp__Claude_in_Chrome__computer` (action `screenshot` / `zoom`) returns images as inline base64. The Claude Code CLI harness renders them visually but writes no file to disk — even with `save_to_disk: true`. The bytes *are* stored in the session's JSONL transcript (`~/.claude/projects/<slug>/<session>.jsonl`). This skill finds that transcript and decodes the image to a file.

## Installation

Copy the `save-screenshot/` directory into your project's `.claude/skills/` folder:

```bash
cp -r save-screenshot /your-project/.claude/skills/
```

Claude Code picks up skills automatically from `.claude/skills/` — no registration step needed.

## Usage

Take a screenshot first, then save it:

```
# Save most recent screenshot to default location (screenshots/<timestamp>.jpg)
/save-screenshot

# Save to a specific path
/save-screenshot img/before.jpg

# Recover a screenshot from a past session
/save-screenshot --session <uuid> --index last --out img/old.jpg
```

The skill resolves the project transcript directory automatically via `git rev-parse --show-toplevel`, so it works in any git repo without configuration.

## How it works

1. Reads the screenshot ID (`ss_xxxxxx`) from the most recent `mcp__Claude_in_Chrome__computer` result in context
2. Greps the project's Claude transcript directory for that ID to locate the right session JSONL
3. Runs `save-screenshot/scripts/save-screenshot.sh --session <id> --index last --out <path>`, which uses Python to parse the JSONL, deduplicate image blocks by MD5, and decode the base64 to a binary file

## Requirements

- Claude Code with the [Claude in Chrome](https://claude.ai/download) MCP connected
- Python 3 (for JSONL parsing)
- `base64` CLI (standard on macOS/Linux)
- A git repository (for automatic slug derivation; pass `--project-slug` to override)

## License

MIT
