# CCC-Persistence-Skill

**Claude Code Context Persistence Skill** — Explicit, inspectable, git-trackable context persistence for Claude Code sessions.

> "Save context before you lose it; load context before you need it."

## Why This Exists

Claude Code has built-in memory, but it's opaque. This skill gives you **visible, git-tracked, searchable** session context you control.

**Save cost** — No re-exploring codebases or re-reading files. Resume with focused context, fewer tokens spent rediscovering what you already knew.

**Faster sessions** — Pre-prioritized files to review, documented decisions, clear next steps. Skip the "where were we?" overhead.

**No context loss** — Save proactively before hitting limits. Key decisions, active plans, and patterns survive across sessions.

| Concern | Built-in Memory | This Skill |
|---------|-----------------|------------|
| Visibility | Opaque | Explicit files |
| Control | Automatic | Manual trigger |
| Versioning | None | Git-tracked |
| Searchability | Limited | Topic tags, file index |

## What's New in v2

- **Active Plans preservation** — `.claude/plans/*.md` files are ephemeral (Claude Code wipes them between sessions). The skill now captures plan content into context files so plans survive session restarts.
- **Proper SKILL.md format** — Follows Anthropic's official skill conventions with YAML frontmatter, in `context/SKILL.md`.
- **Content Priority framework** — Clear hierarchy for what to keep vs trim when context files grow large.
- **Trigger Detection** — Recognizes signs that context is running low and prompts proactive saves.
- **Composition Checklist** — Step-by-step verification when saving or loading context.
- **Key Metrics** — Target limits for file size, active files, and index entries.

## Quick Start

### Option A: Copy the Skill Directory (Recommended)

```bash
# Copy the skill into your project's .claude/skills/
cp -r context/ /your-project/.claude/skills/context/

# Copy the context infrastructure
cp -r .claude/context/ /your-project/.claude/context/
cp -r .claude/rules/ /your-project/.claude/rules/
```

### Option B: Use the Standalone File

```bash
# Copy just the skill documentation
cp contextskill.md /your-project/

# Set up the directory structure
mkdir -p /your-project/.claude/context/{active,archive}
cp .claude/context/_index.md /your-project/.claude/context/
cp -r .claude/rules/ /your-project/.claude/rules/
```

### 2. Use It

**Save context** when:
- Approaching context window limits
- Making important architectural decisions
- Session ending with work in progress
- User says "save context"

**Load context** when:
- Starting a new session on existing project
- User mentions "where were we" or "continuing from..."
- Topic matches existing context file tags

## Directory Structure

```
your-project/
├── .claude/
│   ├── skills/
│   │   └── context/
│   │       └── SKILL.md           # Skill with YAML frontmatter (recommended)
│   ├── context/
│   │   ├── _index.md              # Registry of all context files
│   │   ├── active/                # Recent context (0-7 days)
│   │   │   └── session-YYYY-MM-DD-topic.md
│   │   └── archive/               # Older context (7-30 days)
│   │       └── session-YYYY-MM-DD-topic.md
│   └── rules/
│       └── context-loading.md     # Auto-load behavior rules
└── contextskill.md                # Standalone version (alternative)
```

## Key Features

- **Active Plans Preservation**: Captures `.claude/plans/*.md` content before session end (plans are ephemeral)
- **Topic Tags**: Every context file has searchable tags
- **Priority Levels**: High/medium/low for continuation decisions
- **Quick Search**: Index supports search by topic or modified files
- **Size Management**: Guidelines for splitting large context files
- **Archival Policy**: Clear rules for when to archive/delete
- **Composition Checklist**: Verification steps for save/load quality

## Cross-File Context Search

The index (`_index.md`) enables searching across all context files and combining them.

### Example: Multi-Topic Resume

You have context files from previous sessions:
- `auth-refactor.md` (tags: auth, security)
- `api-v2.md` (tags: api, auth)
- `database-migration.md` (tags: database, schema)

**Just tell Claude:**

```
"Load all context related to auth"
→ Loads auth-refactor.md + api-v2.md (both tagged 'auth')

"I'm working on src/auth.py, what context do we have?"
→ Finds all context files that modified src/auth.py

"Load context for auth and database work"
→ Combines context from auth-refactor.md + database-migration.md
```

## Example Commands

**Saving:**
```
"Save context"
"Save context with tags: auth, api, refactor"
"Save context, this is high priority"
```

**Loading:**
```
"Where were we?"
"Load context for auth"
"What context do we have for src/api.py?"
"Load all high priority context"
"Continue from yesterday's session"
```

**Managing:**
```
"List all context files"
"Cleanup old context"
"Archive context older than 7 days"
```

## Files Included

| File | Purpose |
|------|---------|
| `context/SKILL.md` | Skill with YAML frontmatter (for `.claude/skills/`) |
| `contextskill.md` | Standalone skill documentation (alternative) |
| `.claude/rules/context-loading.md` | Auto-load behavior rules |
| `.claude/context/_index.md` | Example index file |
| `.claude/context/active/session-example.md` | Example context file |

## License

MIT License - See [LICENSE](LICENSE)

## Contributing

Contributions welcome! If you have improvements or additional patterns that work well, please open an issue or PR.
