---
name: bridgeapp-links
description: Use when you need to give a human a link into BridgeApp — to a task, a chat, a thread, a message, a page — or when you are reading BridgeApp content and hit a mention token, a task key, or an internal link you need to resolve. Covers the URL shapes and the mention syntax.
---

# BridgeApp links

Inside BridgeApp, references are mentions and keys, not URLs. When you report
back to a human outside the app, they need a URL they can click. Build it.

## The shape

```
https://<workspace-host>/#<path>
```

Only the `#<path>` half is fixed. The app is a **hash router**, so the path
lives after `#` — a link without it lands on the workspace root.

**The host is never yours to assume.** On the hosted service it looks like
`<workspace-slug>.bridgeapp.ai`, but BridgeApp is also deployed on customers'
own infrastructure, under domains that need not contain "bridge" or "bridgeapp"
at all. A link built against the wrong host opens the wrong workspace, or
nothing — and it looks perfectly valid while doing it.

**MCP will not tell you the host.** `get_current_user` returns only
`company_id`, `participant_type`, and `participant_id` — no workspace host, no
name. So copy the origin verbatim from something you were already given:

1. A link passed with the request — the Share button sends one along with every
   task and message it hands you.
2. The link on any BridgeApp item the user pasted or you were asked about.
3. The URL of the page the user is looking at.

If you have none of those, ask. Do not fall back to `bridgeapp.ai`.

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
