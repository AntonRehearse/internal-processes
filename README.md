# Internal Processes

Company-wide process documentation and reusable skill definitions, kept in sync with the working copies used locally (`~/.claude/skills/`).

## Structure

| Folder | Purpose |
|--------|---------|
| `voice-agents/` | Voice agent prompt-builder skill (mirror copy) + changelog of learnings from testing passes |

## How this stays in sync

The skill files here are **mirror copies** of the working versions in `~/.claude/skills/`. The working copy is what Claude Code actually uses; this repo exists for company-wide version history and sharing. After any edit to a skill that's mirrored here, copy the updated file into this repo and commit, with a changelog entry explaining the root cause and fix.
