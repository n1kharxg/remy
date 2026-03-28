# remy

Simple PRD for a fast terminal-first todo CLI.

---

## Product

`remy` is a local-first todo CLI written in Go.

We use it to capture, start, finish, list, and remove tasks from the terminal with commands that feel natural to type. The product should feel as fast as Taskbook, but simpler and more readable for everyday use.

---

## Problem

Most todo tools interrupt terminal flow. They ask us to switch contexts, learn a larger UI, or remember command flags that are harder to type than the task itself.

We want a tool that works at thought speed. If the command feels heavier than writing the task in a scratch file, the product has failed.

---

## Goal

We are building the fastest way to manage personal tasks from the terminal.

The first version should make five actions obvious:

| Action                     | Example                      |
| -------------------------- | ---------------------------- |
| Add a task                 | `remy add "write README"`    |
| List tasks                 | `remy list`                  |
| Mark a task as in progress | `remy doing "write README"`  |
| Mark a task as done        | `remy done "write README"`   |
| Remove a task              | `remy remove "write README"` |

Commands that act on an existing task should not force us to manually retype the full task every time. We should support shell autocomplete for task names, and we should leave room for task IDs if duplicate names make text matching unsafe.

---

## User

We are building for developers, makers, and terminal-heavy users who want lightweight task tracking without leaving the shell.

These users care more about speed, clarity, and local control than about team collaboration, calendars, or dashboards.

---

## Product Principles

- We prefer **friendly verbs** over flags when the action is obvious.
- We keep **local JSON storage** because it is easy to inspect, back up, and change.
- We keep the codebase **small and extensible** because the tool should stay easy to hack on.
- We optimize for **single-user CLI speed**, not for enterprise workflows.

---

## Core Experience

The command flow should look like this:

```bash
remy add "write README"
remy add "ship first release"
remy list
remy doing "write README"
remy done "write README"
remy remove "ship first release"
```

We should be able to understand the state change from the verb alone. We should not need to remember short flags for common actions.

For follow-up commands, we should be able to press tab and complete an existing task name instead of typing the same string again by hand.

---

## Functional Requirements

| Requirement                    | Why it exists                                                                |
| ------------------------------ | ---------------------------------------------------------------------------- |
| Add a task from one command    | Capture has to be the fastest path                                           |
| List current tasks             | We need one obvious default view                                             |
| Track task state               | We need to distinguish backlog, active work, and completed work              |
| Persist tasks locally          | Data should survive shell sessions without external services                 |
| Remove tasks explicitly        | Cleanup should be simple and intentional                                     |
| Resolve existing tasks quickly | State-changing commands should work well with autocomplete and safe matching |

---

## Command Model

We are currently aiming for this state model:

| State   | Meaning                             |
| ------- | ----------------------------------- |
| `todo`  | The task exists but has not started |
| `doing` | The task is actively in progress    |
| `done`  | The task is complete                |

We can store the state internally however we want, but the CLI should expose verbs that map cleanly to these transitions.

---

## Open Decisions

| Decision                                 | Current thinking                                                                   |
| ---------------------------------------- | ---------------------------------------------------------------------------------- |
| Match tasks by text or ID                | Start with text plus shell autocomplete, then add IDs if duplicates become painful |
| Allow multiple `doing` tasks or only one | Start permissive unless that creates confusing output                              |
| Delete permanently or archive            | Start with permanent delete to keep the model simple                               |
| Default list output                      | Show active and pending tasks first, keep done tasks readable but secondary        |

---

## Non-Goals

- We are not building team collaboration.
- We are not building cloud sync in v1.
- We are not building projects, boards, or nested task trees in v1.
- We are not adding priorities, due dates, or tags unless they preserve CLI speed.

---

## Milestones

### v0: Core behavior

- [ ] Define task model
- [ ] Define command grammar
- [ ] Implement JSON storage
- [ ] Implement `remy add`
- [ ] Implement `remy list`
- [ ] Implement `remy doing`
- [ ] Implement `remy done`
- [ ] Implement `remy remove`

### v1: Usability

- [ ] Improve terminal output
- [ ] Handle duplicate task names safely
- [ ] Add shell autocomplete for existing task names
- [ ] Add tests for storage
- [ ] Add tests for command behavior
- [ ] Add a compact help screen

### After v1

- [ ] Add filters like `remy list doing`
- [ ] Support editing a task
- [ ] Decide whether task IDs should become first-class
- [ ] Add optional metadata only if the CLI stays fast

### Feature Pool

These are the larger features we can evaluate after the core CLI is stable. They are inspired by the broader Taskbook feature set, but we should only take the parts that fit `remy`'s smaller and friendlier command model.

| Feature                 | Why we may want it                                                       |
| ----------------------- | ------------------------------------------------------------------------ |
| Search and filtering    | Useful once the task list grows beyond a short daily queue               |
| Archived task recovery  | Helps if permanent delete becomes too risky in real use                  |
| Progress summary        | Gives us more signal in `list` without turning the tool into a dashboard |
| Custom storage path     | Makes local data easier to sync, back up, or inspect                     |
| Config file             | Gives us a place for preferences without bloating command syntax         |
| Notes attached to tasks | Helps when a task title alone is not enough context                      |
| Grouped lists or boards | Useful only if a flat list stops being enough for real work              |
| Activity history        | Helps users review what changed recently                                 |

---

## Success Criteria

We should be able to install `remy`, create a task, move it to `doing`, mark it `done`, and list the result without opening docs.

If the commands are memorable, the output is clean, and the storage stays local and reliable, the first version is doing its job.

Taskbook reference for later feature ideas:
https://klaudiosinani.com/taskbook/
