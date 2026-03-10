# Context Persistence Skill

A skill for preserving conversation context across Claude Code sessions.

---

## Core Principle

**"Save context before you lose it; load context before you need it."**

This skill enables explicit context management - complementing (not replacing) Claude's built-in auto-memory with inspectable, version-controlled persistence.

| Concern | Built-in Memory | This Skill |
|---------|-----------------|------------|
| Visibility | Opaque | Explicit files |
| Control | Automatic | Manual trigger |
| Versioning | None | Git-tracked |
| Searchability | Limited | Topic tags, file index |

---

## When to Use

### Save Context When:
- Approaching context window limits (~20% remaining)
- Making important architectural decisions
- Completing a major feature/milestone
- Switching topics significantly
- User explicitly requests "save context" or "remember this"
- Session ending with work in progress

### Load Context When:
- Starting a new session on existing project
- User mentions "where were we" or "continuing from..."
- Topic matches existing context file tags
- Referencing files that appear in previous context

---

## Quick Reference

### Save Context
1. Create file: `.claude/context/active/session-YYYY-MM-DD-topic.md`
2. Use template below
3. Update `.claude/context/_index.md`

### Load Context
1. Read `.claude/context/_index.md`
2. Match by topic tags or file references
3. Read matched context file(s)
4. Follow continuation instructions

---

## Directory Structure

```
.claude/
├── context/
│   ├── _index.md              # Registry of all context files
│   ├── active/                # Recent context (0-7 days)
│   │   └── session-YYYY-MM-DD-topic.md
│   └── archive/               # Older context (7-30 days)
│       └── session-YYYY-MM-DD-topic.md
└── rules/
    └── context-loading.md     # Auto-load behavior
```

---

## Context File Template

Use this structure when saving context:

```markdown
---
session_id: session-YYYY-MM-DD-topic
created: YYYY-MM-DDTHH:MM:SS
topic_tags: [tag1, tag2, tag3]
files_modified: [file1.py, file2.tsx]
continuation_priority: high|medium|low
---

# Session Context: [Descriptive Title]

## Summary
[1-2 paragraphs: What was accomplished, what's the current state, what's next]

## Key Decisions Made

| # | Decision | Rationale | Files Affected |
|---|----------|-----------|----------------|
| 1 | [What was decided] | [Why this choice] | [file1.py, file2.py] |
| 2 | [What was decided] | [Why this choice] | [file3.tsx] |

## Current State

### Completed
- [x] Item that was finished
- [x] Another completed item

### In Progress
- [ ] Item being worked on
- [ ] Another in-progress item

### Blocked / Needs Attention
- Issue that needs resolution
- Dependency that's missing

## Important Code Patterns

[Only include if there are non-obvious patterns worth remembering]

` ` `python
# Example: Pattern name and purpose
def example_function():
    # Why this pattern matters
    pass
` ` `

## Files to Review First

When resuming, read these files in order:
1. `/path/to/primary/file.py` - Core logic
2. `/path/to/related/file.tsx` - UI component
3. `/path/to/config.md` - Relevant configuration

## Continuation Instructions

When resuming this work:
1. [First thing to do or check]
2. [Second priority action]
3. [Any skill files to reference]

## Related Context Files
- `session-YYYY-MM-DD-related.md` - [relationship]
```

---

## Index File Format

The index at `.claude/context/_index.md` follows this structure:

```markdown
# Context Index

Last updated: YYYY-MM-DDTHH:MM:SS

## Active Context Files

| File | Topic Tags | Priority | Created | Summary |
|------|------------|----------|---------|---------|
| `active/session-2026-03-09-feature.md` | feature, api, backend | high | 2026-03-09 | API implementation |

## Archived Context Files

| File | Topic Tags | Created | Summary |
|------|------------|---------|---------|
| `archive/session-2026-02-28-setup.md` | setup, config | 2026-02-28 | Initial project setup |

## Quick Search

### By Topic
- **api**: session-2026-03-09-feature.md
- **setup**: session-2026-02-28-setup.md

### By File Modified
- `api.py`: session-2026-03-09-feature.md
```

---

## Size Management

### Limits

| Constraint | Limit | Action When Exceeded |
|------------|-------|----------------------|
| Single context file | 500 lines / 20KB | Split into multiple files |
| Total active context | 5 files | Archive oldest low-priority |
| Index entries | 50 active | Archive entries >30 days |

### Split Strategy

When a session exceeds 500 lines:

**Option 1 - Split by topic (preferred):**
- `session-2026-03-09-feature-backend.md`
- `session-2026-03-09-feature-frontend.md`

**Option 2 - Split chronologically:**
- `session-2026-03-09-feature-part1.md`
- `session-2026-03-09-feature-part2.md`

Link related files in "Related Context Files" section.

### Content Priority (What to Keep vs Trim)

| Priority | Content | Action |
|----------|---------|--------|
| Critical | Key decisions table | Always keep |
| Critical | Continuation instructions | Always keep |
| Critical | Files to review | Always keep |
| High | In-progress items | Keep if actionable |
| Medium | Code patterns | Keep if unique/non-obvious |
| Low | Completed items | Summarize, trim details |

---

## Archival Policy

| Age | Location | Action |
|-----|----------|--------|
| 0-7 days | `active/` | Keep |
| 7-30 days | `archive/` | Move, keep in index |
| >30 days | Delete | Unless `priority: high` |

### Archival Checklist

Before archiving:
1. Check for unresolved in-progress items
2. If unresolved: keep in active OR create new file with just those items
3. Update `_index.md` to reflect new location
4. Update Quick Search sections

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Context Dumping** | Saving everything makes files unreadable | Focus on decisions, patterns, next steps |
| **Missing Continuation** | Next session starts from scratch | Always include continuation instructions |
| **No Topic Tags** | Hard to find relevant context later | Tag with all relevant topics |
| **Stale Context** | Outdated info misleads future sessions | Follow archival policy, check dates |
| **Monolithic File** | Exceeds size limits, slow to load | Split at 500 lines by topic |
| **External Links** | Links break, content unavailable | Embed all necessary content |

---

## Trigger Detection

### Signs Context is Running Low
- Claude mentions summarizing or losing detail
- Responses become less specific about earlier discussion
- User notices repetition or forgotten context
- Long-running session (many tool calls)

### Proactive Save Triggers
- Before starting a new major topic
- After completing a significant milestone
- Before switching to a different feature/area
- When user will be away (end of day, meeting)

---

## Integration Commands

### /save-context
When user says "save context" or similar:
1. Gather key decisions from conversation
2. Identify files modified
3. Note current state and next steps
4. Create context file using template
5. Update index

### /load-context [topic]
When user wants to resume previous work:
1. Read index file
2. Find matches by topic tag
3. Load most relevant context file
4. Summarize continuation instructions to user

### /cleanup-context
Periodic maintenance:
1. List all context files with age and priority
2. Recommend archival/deletion candidates
3. Confirm with user before acting
4. Update index

---

## Composition Checklist

### When Saving Context:
- [ ] Summary captures current state accurately
- [ ] Key decisions have rationale (not just what, but why)
- [ ] Topic tags cover all relevant areas
- [ ] Files to review are in priority order
- [ ] Continuation instructions are actionable
- [ ] No external links (content embedded)
- [ ] Under 500 lines

### When Loading Context:
- [ ] Checked index for matching topics
- [ ] Read most recent/relevant file
- [ ] Verified context still applicable (check dates)
- [ ] Summarized continuation to user

---

## Key Metrics

| Metric | Target |
|--------|--------|
| Context file size | <500 lines |
| Active context files | <5 files |
| Index entries | <50 active |
| Topic tags per file | 2-5 tags |
| Archival threshold | 7 days |
| Deletion threshold | 30 days |
