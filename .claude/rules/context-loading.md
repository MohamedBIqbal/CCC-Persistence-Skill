# Context Loading Rules

## On Session Start

If user's first message mentions:
- Continuing previous work ("where were we", "let's continue")
- A specific feature or topic from past sessions
- Files that appear in context index

Then:
1. Read `.claude/context/_index.md`
2. Find matching context files by topic tag or file reference
3. Read the most relevant context file
4. Summarize continuation instructions to user

## On Topic Detection

If conversation shifts to a topic with existing context:
1. Check index for matching topic tags
2. Mention available context: "I see we have context from [date] about [topic]. Want me to load it?"

## Context Selection Priority

When multiple files match:
1. Higher priority (high > medium > low)
2. More recent (within same priority)
3. More file overlaps (same source files referenced)

## Do Not Load Context When:
- User explicitly starts fresh ("new approach", "forget previous")
- Context is older than 30 days without high priority
- Topic clearly unrelated to any existing context
