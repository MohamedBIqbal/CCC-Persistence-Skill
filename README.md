# CCC-Persistence-Skill

**Claude Code Context Persistence Skill** - A skill for preserving conversation context across Claude Code sessions.

## Why This Exists

Claude Code has built-in memory, but it's opaque. This skill provides **explicit, inspectable, git-trackable** context persistence that complements the built-in system.

| Concern | Built-in Memory | This Skill |
|---------|-----------------|------------|
| Visibility | Opaque | Explicit files |
| Control | Automatic | Manual trigger |
| Versioning | None | Git-tracked |
| Searchability | Limited | Topic tags, file index |

## Quick Start

### 1. Copy to Your Project

```bash
# Copy the skill file
cp contextskill.md /your-project/

# Copy the rules and directory structure
cp -r .claude /your-project/
```

### 2. Reference in CLAUDE.md

Add to your project's `CLAUDE.md`:

```markdown
## Context Persistence System

**On session start:**
- Check `.claude/context/_index.md` for relevant prior context
- If user mentions continuing work, load matching context file

**When approaching context limits (~20% remaining):**
- Use `contextskill.md` to save important session context
- Focus on decisions, state, and continuation instructions

**Context locations:**
- Index: `.claude/context/_index.md`
- Active: `.claude/context/active/`
- Archive: `.claude/context/archive/`
- Skill: `contextskill.md`
```

### 3. Use It

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
├── contextskill.md              # Main skill documentation
├── CLAUDE.md                    # Reference the skill here
└── .claude/
    ├── context/
    │   ├── _index.md            # Registry of all context files
    │   ├── active/              # Recent context (0-7 days)
    │   │   └── session-YYYY-MM-DD-topic.md
    │   └── archive/             # Older context (7-30 days)
    │       └── session-YYYY-MM-DD-topic.md
    └── rules/
        └── context-loading.md   # Auto-load behavior rules
```

## Key Features

- **Topic Tags**: Every context file has searchable tags
- **Priority Levels**: High/medium/low for continuation decisions
- **Quick Search**: Index supports search by topic or modified files
- **Size Management**: Guidelines for splitting large context files
- **Archival Policy**: Clear rules for when to archive/delete

## Example Commands

When working with Claude Code, you can use natural language:

- "Save context" - Creates a context file for current session
- "Load context for [topic]" - Loads relevant previous context
- "Where were we?" - Triggers context lookup and summary
- "Cleanup context" - Reviews and archives old context files

## Files Included

| File | Purpose |
|------|---------|
| `contextskill.md` | Complete skill documentation with templates |
| `.claude/rules/context-loading.md` | Auto-load behavior rules |
| `.claude/context/_index.md` | Example index file |
| `.claude/context/active/session-example.md` | Example context file |

## License

MIT License - See [LICENSE](LICENSE)

## Contributing

Contributions welcome! This skill emerged from real usage patterns with Claude Code. If you have improvements or additional patterns that work well, please open an issue or PR.
