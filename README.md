# RedlineJS MCP server

**Point at the problem. Your agent ships the fix.**

[RedlineJS](https://redlinejs.com) is visual website feedback for coding agents. Reviewers highlight text, circle a detail or pin a comment to the exact element on the live page. `redline-mcp` gives those comments to Claude Code, Codex, Cursor or any other MCP client: the agent reads each comment with the element attached, replies, makes the fix and marks it resolved. Reviewers see the agent's replies and status changes in their panel, labelled Agent.

The server ships in the npm package [`@perpetio/redline`](https://www.npmjs.com/package/@perpetio/redline) and needs **Node.js 20 or later**. This repository holds its marketplace listing and documentation; it contains no source code.

[Website](https://redlinejs.com) · [MCP docs](https://redlinejs.com/docs/mcp) · [Get started](https://redlinejs.com/docs) · [Redline Cloud](https://app.redlinejs.com)

## What the agent gets

Every comment records the element it is about: a readable label (`Button "Start free trial"`), a stable selector that prefers ids and `data-testid`, the nearest heading, its classes, size and computed styles, and, in React, Vue or Svelte development builds, the component chain and source file:

```markdown
## 1. Button "Start free trial"
Pin · open · Jane Doe
> Should say "Start 14-day trial", legal asked for it.

- Selector: `[data-testid="start-trial"]`
- Component: `<Pricing> <PlanCard> <Button>` · `src/components/PlanCard.tsx:42:7`
- Under heading: "Pricing"
```

## Quick start (local)

1. In your project folder, register the server with your coding agent.

   **Claude Code:**

   ```bash
   claude mcp add redline -- npx -y -p @perpetio/redline@latest redline-mcp
   ```

   **Codex:**

   ```bash
   codex mcp add redline -- npx -y -p @perpetio/redline@latest redline-mcp
   ```

   **VS Code (GitHub Copilot):** [Install in VS Code](https://vscode.dev/redirect/mcp/install?name=redline&config=%7B%22type%22%3A%22stdio%22%2C%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22-p%22%2C%22%40perpetio%2Fredline%40latest%22%2C%22redline-mcp%22%5D%7D). VS Code shows the server to you before it installs. From a terminal:

   ```bash
   code --add-mcp '{"name":"redline","type":"stdio","command":"npx","args":["-y","-p","@perpetio/redline@latest","redline-mcp"]}'
   ```

   It starts a review backend for the folder on `127.0.0.1:7335` and keeps its data in `.annotate/` in the project folder. That folder has its own `.gitignore`, so nothing is committed.

2. Open the page you are developing, on `localhost` or `127.0.0.1`, with the Redline snippet:

   ```html
   <script src="https://unpkg.com/@perpetio/redline"></script>
   ```

   Click **Settings** (the gear) in the Redline toolbar and turn on **Send comments to my coding agent**.

3. Leave some comments, then tell the agent: "work through the Redline comments".

## Cursor, Claude Desktop and other clients

Any MCP client that can start a stdio server works. The usual JSON config:

```json
{
  "mcpServers": {
    "redline": {
      "command": "npx",
      "args": ["-y", "-p", "@perpetio/redline@latest", "redline-mcp"]
    }
  }
}
```

Clients that don't start servers in the project folder (Claude Desktop starts them in `/`) need the folder given explicitly: add `"--data-dir", "/absolute/path/to/project/.annotate"` to `args`, or use the client's `cwd` setting.

## Claude Code plugin

This repository is also a Claude Code plugin that starts the same server, pinned to the current release:

```
/plugin install redlinejs --marketplace perpetio/redlinejs-mcp
```

When you enable it, Claude Code asks for a Redline Cloud review and agent invite. Both are optional: leave them empty to use the local review in your project folder.

## Gemini CLI extension

```bash
gemini extensions install https://github.com/perpetio/redlinejs-mcp
```

It asks for the same two optional settings.

## What the server runs and sends

- `npx` downloads `@perpetio/redline` from the npm registry and runs its `redline-mcp` server on your computer.
- **Local mode** (no review or invite): the server starts a review backend on `127.0.0.1:7335`, which the Redline widget on your `localhost` page talks to, and keeps the comments in `.annotate/` in the folder it starts in. Nothing leaves your computer.
- **Remote mode** (review and invite set): the server connects to Redline Cloud at `https://api.redlinejs.com` with the agent invite, reads that review's comments and sends your agent's replies and status changes.
- It sends no analytics or telemetry.

## Tools

| Tool | What it does |
|---|---|
| `annotate_list_pending` | Open and in-progress comments, oldest first, as Markdown plus JSON. Optional `page` filter. |
| `annotate_get_comment` | One comment in full: styles, position, replies and activity. |
| `annotate_reply` | Adds a reply to a comment thread. |
| `annotate_set_status` | Sets `in_progress`, `resolved`, `wont_fix` or `open`. A note is posted as a reply first, and `wont_fix` needs one. |
| `annotate_watch` | Waits for new or changed comments, then returns them. |

## Remote mode (Redline Cloud)

To work against a shared review in [Redline Cloud](https://redlinejs.com/docs/cloud) instead of the local one, pass the review and the review's agent invite:

```bash
npx -y -p @perpetio/redline@latest redline-mcp --review rvw_… --invite agt_…
```

`REDLINE_REVIEW` and `REDLINE_INVITE` work in place of the flags. Create the agent token in the review's Agent tab at [app.redlinejs.com](https://app.redlinejs.com); it shows this command ready to copy.

Anyone who can comment on a review can write to your agent. Don't pair an open review with an agent on a site that strangers can reach.

## Troubleshooting

Run `npx -y -p @perpetio/redline@latest redline-mcp doctor` in the same folder (with the same flags or variables in remote mode). It prints a line per check and exits 1 if one fails. More in the [MCP docs](https://redlinejs.com/docs/mcp#troubleshooting).

## License

The widget, local mode and `redline-mcp` are free to use in personal and commercial projects under the terms in [LICENSE](LICENSE). RedlineJS is not open source. Redline Cloud is a subscription service by Perpetio OÜ, provided under its [Terms of Service](https://redlinejs.com/terms) and [Privacy Policy](https://redlinejs.com/privacy).

Questions: [redlinejs.com/contact](https://redlinejs.com/contact).

[![RedlineJS MCP server](https://mcprush.com/badge/redlinejs-mcp/card.svg)](https://mcprush.com/to/redlinejs-mcp?ref=badge)
