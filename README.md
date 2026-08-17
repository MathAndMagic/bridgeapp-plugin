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
than asking an agent to:

```
claude mcp login bridgeapp
```

## Other agents

Point any MCP client that speaks streamable HTTP with OAuth at:

```
https://mcp.bridgeapp.ai/mcp
```

It discovers the authorization server and the `bridgeapp:mcp` scope on its own
from `https://mcp.bridgeapp.ai/.well-known/oauth-protected-resource`.

Per-client instructions, including the ones an agent can follow on its own, live
in [`agent-setup.md`](./agent-setup.md), published at
`https://bridgeapp.ai/agent-setup.md`.

## What is in here

```
plugins/bridgeapp/
  .claude-plugin/plugin.json   Claude Code manifest
  .cursor-plugin/plugin.json   Cursor manifest
  .mcp.json                    the hosted BridgeApp MCP server
  skills/                      how to work with a BridgeApp workspace
```

One plugin serves every client: the manifests differ, the MCP server and the
skills are shared.

## Skills

- **bridgeapp-thread** — reading a shared message or thread to the end,
  including the agent transcripts inside it. This is what fires when someone
  shares a message from BridgeApp with your agent.
- **bridgeapp-context** — walking from a task, message, or page to everything it
  hangs off, and where to stop.
- **bridgeapp-links** — the URL shapes and the mention syntax: how to give a
  human a link into the app, and how to resolve the tokens and keys you meet
  while reading.
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
