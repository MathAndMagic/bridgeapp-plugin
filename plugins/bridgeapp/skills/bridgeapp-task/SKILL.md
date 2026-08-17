---
name: bridgeapp-task
description: Use when someone hands you a BridgeApp task — a key like DEV-1234, a task link, or a shared task — and asks you to read it, plan it, or work on it. Covers the task's own graph, the project it belongs to, and how to find out which repository and conventions apply.
---

# Working from a BridgeApp task

A task alone is rarely enough to act on. It sits in a project that carries the
conventions, and it points at conversations that carry the reasoning.

## 1. The task itself

```
get_task_by_key("DEV-1234")     when you have the key
get_task(id)                    when you have the id
```

Then its graph, as the question requires:

| Tool | What it adds |
| --- | --- |
| `get_task_plan_context` | root-to-current chain **with plan documents** — start here for anything non-trivial |
| `list_task_comments` | the discussion, where scope usually changed |
| `list_task_subtasks` | how the work was broken down |
| `list_task_blockers` | what stops it |
| `list_task_blocked_tasks` | what it stops — matters before you touch anything |
| `list_task_pages` | attached documents |

Each pages at 15. Keep going until a page comes back short.

## 2. The project, which is where the rules live

Read these before you write code for a task:

```
get_project(id)          name, description
get_project_brief(id)    conventions, flow, links
get_company_brief()      workspace-wide rules
```

**The briefs are how you learn which repository this work belongs to.** MCP
exposes no structured repository, VCS, or integration field — the project's
repository, its branch and PR conventions, its deploy flow, and any agent
workflow are prose in the brief, if they are recorded at all.

So: read both briefs, and take what they say about repositories and process as
the instruction. If neither names a repository and the task does not either, ask
the user which one to work in rather than inferring it from the directory you
happen to be sitting in.

Where a project has structure worth knowing — `list_project_components`,
`list_project_milestones`, `list_project_versions`,
`list_project_participants`, `list_project_agents` — pull it only when the task
turns on it: choosing a component for a new task, checking who owns an area,
seeing which agents can be assigned.

## 3. The conversations it points at

Task descriptions and comments carry references, and they are usually where the
"why" lives:

- a chat or message link → read that conversation; see `bridgeapp-thread`
- `@Task:<uuid>` or another `DEV-` key → a related task, one hop
- a page link → `get_page`

Follow them two hops deep, no further, and skip what you already loaded. The
graph is cyclic — tasks reference threads which reference tasks.

## 4. Say what you actually found

Before doing the work, report back: what the task asks, which project and
repository it belongs to, what the plan document says, what blocks it, and what
you could not determine. If the repository is unclear, that is the first thing
to say, not a detail to bury.

Writing back into BridgeApp — `create_task_comment`, `update_task` — posts as
the signed-in user and is visible immediately. Summarize here first and let the
user decide what goes back, unless they asked for exactly that.
