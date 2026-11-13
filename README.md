# Substack MCP server for ChatGPT and Claude

**Yes, ChatGPT, Claude and other AI agents can work in your Substack account.** Use Cotlet.dev to publish posts, schedule Notes, manage subscribers and read publication stats in your signed-in account.

## What you can ask

> Schedule a Markdown newsletter draft for Friday at 9:00 AM.

> Publish a Note tomorrow at 10:00 AM with this text and ask before saving.

> Show subscriber growth and revenue for the last 30 days.

> List comments on my latest post and draft replies without sending them.

> Create a weekly report of posts, Notes and subscriber changes.

## Connect in 2 minutes

Get your API key at https://cotlet.dev/app and export it as `COTLET_API_KEY`.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

### ChatGPT

Settings → Apps → add connector URL `https://cotlet.dev/mcp/v1`, then sign in with Cotlet.dev. No API key needed.

### Cursor

Copy `.cursor/mcp.json` from this repo into your project:

```json
{
  "mcpServers": {
    "cotlet": {
      "url": "https://cotlet.dev/mcp/v1",
      "headers": {
        "Authorization": "Bearer ${COTLET_API_KEY}"
      }
    }
  }
}
```

## Run it locally (stdio)

This repo is a real MCP server. It speaks MCP over stdio, lists its Substack tools without any key, and forwards every tool call to the hosted Cotlet.dev server. The hosted URL above stays the recommended way. Use this when your client only starts local servers.

```json
{"mcpServers":{"substack":{"command":"uvx","args":["substack-mcp"],"env":{"COTLET_API_KEY":"your-key"}}}}
```

Or with Docker:

```bash
docker build -t substack-mcp .
docker run -i --rm -e COTLET_API_KEY substack-mcp
```

Tools: `substack_guide`, `substack_status`, `substack_open`, `substack_read`, `substack_click`, `substack_type`, `substack_close`. Without `COTLET_API_KEY` a tool call answers: "Set COTLET_API_KEY. Get a key at https://cotlet.dev/app".

## Command line

Python 3.10+, nothing else to install. From PyPI:

```bash
pip install substack-mcp        # or run it once: uvx substack-mcp connect
substack-mcp connect
```

Or straight from this repo:

```bash
export COTLET_API_KEY="your-key"
python3 substack_agent.py connect   # ready config for Claude Code, Cursor, ChatGPT
python3 substack_agent.py status    # is Substack ready in your Cotlet.dev account
python3 substack_agent.py guide     # Substack screens and sign-in link
python3 substack_agent.py read      # open Substack, read the page, close the browser
python3 substack_agent.py tools     # list Cotlet.dev tools
python3 substack_agent.py serve     # start the stdio MCP server (same as no argument)
```

## Questions from Substack users

**How do I connect Substack to ChatGPT or Claude?**

Sign up at cotlet.dev, log in to Substack once in its Cloud Browser and add Cotlet to ChatGPT or Claude, following the steps in your console. No API key is needed. ChatGPT and Claude then work in your Substack account through Cotlet, in their phone apps too.

**What can ChatGPT do in Substack?**

With Cotlet, ChatGPT, Claude and other AI agents handle jobs like these in Substack: schedule newsletter posts from a Markdown draft; post a Note when a new post goes out; weekly subscriber growth and revenue report. You ask in plain words, and anything that reaches other people waits for your OK first.

**Can it schedule Notes as well as posts?**

Yes. publish_note takes a publish_at time and the Note waits in your scheduled Notes until then. Substack added Notes scheduling in its apps; see On Substack, linked in the sources.

**Is the MCP server different from the REST API?**

Only the way in. Both have the same 50 tools, inputs and answers, one API key and one price.

## Find it on

- [Substack on Cotlet.dev](https://cotlet.dev/integrations/substack-api-mcp-server)
- [All Cotlet packages](https://github.com/cotlet-connect)

---

More: https://cotlet.dev/integrations/substack-api-mcp-server?utm_source=github&utm_medium=readme&utm_campaign=substack

Request access: https://cotlet.dev/signup?app=substack&utm_source=github&utm_medium=readme&utm_campaign=substack
