# Installing the RedlineJS MCP server

These steps are for an AI assistant (such as Cline) that sets up `redline-mcp` for a user. The server ships in the npm package `@perpetio/redline`, runs over stdio, and needs Node.js 20 or later (`node --version`).

## 1. Add the server

Add this entry to the MCP settings. Replace `/absolute/path/to/project` with the absolute path of the user's project folder: the server keeps its comments in `.annotate/` there.

```json
{
  "mcpServers": {
    "redline": {
      "command": "npx",
      "args": ["-y", "@perpetio/redline@latest", "--data-dir", "/absolute/path/to/project/.annotate"]
    }
  }
}
```

No API key is needed for this local mode. It starts a review backend on `127.0.0.1:7335`.

## 2. Turn it on in the page

The user's page, opened on `localhost` or `127.0.0.1`, needs the Redline script:

```html
<script src="https://unpkg.com/@perpetio/redline"></script>
```

Ask the user to click **Settings** (the gear) in the Redline toolbar and turn on **Send comments to my coding agent**, then leave a few comments on the page.

## 3. Check it

Call `annotate_list_pending`. It returns the open comments, oldest first, each with the element it is about. Reply with `annotate_reply` and close each one with `annotate_set_status` (`resolved`, or `wont_fix` with a note).

If the call fails, run `npx -y -p @perpetio/redline@latest redline-mcp doctor --data-dir /absolute/path/to/project/.annotate`. It prints one line per check.

## Optional: a shared review in Redline Cloud

To work on a review in Redline Cloud instead, the user creates an agent token in the review's Agent tab at https://app.redlinejs.com. Add both values to the entry:

```json
"env": {
  "REDLINE_REVIEW": "rvw_…",
  "REDLINE_INVITE": "agt_…"
}
```

Treat the agent token like a password.
