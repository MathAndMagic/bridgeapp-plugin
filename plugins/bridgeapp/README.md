# BridgeApp

Work your [BridgeApp](https://bridgeapp.ai) workspace from Claude. The plugin
connects Claude to the BridgeApp MCP server, so it can read and act on your
tasks, chats, threads, pages, and projects, and adds skills that teach it the
workflows around them: picking up a task with its plan and blockers, reading a
shared thread to the end, investigating a bug reported in BridgeApp, and
turning a conversation into a well-formed task.

## What you get

- **The BridgeApp MCP server** at `https://mcp.bridgeapp.ai/mcp`. You sign in
  with your BridgeApp account; Claude acts with that account's permissions and
  nothing more.
- **Skills:**
  - **bridgeapp-task** — work from a task: its plan, comments, subtasks,
    blockers, and the project brief.
  - **bridgeapp-thread** — read a shared message or thread to the end,
    including agent activity inside it.
  - **bridgeapp-context** — walk from a task, message, page, or link to what it
    hangs off, and stop there.
  - **bridgeapp-investigation** — diagnose a bug whose trail runs through both
    the code and BridgeApp. Read-only.
  - **bridgeapp-task-creation** — create a task from a request, checking for
    duplicates first.

## What it sends where

The plugin runs no local code. Its only connection is the BridgeApp MCP server
above, which reads and writes your workspace on your behalf after you sign in.
See the [privacy policy](https://bridgeapp.ai/legal/privacy-policy) for how
BridgeApp handles your data.

## Getting started

After adding the plugin, connect BridgeApp when Claude asks you to sign in.
Then try:

- "What's on my plate in BridgeApp this week?"
- "Read DEV-1234 and plan the work."
- "Summarize this thread: <BridgeApp message link>"

Setup for other agents, and for workspaces hosted on your own domain:
<https://bridgeapp.ai/agent-setup.md>.

## Support

support@mathandmagic.ai
