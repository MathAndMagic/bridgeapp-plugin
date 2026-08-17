---
name: bridgeapp-context
description: Use when the user points at something in BridgeApp — a task key like DEV-1234, a message or thread, a page, a project — or shares a BridgeApp link, and you need the surrounding context before acting. Covers how to walk from one BridgeApp object to everything it hangs off without crawling the whole workspace.
---

# Reading BridgeApp context

BridgeApp objects are a graph, not a list. A message references a task, the task
has a plan document and blockers, each blocker has its own thread. Pull the part
you need and stop.

## Entry points

| You were given | Start with |
| --- | --- |
| Task key, e.g. `DEV-1234` | `get_task_by_key` |
| `@Task:<uuid>` mention token | `get_task` — it is a token, not a URL |
| Chat message | `list_thread_messages` for its thread, else `list_chat_messages` |
| Page link | `get_page` |
| Nothing specific | `get_current_user`, then `list_projects` |

## Walking outward

Both message listings return **15 per page**. Page until exhausted — reading the
first page and treating it as the whole thread is the most common mistake here.

From a task, the useful neighbours are `list_task_comments`,
`list_task_subtasks`, `list_task_blockers`, `list_task_pages`, and
`get_task_plan_context`, which returns the root-to-current chain together with
the plan documents rather than one task in isolation.

For a message that shows agent activity, `get_message_transcript` returns that
agent's steps — reasoning, tool calls, skill calls — one 15-step page at a time.

## Where to stop

Follow references **two hops** and no further, and skip anything already loaded.
The graph is cyclic: tasks have blockers, blockers have threads, threads mention
tasks. Without a bound the walk does not terminate.

If a call comes back denied, say so and move on. The server enforces the
permissions of the signed-in account; a denial is information about the
workspace, not a problem to route around.

## Writing back

Reads are free; writes are not. `create_task_comment`, `update_task`,
`send_channel_message`, and `send_direct_message` act as the signed-in user and
are visible to their colleagues immediately. Say what you are about to post and
where, and let the user confirm, unless they already asked for exactly that.
