# Substack API integration and MCP server: connect Substack to ChatGPT, Claude and any AI agent

Substack API integration and MCP server, unofficial, by Cotlet. Your AI works in your own signed-in Substack account: posts, drafts, Notes, subscribers, comments and stats, on the same screens your login opens. Cotlet has 50 ready Substack tools for AI agents, and the Cloud Browser does the rest. No developer and no Substack API key needed. Cotlet is independent and is not affiliated with Substack.

**Does Substack have an API or an MCP server?** Substack has an official MCP server, but it only reads (stats, traffic, revenue, retention), works only for Bestseller publications where you are an admin, and cannot publish posts or send Notes (Substack help center, checked 22 Nov 2026). Its Developer API returns public creator profile data after approval, with no posts, drafts or subscribers. Substack has no Zapier app and no Make app.

## What writers ask their AI to do in Substack

| Job | Ask your AI | Make it repeat |
|---|---|---|
| Draft from a Google Doc | "Turn this Google Doc into a Substack draft with its headings and images. Do not publish." | When a post is ready |
| Old posts into Substack | "Copy my archived posts from these Google Docs into Substack drafts, one per post, with the images." | Once |
| Website signups into Substack | "Add the people who signed up on my website form today as free subscribers. Show me the list first." | Your own endpoint, called from your form tool |
| Subscribers to MailerLite or Kit | "List this week's new free and paid subscribers so I can add them to MailerLite." | Your own endpoint, called from Zapier, Make or n8n |
| Notes from your post | "Write 5 Notes from my latest post and show them to me. Post the one I pick at the time I choose." | After each post |
| Why the numbers moved | "Why did my open rate drop this month? Compare opens, new subscribers and traffic sources with last month." | Monthly |
| Stats in a spreadsheet | "Put each post's opens, views and new subscribers from this year in a spreadsheet." | Your own endpoint, called from Google Sheets |
| Comments you have not answered | "List comments on my last 3 posts that I have not answered and draft a reply for each. Post nothing." | When you ask |
| Share a post on social | "Write a LinkedIn post and an X post for today's Substack post, with its link." | After each post |
| Clean up the list | "Tag subscribers with the lowest activity rating as inactive. Show me how many first." | Monthly |
| Backup | "Download my subscriber list and all my posts as a backup." | Monthly |
| Leaving Substack | "Export my posts and my subscribers with their plan for my move to Ghost." | Once |

Your AI asks you before it publishes, posts a Note, replies or changes anything. It does not auto-reply and does not post in bulk: Substack's Terms of Use forbid auto-responders and spam. Payments stay in your hands: Cotlet refuses payments.

## Your own Substack API, built with Cotlet

Substack gives you no API for posts, drafts or subscribers. With Cotlet you build your own: your AI does the job once in your Cloud Browser, you save it as an endpoint, and you call it from Google Sheets, Webflow or WordPress forms, Zapier, Make, n8n, your own code.

Examples you can build in a few minutes (none of them exists until you build it):

| Endpoint you build | What it returns or does |
|---|---|
| `new-subscribers` | subscribers who joined since a date, with free or paid plan |
| `add-subscriber` | adds one email to your list as a free subscriber (writes only after you allow it) |
| `post-stats` | opens, open rate, views and new subscribers per post for a period |
| `publication-stats` | total and paid subscribers, cancellations and estimated revenue for a period |
| `traffic-sources` | where your visitors came from in a period |
| `unanswered-comments` | comments on your posts that have no reply from you |
| `export-subscribers` | the full subscriber list as a CSV link |
| `create-draft` | a draft from your Markdown or HTML; nothing is sent (writes only after you allow it) |

Call it with your Cotlet API key, JSON in and JSON out:

```bash
curl -X POST https://cotlet.dev/api/v1/custom/new-subscribers \
  -H "Authorization: Bearer $COTLET_API_KEY" -H "Content-Type: application/json" -d '{}'
```

Limits: one call runs up to 90 seconds, writes wait for your consent, payments are blocked. Start here: https://cotlet.dev/app/custom-api/new?app=substack

## Connect in 2 minutes

Sign up at https://cotlet.dev, sign in to Substack once in your Cloud Browser, then add Cotlet to your AI.

### Claude

Settings → Connectors → Add custom connector, paste `https://cotlet.dev/mcp/v1` and sign in with Cotlet.dev.

### ChatGPT

Open Plugins in ChatGPT, choose Create as a plugin, paste `https://cotlet.dev/mcp/v1` and sign in with Cotlet.dev. No API key needed.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

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

## Questions from Substack writers
**Does Substack have an API?**

A narrow one. The Substack Developer API (terms updated 8 Jan 2026) returns public creator profile data after approval. It has no endpoints for posts, drafts or subscribers. With Cotlet you build your own endpoints that work in your signed-in Substack account.

**Is there an official Substack MCP server for ChatGPT or Claude?**

Yes, read-only. Substack's MCP server reads stats, traffic, revenue and retention, only for Bestseller publications where you are an admin. It cannot publish posts or send Notes. Cotlet works in your own Substack account on any plan and also does the writing side, after your OK.

**How do I connect Substack to Zapier or Make so a website signup is added to Substack?**

Substack has no Zapier or Make app. Build an `add-subscriber` endpoint with Cotlet and call it from Zapier, Make, n8n or your form tool. Each new email is added as a free subscriber.

**How do I sync Substack subscribers to MailerLite, Kit or beehiiv without exporting a CSV every time?**

Build a `new-subscribers` endpoint and call it from Zapier, Make or n8n on a schedule. It returns the people who joined since a date, with their free or paid plan, ready to add in your email tool.

**Can Claude or ChatGPT create a draft in Substack from a Google Doc?**

Yes, with Cotlet. Your AI reads the doc and saves a Substack draft with its headings and images. Nothing is published until you say so.

**Can AI write and schedule Substack Notes?**

It writes Notes from your post and posts the one you pick, now or at the time you choose. It does not post Notes on its own.

**Can AI tell me why my open rate or subscriber growth dropped?**

Yes. It reads your post stats, daily stats and traffic sources in your account and compares two periods in plain words.

**How do I export all my Substack posts and subscribers?**

Substack exports them by hand in Settings. Your AI can download the subscriber CSV and your posts for you, and a monthly backup can run from your own endpoint.

**Can I connect Substack to Google Sheets?**

Yes. Build a `post-stats` or `publication-stats` endpoint and call it from Google Sheets, or from a Zapier or Make step that writes the rows.

**Can I move my subscribers to Ghost, beehiiv or Kit?**

Your AI exports your posts and the subscriber list with each person's plan, in the files those importers take. Stripe and paid subscriptions stay in your hands.

**Is this allowed by Substack's terms?**

Cotlet works in your own signed-in Substack session and does what you ask, one task at a time, with your OK before anything is posted. Substack's Terms of Use (6 Oct 2026) forbid auto-responders, spam and crawling, so do not use it for auto-replies, bulk Notes or copying other writers' posts.

## Find it on

- [Substack MCP server on Cotlet.dev](https://cotlet.dev/integrations/substack-api-mcp-server?utm_source=github&utm_medium=readme&utm_campaign=substack&utm_content=find)
- [PyPI package substack-mcp](https://pypi.org/project/substack-mcp/)

## More newsletter and creator apps on Cotlet

- [Medium MCP](https://github.com/cotlet-connect/medium-mcp)
- [Ghost MCP](https://github.com/cotlet-connect/ghost-mcp)
- [Patreon MCP](https://github.com/cotlet-connect/patreon-mcp)
- [Quora MCP](https://github.com/cotlet-connect/quora-mcp)
- [Skool MCP](https://github.com/cotlet-connect/skool-mcp)
- [LinkedIn MCP](https://github.com/cotlet-connect/linkedin-mcp)
