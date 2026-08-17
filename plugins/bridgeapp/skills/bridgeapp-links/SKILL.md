---
name: bridgeapp-links
description: Use when you need to give a human a link into BridgeApp — to a task, a chat, a thread, a message, a page — or when you are reading BridgeApp content and hit a mention token, a task key, or an internal link you need to resolve. Covers the URL shapes and the mention syntax.
---

# BridgeApp links

Inside BridgeApp, references are mentions and keys, not URLs. When you report
back to a human outside the app, they need a URL they can click. Build it.

## The shape

```
https://<workspace-slug>.bridgeapp.ai/#<path>
```

Two things trip people up. The app is a **hash router**, so the path lives after
`#` — a link without it lands on the workspace root. And the host carries the
**workspace slug**, so a link built against the wrong slug opens the wrong
workspace, or nothing.

Take the origin from a link the user already gave you, or from what
`get_current_user` tells you about the workspace. Do not assume `bridgeapp.ai`
bare.

## Paths

| Target | Path |
| --- | --- |
| Task by key | `/issues/DEV-1234` |
| Chat | `/chats/<chatId>` |
| Direct chat | `/chats/direct/<chatId>` |
| Message in a chat | `/chats/<chatId>?messageId=<messageId>` |
| Message in a thread | `/chats/<chatId>?messageId=<messageId>&rs=t--<threadId>` |
| Page | `/docs/<pageId>` |
| Project | `/projects/<projectId>` |

The thread parameter really is `rs=t--<threadId>` — the `t--` prefix is part of
the value, not a typo.

## Mentions, when writing into BridgeApp

Text you post through `create_task_comment`, `send_channel_message`, or
`send_direct_message` is rendered by the app, so a mention token becomes a live
card:

```
@Task:<uuid>
```

Use the token when you are writing **into** BridgeApp — it renders as the task
with its title and status, and stays correct when the task is renamed. Use a URL
when you are writing **outside** it, in a terminal, an editor, or a summary back
to the user, where the token is just text.

## Reading them

Going the other way, in content you read:

- `DEV-1234` — a task key, resolve with `get_task_by_key`
- `@Task:<uuid>` — a mention token, resolve with `get_task`
- a `/#/chats/...` link — parse `chatId` from the path, `messageId` and the
  `t--`-prefixed `rs` from the query

A bare uuid with no prefix is ambiguous; find out what it belongs to from
context rather than guessing an object type.
