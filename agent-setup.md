# Connect your coding agent to BridgeApp

> Official BridgeApp instructions that connect your coding agent to the BridgeApp
> MCP server, so it can read and act on your workspace — tasks, chats, threads,
> pages, and projects.
>
> These instructions are published at `https://bridgeapp.ai/agent-setup.md` and
> at `/agent-setup.md` on every BridgeApp workspace host — a self-hosted
> workspace serves its own copy — so you can verify their origin before
> following them.

Complete the steps below yourself by running the commands directly. The only
exceptions are the steps marked **User step** — sign-in happens in a browser and
in your agent's own interface, so you cannot perform them. Never run a command
that blocks on an interactive prompt.

Every command here is safe to re-run on a machine where setup already happened.

## What this gives you

One MCP server, `bridgeapp`, exposing the workspace over the Model Context
Protocol: tasks and their comments, subtasks, blockers and plan documents; chats
and threads; pages; projects and participants. Reads and writes both — the
server enforces the permissions of the account that signs in, not more.

## The server URL

- Server URL: `https://mcp.bridgeapp.ai/mcp`
- OAuth scope: `bridgeapp:mcp`

**Unless you were given a different one.** BridgeApp also runs on customers' own
infrastructure, under domains that need not mention BridgeApp at all, and the
**Connect Apps** page passes the right endpoint alongside these instructions
when it asks an agent to set itself up. A URL you were handed always wins over
the one printed here; substitute it into every command below. If you have
neither, the workspace shows its endpoint under **Agents → Connect Apps**.

Clients discover the scope on their own from the server's protected-resource
metadata, so you never have to configure it.

## Claude Code

Prefer the plugin — it registers the MCP server and installs the BridgeApp
skills next to it:

```
claude plugin marketplace add MathAndMagic/bridgeapp-plugin
claude plugin install bridgeapp@bridgeapp
```

The plugin ships the hosted server URL. Given a different one, pass it to the
install:

```
claude plugin install bridgeapp@bridgeapp --config endpoint=https://mcp.example.com/mcp
```

> **User step.** Ask the user to run `claude mcp login plugin:bridgeapp:bridgeapp`
> in a terminal and approve the browser prompt. It needs an interactive
> terminal, so you cannot complete it from a tool call. Wait for the user to
> confirm before reporting the server as connected.

Verify:

```
claude mcp get plugin:bridgeapp:bridgeapp
```

Expect `Status: ✔ connected`. `Needs authentication` means the sign-in above has
not finished.

Without the plugin, register just the server — no skills come with it:

```
claude mcp add --scope user --transport http bridgeapp https://mcp.bridgeapp.ai/mcp
```

`--scope user` matters: without it the server is bound to the directory the
command ran in, and signing in from anywhere else fails to find it. Sign-in and
verification work the same, with the server named plainly `bridgeapp`.

## ChatGPT and Codex

One configuration serves the ChatGPT desktop app, the Codex CLI, and the IDE
extension — they share `~/.codex/config.toml`.

```
codex mcp add bridgeapp --url https://mcp.bridgeapp.ai/mcp
```

This writes the configuration and starts the OAuth flow on its own.

> **User step.** Ask the user to approve the browser prompt that opens. If no
> browser opens, they can run `codex mcp login bridgeapp`.

Without the CLI, add the block by hand to `~/.codex/config.toml`:

```toml
[mcp_servers.bridgeapp]
url = "https://mcp.bridgeapp.ai/mcp"
```

## Cursor

Cursor has no CLI for this. Add the server to `~/.cursor/mcp.json`, or to
`.cursor/mcp.json` for a single project:

```json
{
  "mcpServers": {
    "bridgeapp": {
      "url": "https://mcp.bridgeapp.ai/mcp"
    }
  }
}
```

> **User step.** Ask the user to open Cursor's MCP settings and authorize
> BridgeApp when the browser prompt appears.

## Other agents

Any client that speaks streamable HTTP MCP with OAuth works. Point it at
`https://mcp.bridgeapp.ai/mcp`; it discovers the authorization server and the
`bridgeapp:mcp` scope on its own through the protected-resource metadata at
`https://mcp.bridgeapp.ai/.well-known/oauth-protected-resource`.

## Verify end to end

Once the user has signed in, confirm the connection by calling a read tool —
`list_projects` is the cheapest. An empty list is a pass: it means the
connection works and the account has no projects yet.

If a call fails with an authorization error, the sign-in did not complete. If it
fails during the token exchange, report the error verbatim rather than retrying:
that is a server-side problem, not something the user can fix by signing in
again.

## Report the result

Report only what you verified. Do not print a checkmark for anything you could
not confirm — the server stays unauthenticated until the user finishes signing
in.

```
┌─ BridgeApp Agent Setup ──────────────────────────────┐
│  ✓ bridgeapp MCP   registered                        │
│  ⚠ sign-in         needs the user: claude mcp login  │
│                                                      │
│  ⚡ Reload your agent to pick up the new tools       │
└──────────────────────────────────────────────────────┘
```

Use `✓` for verified, `⚠` for a pending user action, `✗` for failed. Follow the
banner with the specific next action for every line that is not `✓`.

## Resources

- Connect Apps, in the app: **Agents → Connect Apps**
- Plugin and skills: `https://github.com/MathAndMagic/bridgeapp-plugin`
- Claude Code MCP: `https://code.claude.com/docs/en/mcp`
- Codex MCP: `https://learn.chatgpt.com/docs/extend/mcp`
- Cursor MCP: `https://cursor.com/docs/mcp`
