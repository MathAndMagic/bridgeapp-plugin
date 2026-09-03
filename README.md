# BridgeApp plugins

Official plugins that connect a coding agent to a [BridgeApp](https://bridgeapp.ai)
workspace over MCP, so it can read and act on tasks, chats, threads, pages, and
projects under the signed-in account's own permissions.

## Claude Code

```
claude plugin marketplace add MathAndMagic/bridgeapp-plugin
claude plugin install bridgeapp@bridgeapp
```

Then sign in — this needs an interactive terminal, so run it yourself rather
than asking an agent to. Claude Code addresses a plugin-provided server as
`plugin:<plugin>:<server>`:

```
claude mcp login plugin:bridgeapp:bridgeapp
```

## Codex and ChatGPT

The same plugin installs into Codex — MCP server and skills together:

```
codex plugin marketplace add MathAndMagic/bridgeapp-plugin
codex plugin add bridgeapp@bridgeapp
```

Codex asks you to sign in to BridgeApp the first time a tool is used. The
manifest lives in `.codex-plugin/plugin.json` and the marketplace in
`.agents/plugins/marketplace.json`; both read the same `skills/` and `.mcp.json`
as the Claude Code and Cursor manifests.

## Other agents

Point any MCP client that speaks streamable HTTP with OAuth at:

```
https://mcp.bridgeapp.ai/mcp
```

It discovers the authorization server and the `bridgeapp:mcp` scope on its own
from `https://mcp.bridgeapp.ai/.well-known/oauth-protected-resource`.

Per-client instructions, including the ones an agent can follow on its own, live
in [`agent-setup.md`](./agent-setup.md), published at
`https://bridgeapp.ai/agent-setup.md` and at `/agent-setup.md` on every
workspace host.

That file is the master copy. The same file ships in `bridgeapp-web`
(`public/agent-setup.md`, serving every workspace host) and in `bridge-website`
(`public/agent-setup.md`, serving the apex domain) — edit it here and copy it to
both in the same piece of work, byte-identical.

## Self-hosted workspaces

Every URL above belongs to the hosted service. BridgeApp also runs on customers'
own infrastructure, under domains of their choosing, and the plugin ships the
hosted endpoint in [`.mcp.json`](./plugins/bridgeapp/.mcp.json) — deliberately
as a plain value: Claude Code substitutes plugin settings unreliably across
versions, and a server that silently fails to register is worse than a fixed
URL. On a self-hosted workspace, register the workspace's own endpoint — shown
on its **Agents → Connect Apps** page — directly:

```
claude mcp add --scope user --transport http bridgeapp https://mcp.example.com/mcp
```

The plugin's skills still apply; the plugin's own hosted server stays
unauthenticated and can be ignored or removed from `/mcp`. `bridgeapp-links`
covers the matching rule for building links back into a self-hosted workspace.

## What is in here

```
plugins/bridgeapp/
  .claude-plugin/plugin.json   Claude Code manifest
  .cursor-plugin/plugin.json   Cursor manifest
  .codex-plugin/plugin.json    Codex / ChatGPT manifest (directory metadata, logo)
  .mcp.json                    the hosted BridgeApp MCP server
  skills/                      how to work with a BridgeApp workspace
    bridgeapp-links/references/routes.md   the app's route map
```

One plugin serves every client: the manifests differ, the MCP server and the
skills are shared.

## Skills

- **bridgeapp-task** — working from a task: its plan, comments, subtasks and
  blockers, plus the project brief, which is the only place the repository and
  the team's flow are recorded.
- **bridgeapp-thread** — reading a shared message or thread to the end,
  including the agent transcripts inside it. This is what fires when someone
  shares a message from BridgeApp with your agent.
- **bridgeapp-context** — walking from a task, message, or page to everything it
  hangs off, and where to stop.
- **bridgeapp-links** — the URL shapes and the mention syntax: how to give a
  human a link into the app, and how to resolve the tokens and keys you meet
  while reading. Carries the full route map — every path in the app, web and
  desktop, with the query params each one takes.
- **bridgeapp-investigation** — diagnosing a bug when the trail runs through
  both the repository and BridgeApp. Read-only by construction.
- **bridgeapp-task-creation** — turning a request into a well-formed task,
  including the duplicate check that comes first.

Because the skills carry the how, a shared link only has to carry the what: the
Share action in BridgeApp sends a pointer to the message, not a copy of the
conversation and not a list of instructions.

## Verifying a connection

Call `list_projects`. An empty list is a pass: it means the connection works and
the account has no projects yet.
