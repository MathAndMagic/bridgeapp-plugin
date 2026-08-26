---
name: bridgeapp-task-creation
description: Use when the user asks to turn something into a BridgeApp task — from a chat message, a bug you just found, a piece of work you were discussing — or says "create a task for this". Covers filling the task properly and what to reply.
---

# Creating a BridgeApp task

You place the task. You do not solve it.

## Before creating

**Look for an existing task first.** `tasks_search` on the feature and on the
error text. If something might already cover it, show the user what you found
and ask whether they still want a new one. A duplicate costs more than the
question.

**Determine the project.** From the chat the request came from, from the task
being discussed, from what the user said. If it is genuinely unclear, ask — this
is the one thing that should stop you. Everything else you can fill in or leave
empty.

Then read the surrounding context before writing: the message and its thread,
whatever it references. `get_task_create_context` returns what a project accepts
— statuses, types, components, versions, assignable agents — so you can fill
fields instead of guessing at them.

## Writing it

The description is a spec the assignee acts on, grounded only in what was
actually discussed. Do not invent acceptance criteria nobody agreed to.

Carry over what makes it actionable: the originating thread, related tasks, the
error text as it appeared, the reproduction if there was one. Link them —
mention tokens when the text lives inside BridgeApp, URLs when it does not; see
`bridgeapp-links`.

Set the type from the request. Use the type the user named; otherwise infer it
from the nature of the work.

Create with what you have. A missing detail never blocks creation — only an
unclear project, or a task that already exists.

## What to reply

You are outside BridgeApp, so a mention token renders as raw text here. Give the
user the task key, its title, and a URL they can open:

```
DEV-1234 — Fix the token exchange returning null
https://<workspace-host>/#/projects/<projectId>/board/<taskId>
```

`<projectId>` is the project you created the task in and `<taskId>` comes from
the create result — a task link always carries both; there is no by-key URL.
Build `<workspace-host>` from a link you were already given rather than assuming
one — the workspace may be self-hosted on a domain of its own. The
`bridgeapp-links` skill has the rule and the full route map.

That is the whole reply. No summary of the fields you filled, no offer to do
more, no closing question. The user asked for a task; the task exists; the link
proves it.

Add words only when something needs saying: you could not do one specific thing
they asked for, or you created it in a project you had to guess at. One line,
naming only that.

Reply in the user's language.
