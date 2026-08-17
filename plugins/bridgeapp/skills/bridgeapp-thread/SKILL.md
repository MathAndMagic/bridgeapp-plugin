---
name: bridgeapp-thread
description: Use when someone hands you a BridgeApp message or thread — a shared message, a permalink, or a chat/thread/message id — and asks you to read it, summarize it, or act on it. Covers reading a conversation to the end and picking up the agent activity inside it.
---

# Reading a BridgeApp conversation

You were pointed at one message. Read the conversation around it before doing
anything with it.

## Read the whole thing, not the first page

```
list_thread_messages(thread_id)     when the message has a thread
list_chat_messages(chat_id)         when it does not
```

Both return **15 messages per page**. Keep paging until a page comes back short
or empty. Stopping at the first page is the single most common way to
misread a BridgeApp conversation: the reply that matters is usually not in the
first fifteen.

`list_chat_messages` takes `created_after` and `created_before` as RFC3339
timestamps. When you only need the neighbourhood of one message, bracket it
rather than reading the channel from its beginning.

## Pick up what the agents did

A message with agent activity has a transcript: reasoning, tool calls, skill
calls, one 15-step page at a time.

```
get_message_transcript(message_id)
```

Read it when the conversation refers to something an agent did — "it failed",
"see what it found" — otherwise the thread alone will not explain the outcome.

## Know who is talking

`get_current_user` tells you which account you are acting as, which is also
whose permissions apply. `list_project_participants` resolves the names behind
the ids in a project chat.

## Then follow the references

Task keys (`DEV-1234`), `@Task:<uuid>` mention tokens, page links, and links to
other messages all appear as plain text in message content. Walking those is the
`bridgeapp-context` skill; use it rather than repeating its rules here.

## Before you reply into BridgeApp

`create_task_comment`, `send_channel_message`, and `send_direct_message` post as
the signed-in user and are immediately visible to their colleagues. Summarize
back in this session first and let the user decide what gets posted, unless they
asked for exactly that message.
