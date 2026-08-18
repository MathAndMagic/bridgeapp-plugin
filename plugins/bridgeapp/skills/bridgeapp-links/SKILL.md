---
name: bridgeapp-links
description: Use when you need to give a human a link into BridgeApp — to a task, a chat, a thread, a message, a page, a call, a database entry, an agent — or when you are reading BridgeApp content and hit a mention token, a task key, or an internal link you need to resolve. Covers the URL shapes for web and the desktop app, the route map, and the mention syntax.
---

# BridgeApp links

Inside BridgeApp, references are mentions and keys, not URLs. When you report
back to a human outside the app, they need a URL they can click. Build it.

## The shape

```
https://<workspace-host>/#<path>
```

Only the `#<path>` half is fixed. The app is a **hash router**, so the path
lives after `#` — a link without it lands on the workspace root, and the same
path without the `#` hits the server, which knows nothing about it.

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

## The desktop app

BridgeApp also ships as a desktop app, which registers the `bridgeapp://`
scheme — not `bridge://`. A deep link into it is:

```
bridgeapp://app/#<path>
```

The same `#<path>` as on the web: the desktop shell hands the URL to the app,
which reads **only the hash** and navigates there. Everything in the route map
below works unchanged.

Two things not to do with it:

- Do not hand `bridgeapp://` links to someone unless you know they run the
  desktop app — a browser cannot open them. Web URLs work everywhere, including
  inside the desktop app, which is why the app's own Copy-link actions produce
  `https://` links even when running on the desktop.
- `bridgeapp://app/oauth/callback` is reserved for the sign-in flow. Never
  construct it.

## The paths that matter most

| Target | Path |
| --- | --- |
| Task | `/projects/<projectId>/board/<taskId>` |
| Message in a chat | `/chats/<chatId>?messageId=<messageId>` |
| Message in a thread | `/chats/<chatId>?messageId=<messageId>&rs=t--<threadId>` |
| Chat | `/chats/<chatId>` |
| Direct chat | `/chats/direct/<chatId>` |
| Page | `/docs/<pageId>` |
| Project | `/projects/<projectId>` |
| Call | `/calls/<callId>` |
| Database entry | `/databases/<databaseId>?rs=v&e=<entryId>` |
| Agent | `/agents/<agentId>` |
| Person | `/contacts/contacts/<userId>` |

Two of these repay a second look.

**A task link needs the project id.** `/issues/<taskId>` looks like the short
way there and is not — the route resolves nothing and lands on the chat list.
Nor does any URL accept a `DEV-1234` key: that is how humans refer to a task,
not how the app routes to one. Resolve the key with `get_task_by_key`, take
`projectId` off the task, and build the path above. `board`, `backlog` and
`list` are interchangeable in that path; they only decide which view opens
behind the task.

**The thread parameter really is `rs=t--<threadId>`** — the `t--` prefix is part
of the value, not a typo.

Everything else — every route in the app, the query params each one takes, and
the paths that exist but lead nowhere — is in
[`references/routes.md`](references/routes.md). Read it before inventing a path.

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

A URL you post into BridgeApp is not wasted either: the app recognizes links
whose origin is the workspace itself and renders them as mention cards, matching
the task, page, chat, database, agent and user paths from the table above. A
link carrying the wrong host stays a plain link — one more reason not to guess
the origin.

## Reading them

Going the other way, in content you read:

- `DEV-1234` — a task key, resolve with `get_task_by_key`
- `@Task:<uuid>` — a mention token, resolve with `get_task`
- a `/#/chats/...` link — parse `chatId` from the path, `messageId` and the
  `t--`-prefixed `rs` from the query
- a `/#/projects/<projectId>/<view>/<taskId>` link — the last segment is the
  task id, and it is a uuid, not a key; `get_task` takes it directly
- a `bridgeapp://` link — strip everything before the `#` and read the rest
  exactly as a web path

A bare uuid with no prefix is ambiguous; find out what it belongs to from
context rather than guessing an object type.
