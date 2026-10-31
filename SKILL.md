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

- Record the user's initial intent for a task as discussion.
- If the user confirms the task, move it to `planning/` and go to fill the details with the user, updating the task and plan according to the user's intent, record the user's decisions.
- If a task is fully planned and clearly has no blockers, move it to `executable/`, even if other tasks it depends on are unfinished.
- To execute a task, move it to `active/` and protect it with `flock`. Parent agent should wait for its subtask `flock`s' completion except for explicitly asked not.
- If a task is requiring some resources, create a subtask to present the resource and also protect it with `flock`.
- A fully completed task should be moved to `done/`.

## Task Documents

- Each task is a Markdown file named `YYYY-MM-DD-title.md`, with a stable filename unique across phases.
- Follow [`programmatic-planning`](../programmatic-planning/SKILL.md) for task scripts, subtasks, and execution tracking.

````markdown
# Task Script

```kotlin
try {
    for (module in modules) {
        if (module.requiresTests) {
            `Compile and test module`(module)
        }
    }
} catch (e: CompileOrTestFailed) {
    val response = `Report failure and wait for a decision`(e)
    `Replan based on the decision`(response)
} finally {
    `Clean up task resources`()
}
```

# Task State

Task context, state needed to resume, pending user colaboration documents, and recorded memory.
````

