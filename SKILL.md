---
name: kanban-workflow
description: Use with multi-step work.
---

# Kanban Workflow

Reuse existing project task locations, or create `/tmp/kanban-<topic>` when absent.

```kotlin
@file:Import("../agent-script/SKILL.md")

enum class Phase(val dir: String) {
    Discussion("kanban/discussion/"),
    Planning("kanban/planning/"),
    Executable("kanban/executable/"),
    Active("kanban/active/"),
    Done("kanban/done/"),
}

/**
 * Markdown document named `YYYY-MM-DD-title.md` (stable unique filename across phases).
 * Tasks form a tree via [parent] and [subtasks]: decomposed work, subagent delegations,
 * and shared resource locks are standalone child [Task] documents imported by [script].
 */
data class Task(
    val fileName: String, // YYYY-MM-DD-title.md
    var phase: Phase,
    val script: TaskScript, // # Task Script (fenced kotlin block written in AgentScript)
    val state: TaskState,   // # Task State (context, resume state, collaboration docs, memory)
    val parent: Task? = null,
    val subtasks: List<Task> = emptyList(),
    val dependencies: List<Task> = emptyList(),
)

/** Edit kanban/Draft.md (standalone `---` marks a prompt boundary) only when explicitly asked. */
fun promoteDraftItem(draftFile: Path = Path("kanban/Draft.md"), item: DraftItem): Task {
    val task = `Create Task document in`(Phase.Discussion, item.initialIntent)
    `Remove only promoted item from`(draftFile, item)
    return task
}

fun runTaskLifecycle(task: Task) {
    `Wait for user to confirm`(task)
    task.moveTo(Phase.Planning)
    while (!task.isFullyPlanned || task.hasBlockers) {
        `Fill details, update script/subtasks with user intent, and record decisions`(task)
    }

    // Eligible for Executable even if tasks in [task.dependencies] are unfinished.
    task.moveTo(Phase.Executable)
    runExecutableTask(task)
}

/** Entry point for both top-level execution and subagents picking up a prepared subtask. */
fun runExecutableTask(task: Task) {
    `Wait until all`(task.dependencies).`reach`(Phase.Done)

    task.moveTo(Phase.Active)
    // Wrap the command or a single lifetime holder; do not spawn repeated sleep locks per step.
    task.withFlock {
        // Child [task.subtasks] (including resource & subagent tasks) runExecutableTask and hold their own flocks.
        // Advance /*PC*/ as each step finishes.
        `Execute task.script and wait for child task.subtasks unless asked not to`(task)
    }
    task.moveTo(Phase.Done)
}
```
