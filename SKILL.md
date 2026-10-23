---
name: kanban-workflow
description: Use with multi-step work.
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
├── active/
└── done/
```

- Follow [`programmatic-planning`](../programmatic-planning/SKILL.md#subtasks) for subtasks.
- Record the user's initial intent for a task as discussion.
- If the user confirms the task, move it to `planning/` and go to fill the details with the user, updating the task and plan according to the user's intent, record the user's decisions.
- If a task is fully planned with no blocking prerequisites, move it to `executable/`.
- To execute a task, move it to `active/` and protect it with `flock`.
- If a task is requiring some resources, create a subtask to present the resource and also protect it with `flock`.
- A fully completed task should be moved to `done/`.

## Task Documents

- Each task is a Markdown file named `YYYY-MM-DD-title.md`, with a stable filename unique across phases.
- Express `Task Script` as Kotlin script pseudocode in nested Markdown lists, with top-level logic and descriptive backtick-quoted action names.
- Follow `programmatic-planning` for detailed execution tracking, control flow, and concurrency.

```markdown
# Task Script

- `Review task requirements and relevant code`()
- **`Implement the requested behavior`()**
- `Run validation checks for the change`()

# Details

Task context, state needed to resume, inline checks, and subtask links.
```
