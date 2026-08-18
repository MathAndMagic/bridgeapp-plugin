# BridgeApp route map

Every path below is a **hash-router path**: it goes after the `#` in a web URL
(`https://<workspace-host>/#/docs/abc`) and after the `#` in a desktop deep link
(`bridgeapp://app/#/docs/abc`). Nothing here is a server path — requesting
`https://<host>/docs/abc` returns the SPA shell, not the page.

`:name` marks a path parameter. Unless a row says otherwise, ids are uuids.

## Home and top level

| Path | What it is |
| --- | --- |
| `/` | web: redirects to `/home`; desktop: the workspace-picker welcome screen |
| `/home` | the home feed — the app's landing surface |
| `/search` | search page. The query is passed through router state, **not** the URL — a `/search` link always opens empty |

## Chats

| Path | What it is |
| --- | --- |
| `/chats` | channel list, no chat selected |
| `/chats/:chatId` | a channel or group chat |
| `/chats/channels` | channel browser (`?chatId=` preselects one) |
| `/chats/drafts` | unsent drafts |
| `/chats/threads` | every thread the user follows |
| `/chats/threads/:chatId` | that list, scoped to one chat |
| `/chats/unreads` | unread across chats |
| `/chats/direct` | direct-message list |
| `/chats/direct/:chatId` | one direct conversation |

Query params, on `/chats/:chatId` and `/chats/direct/:chatId`:

| Param | Meaning |
| --- | --- |
| `messageId=<id>` | scroll to and highlight that message — this is what makes a permalink |
| `rs=t--<threadId>` | open the thread sidebar (the `t--` prefix is part of the value) |
| `rs=c` | open the chat-info sidebar |
| `rs=p` | open pinned messages |
| `rs=pt` + `userId=` / `agentId=` / `serviceAccountId=` | open that participant's profile sidebar |
| `tab=` | which chat-info tab: `i` info, `m` members, `md` media, `f` files, `l` links |

The permalink the app's own **Copy link** produces is
`/chats/<chatId>?messageId=<messageId>` plus `&rs=t--<threadId>` when the
message lives in a thread. Directs use the `/chats/direct/<chatId>` base.

## Calls

| Path | What it is |
| --- | --- |
| `/calls/:callId` | the full-screen call page |
| `/pip-call/:callId` | **desktop only** — the standalone picture-in-picture window. Never link a human here |

Query params on `/calls/:callId`:

| Param | Meaning |
| --- | --- |
| `rs=t--<threadId>` | open the Call info sidebar |
| `tab=` | which Call info tab: `thread`, `overview`, `transcript`, `participants`, `media`, `files`, `links` |
| `rs=c` | channel info |
| `rs=pt` + `userId=` | participant profile |
| `messageId=` | highlight a message inside the call thread |

`/calls` on its own renders nothing useful — always include the call id.

## Tasks and projects

Tasks live under their project. `/tasks` exists only as a redirect to
`/projects`.

| Path | What it is |
| --- | --- |
| `/projects` | empty state |
| `/projects/list` | the list of projects (`?p=<projectId>` opens the project sidebar) |
| `/projects/:projectId` | redirects to that project's board |
| `/projects/:projectId/board` | board view |
| `/projects/:projectId/board/:taskId` | board view with the task open |
| `/projects/:projectId/board/create` | board view with the create-task form open |
| `/projects/:projectId/backlog`, `/backlog/:taskId`, `/backlog/create` | same three for the backlog |
| `/projects/:projectId/list`, `/list/:taskId`, `/list/create` | same three for the list view |
| `/projects/:projectId/milestones`, `/milestones/create`, `/milestones/:milestoneId` | milestones |
| `/projects/:projectId/versions`, `/versions/create`, `/versions/:versionId` | versions |
| `/projects/:projectId/components`, `/components/create`, `/components/:componentId` | project components |
| `/projects/:projectId/settings` | project settings |
| `/projects/:projectId/settings/general`, `/brief`, `/worktypes`, `/members`, `/integrations` | its tabs |
| `/projects/:projectId/settings/worktypes/:taskTypeId` | one work type |

**A task link needs the project id.** `/projects/<projectId>/board/<taskId>` is
what the app's own Copy-link action on a task produces; `board`, `backlog` and
`list` are interchangeable and just decide which view opens behind the task.

Query params on the task views:

| Param | Meaning |
| --- | --- |
| `jql=` | the encoded filter query |
| `presetId=` | selected task-view preset |
| `q=` | text search |
| `g=` | grouping |
| `f=` | legacy JSON filter, still read for old links |
| `statusId=` | on `/create`, the status the new task starts in |
| `parentTaskId=` | on `/create`, the parent it hangs under |
| `page=`, `size=` | list-view paging |

## Pages (docs)

| Path | What it is |
| --- | --- |
| `/docs` | page tree, nothing open |
| `/docs/:pageId` | one page |

`?projectId=` scopes the tree to a project; `?createProjectId=` preselects the
project for a new page.

## Databases

| Path | What it is |
| --- | --- |
| `/databases` | database list |
| `/databases/:databaseId` | one database |
| `/databases/:databaseId/settings` | its schema settings |

Query params on `/databases/:databaseId`: `rs=v` view / `rs=e` edit / `rs=c`
create sidebar, `e=<entryId>` the entry the sidebar shows, `q=` search, `f=`
filters, `p=` page, `ps=` page size. A link to a single row is therefore
`/databases/<databaseId>?rs=v&e=<entryId>`.

## Agents

| Path | What it is |
| --- | --- |
| `/agents` | the agent library (`?rs=pt&agentId=<id>` opens a profile sidebar over it) |
| `/agents/new` | create-agent sidebar |
| `/agents/:agentId` | agent profile |
| `/agents/:agentId/edit/:configId` | edit an agent configuration |
| `/agents/:agentId/configs/:configId` | legacy deep link, resolves to the profile |
| `/agents/flows` | flow list |
| `/agents/flows/edit/:flowId` | flow editor (full screen, outside the agents layout) |
| `/agents/mcp` | MCP servers |
| `/agents/mcp/create`, `/agents/mcp/:mcpId`, `/agents/mcp/:mcpId/edit` | add / view / edit one |
| `/agents/mcp/browse/:presetKey` | one of the built-in server presets |
| `/agents/mcp/oauth-callback` | OAuth return leg — never link a human here |
| `/agents/skills`, `/agents/skills/:skillId` | skill library |
| `/agents/rules`, `/agents/rules/:ruleId` | rules |

`?view=list` switches the library grids to list layout.

## Routines and automations

| Path | What it is |
| --- | --- |
| `/routines` | routine list |
| `/routines/create` | full-page create form |
| `/routines/:routineId` | routine detail with its runs (`?taskId=`+`?projectId=` open a task sidebar over it) |
| `/routines/:routineId/edit` | full-page edit form |
| `/automations` | automation list |
| `/automations/new` | create sidebar |
| `/automations/:automationId` | edit sidebar |
| `/automations/:automationId/flow/:flowId` | the automation's flow editor |
| `/automations/launches` | launch history |
| `/automations/launches/:launchId` | one launch |

## CRM

| Path | What it is |
| --- | --- |
| `/crm` | customer list |
| `/crm/:customerId` | customer sidebar, Info tab |
| `/crm/:customerId/interactions` | customer sidebar, Interactions tab |
| `/crm/:customerId/interactions/:interactionId` | one interaction |
| `/crm/:customerId/activity` | customer sidebar, Activity tab |
| `/crm/views` | saved views |
| `/crm/views/:viewId` | one view (`?view=` display mode, `?q=` search, `?f=` filters, `?page=`, `?size=`) |
| `/crm/views/:viewId/new` | create-entry sidebar (`?stageId=` preselects a stage) |
| `/crm/views/:viewId/:entryId` | one entry |
| `/crm/campaigns`, `/crm/campaigns/create`, `/crm/campaigns/:campaignId` | campaigns |
| `/crm/layouts`, `/crm/layouts/create`, `/crm/layouts/:layoutId` | layouts |

## People

| Path | What it is |
| --- | --- |
| `/contacts` | redirects to `/contacts/contacts` |
| `/contacts/contacts`, `/contacts/contacts/:contactId` | the user's contacts |
| `/contacts/incoming`, `/contacts/incoming/:contactId` | incoming contact requests |
| `/contacts/outgoing`, `/contacts/outgoing/:contactId` | sent requests |
| `/profile/:userId` | a person's profile, rendered standalone — no app chrome, no sidebar |

`?view=list` switches the contact grids to list layout.

A user mention in content links to `/contacts/contacts/<userId>`; the workspace
member row links to `/settings/users/<userId>`. Both resolve to the same person.

The shareable public profile is different: it is served from the reserved `me`
subdomain — `https://me.<base-domain>/#/profile/<userId>` — rather than the
workspace host.

## Workspace settings

| Path | What it is |
| --- | --- |
| `/settings` | redirects to `/settings/general` |
| `/settings/general` | workspace name, slug, defaults |
| `/settings/brief` | the company brief |
| `/settings/emoji` | custom emoji |
| `/settings/users`, `/settings/users/:userId` | members (`?tab=all\|blocked`, `?view=list`) |
| `/settings/roles`, `/settings/roles/create`, `/settings/roles/:roleId` | roles and permissions |
| `/settings/serviceAccounts` | service accounts — note the camelCase segment |
| `/settings/serviceAccounts/new`, `/settings/serviceAccounts/:serviceAccountId`, `/settings/serviceAccounts/:serviceAccountId/edit` | create / view / edit one |
| `/settings/billing`, `/settings/billing/plans` | billing |
| `/settings/integrations`, `/settings/integrations/github` | integrations, including **Connect Apps** — where the workspace's own MCP endpoint is shown |

## Paths that look like routes but are not

Do not build links from these. They exist in the app's navigation tree or its
route table without a working destination:

- `/issues/:key` — the historical "task by key" path. It is still in the route
  table, but it renders the chat page, finds no chat id, and bounces to
  `/chats`. **There is no URL that opens a task by its `DEV-1234` key** — resolve
  the key with `get_task_by_key`, then build
  `/projects/<projectId>/board/<taskId>`.
- `/sign-in`, `/sign-up`, `/accept-invite`, `/reset-password`, `/confirm-user` —
  authentication moved to a separate `auth.<base-domain>` service; these are
  leftovers with no route mounted.
- `/legal/terms-of-service`, `/legal/privacy-policy`, `/legal/cookie-policy` —
  the real ones live on the marketing site, `https://bridgeapp.ai/legal/...`.
- `/crm/companies`, `/crm/funnels`, `/projects/overview`, `/projects/roadmap`,
  `/projects/:projectId/overview`, `/projects/:projectId/roadmap`,
  `/profile/notifications`, `/profile/security`, `/profile/preferences` —
  declared, never mounted.
- `/oauth/callback` and `/callback` — these are real **server** paths, not hash
  paths, and belong to the sign-in flow.
