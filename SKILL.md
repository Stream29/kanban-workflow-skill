---
name: kanban-workflow
description: Use when tracking work in project task records or kanban boards.
---

# Kanban Workflow

- Use the project's existing task locations.
- When the kanban is absent and user wants to track with kanban, create temporary kanban boards. e.g. `/tmp/kanban-<topic>`

## User Drafts

Usually, it is `kanban/Draft.md`.

- Edit the user's draft only when explicitly asked. Promoting an identified item permits creating its discussion task and removing only that item.
- Follow existing writing-boundary conventions. By default, a standalone `---` marks a new complete task prompt.

## Task Phases

```text
kanban/
├── discussion/
├── planning/
├── executable/
└── done/
```

## Task Documents

- Each task is a Markdown file named `YYYY-MM-DD-title.md`, with a stable filename unique across phases.
- Express `Task Tree` as Kotlin script pseudocode in nested Markdown lists, with top-level logic and descriptive backtick-quoted action names.

```markdown
# Task Tree

- `Review task requirements and relevant code`()
- **`Implement the requested behavior`()**
- `Run validation checks for the change`()

# Details

Task context, state needed to resume, inline checks, and subtask links.
```

## Execution

- Bold the current executing or suspended statement in each active invocation or coroutine; concurrent branches may have multiple bold positions.
- Use `kotlinx.coroutines` primitives for structured-concurrency semantics.
- A task completes when its script finishes successfully and its scoped child coroutines have finished.

## Subtask Calls

- Keep each subtask in a standalone task document; express it as a function call and link the call name to its document in `Details`.
