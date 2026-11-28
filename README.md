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
| Notes from your post | "Write 5 Notes from my latest post and schedule them over the week. Show me first." | After each post |
| Why the numbers moved | "Why did my open rate drop this month? Compare opens, new subscribers and traffic sources with last month." | Monthly |
| Stats in a spreadsheet | "Put each post's opens, views and new subscribers from this year in a spreadsheet." | Your own endpoint, called from Google Sheets |
| Comments you have not answered | "List comments on my last 3 posts that I have not answered and reply to each after my OK." | When you ask |
| Share a post on social | "Write a LinkedIn post and an X post for today's Substack post, with its link." | After each post |
| Clean up the list | "Tag subscribers with the lowest activity rating as inactive. Show me how many first." | Monthly |
| Backup | "Download my subscriber list and all my posts as a backup." | Monthly |
| Leaving Substack | "Export my posts and my subscribers with their plan for my move to Ghost." | Once |

Your AI asks you before it publishes, posts a Note, replies or changes anything. Payments stay in your hands: Cotlet refuses payments.

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

## What you can ask your AI to do in Substack

These are real jobs Substack users asked about. Cotlet lets ChatGPT, Claude or any MCP client do them in your own signed-in Substack account.

### Subscribers and lists

#### How do I automatically sync my Substack subscribers to MailerLite, Kit or beehiiv without exporting a CSV every time?

Partly. Substack has no native sync to other tools, only a CSV export on the Subscribers page and an embeddable signup form. With Cotlet, your agent reads new subscribers in your signed-in Substack and adds them to MailerLite, Kit, beehiiv or your CRM, signed in in the same Cloud Browser. Nothing is added until you say yes.

Ask your agent: "Read the Substack subscribers added this week and add each one to my MailerLite list with email and signup date. Show me the list first."

As an API endpoint: Yes. Save the new-subscriber read as an endpoint in Cotlet and call it daily from n8n, Make or Zapier to push the people into your other tool.

#### How do I connect Substack to Zapier or Make so a new subscriber from my website form is added to my Substack list?

Partly. Substack has no Zapier or Make app and no public API for adding subscribers; you add them by hand or upload a CSV on the Subscribers page. With Cotlet, your agent takes each new form signup and adds it there. Nothing is added until you say yes.

Ask your agent: "Take today's new signups from my website form sheet and add each email as a Substack subscriber. Show me the emails before you add them."

As an API endpoint: Yes. Save the add-subscriber step as an endpoint in Cotlet and call it from n8n, Make or Zapier whenever your form gets a new signup.

#### Can AI clean my Substack subscriber list and remove inactive free subscribers in bulk?

Partly. Substack's Subscribers page can already prune inactive or undeliverable readers with its own filters. With Cotlet, your agent applies those filters, lists the free subscribers it would remove and removes them one by one. Nothing is removed until you say yes.

Ask your agent: "List free Substack subscribers who have not opened an email in 6 months and show me the count and names. Remove nobody yet."

As an API endpoint: Yes. Save the inactive-subscriber list as an endpoint in Cotlet and call it monthly from n8n, Make or a script to get a review list.

#### How do I import 2,000+ subscribers from Mailchimp or Sendy into Substack when the upload keeps being rejected?

Partly. Substack imports lists by upload on the Subscribers page, and publishers report large Mailchimp files being rejected. With Cotlet, your agent reads your export, trims it to the email column and uploads it in Substack, then reports what was accepted. Nothing is imported until you say yes.

Ask your agent: "Read this Mailchimp export, keep only the email column and upload it to Substack as new subscribers. Tell me how many were accepted."

#### Can an AI agent set up a welcome email series for new Substack subscribers?

Partly. Substack lets you set one welcome email for free subscribers and one for paid, in Settings under Emails, not a series. With Cotlet, your agent writes and fills in those two emails and drafts the follow-up emails you can schedule as posts. Nothing changes until you say yes.

Ask your agent: "Write a welcome email for free subscribers and one for paid subscribers and put them into my Substack welcome email settings. Show me the text first."

### Posts and drafts

#### Can Claude or ChatGPT create a draft post in my Substack from a Google Doc and leave it for me to review?

Yes. Substack's editor has no import from Google Docs or Markdown. With Cotlet, your agent reads the Google Doc and builds a draft in your signed-in Substack with the headings, links and images. It stays a draft until you say yes.

Ask your agent: "Read my Google Doc called Newsletter 12 and create a Substack draft from it. Do not publish or schedule it."

As an API endpoint: Yes. Save the draft step as an endpoint in Cotlet and call it from n8n, Make or a script whenever a new Google Doc is ready.

#### How do I bulk import my old blog posts (WordPress, Notion, TinyLetter Google Docs) into Substack with images?

Partly. Substack imports posts from some platforms on its own, in Settings under Import. For the rest, your agent with Cotlet opens each old post and rebuilds it as a Substack draft with its images, one at a time. Nothing is created until you say yes to the first batch.

Ask your agent: "Take my last 10 WordPress posts and create a Substack draft for each, with the images. Show me the first one before you do the rest."

#### How do I connect WordPress to Substack so a new blog post is also published or emailed on Substack?

Partly. Substack has no public API for posts, so a new WordPress post cannot arrive on its own. With Cotlet, your agent reads the new post in WordPress and creates the Substack draft, both signed in in your Cloud Browser. Nothing is published or emailed until you say yes.

Ask your agent: "Take my newest WordPress post and create a Substack draft with the same title, text and images. Do not send it."

As an API endpoint: Yes. Save the copy step as an endpoint in Cotlet and call it from n8n or Make when a new WordPress post goes live.

#### Can Claude read my Substack archive and tell me which old posts to update for SEO?

Yes. With Cotlet, Claude reads your published posts and the Analytics section of your signed-in Substack, including traffic sources, and lists the old posts worth refreshing with the changes to make. Nothing is edited in Substack.

Ask your agent: "Read my last 40 Substack posts and their traffic sources and list the 5 old posts most worth updating for search, with what to change."

As an API endpoint: Yes. Save the archive read as an endpoint in Cotlet and call it every quarter from n8n, Make or a script.

### Notes and social

#### Can an AI agent write and schedule a week of Substack Notes from my latest post?

Yes. Substack lets you schedule a Note from the three dots in the Notes composer, one at a time. With Cotlet, your agent reads your latest post, drafts the Notes and schedules each one in your signed-in Substack. Nothing is scheduled until you say yes.

Ask your agent: "Read my latest Substack post and write 7 Notes, one per day. Show me the list, then schedule them at 9:00 after I say yes."

As an API endpoint: Yes. Save the Notes scheduling as an endpoint in Cotlet and call it every Monday from n8n, Make or Zapier.

#### How do I connect Substack to LinkedIn, X and Facebook so every new post is shared without my doing it by hand?

Partly. Substack lets you edit the social preview of a post and connects YouTube for auto-upload, but it has no native auto-share to LinkedIn, X or Facebook. With Cotlet, your agent reads the new post and drafts the post for each network. Nothing is published until you say yes.

Ask your agent: "Read my newest Substack post and write a LinkedIn post, an X post and a Facebook post that link to it. Do not publish them."

As an API endpoint: Yes. Save the draft step as an endpoint in Cotlet and call it from n8n, Make or Zapier when a post goes live.

### Stats, revenue and tax

#### Can AI tell me why my Substack open rate or subscriber growth dropped, using my own analytics?

Yes. Substack's own AI connection is read-only and only for Bestseller publications. With Cotlet, your agent reads the Analytics section, Sources and retention in your signed-in Substack, compares recent weeks and names what changed, such as a post type or a traffic source.

Ask your agent: "Compare my Substack open rate and new subscribers for the last 4 weeks with the 4 before and tell me what changed and which posts drove it."

As an API endpoint: Yes. Save the weekly comparison as an endpoint in Cotlet and call it every Monday from n8n, Make or Zapier.

#### How do I export my Substack analytics (opens, clicks, traffic sources) into Excel or Google Sheets?

Yes. Substack shows post stats and Analytics in the dashboard, with no automatic sheet sync. With Cotlet, your agent reads opens, clicks and sources per post in your signed-in Substack and adds them as rows to your Google Sheet. Nothing is added until you say yes.

Ask your agent: "Read opens, clicks and top traffic source for each Substack post from this month and add one row per post to my Google Sheet."

As an API endpoint: Yes. Save the stats read as an endpoint in Cotlet and call it every Monday from n8n, Make or Zapier to update the sheet.

#### How do I connect Substack to Stripe reporting or my own dashboard to see paid subscribers and churn in one place?

Partly. Substack shows paid subscribers and retention in its dashboard, and payments run through your Stripe account. With Cotlet, your agent reads both screens, signed in in the same Cloud Browser, and gives you one table of paid subscribers, cancellations and revenue. Nothing changes in either app.

Ask your agent: "Read my Substack paid subscribers and cancellations for this month and my Stripe payments, and give me one table with paid count, churn and revenue."

As an API endpoint: Yes. Save the combined read as an endpoint in Cotlet and call it monthly from n8n, Make or a script to feed your own dashboard.

#### How do I automate VAT and sales tax for my Substack paid subscribers in the EU and UK?

Not on its own. Substack says that from December 1, 2026 it calculates, collects and remits VAT and sales tax itself. With Cotlet, your agent reads your Payments settings and the tax notices in your Substack account and sums up what applies to you. Tax advice stays with your accountant.

Ask your agent: "Read my Substack Payments settings and tax notices and tell me in plain words what Substack handles for VAT and what I still have to do."

### Backups and moving

#### How do I automatically back up all my Substack posts, images and subscriber list every month?

Yes. Substack has Settings, Exports for your posts and an Export button on the Subscribers page, both by hand. With Cotlet, your agent starts a new export in your signed-in Substack, downloads the file and the subscriber CSV and saves them for you.

Ask your agent: "Start a new Substack export, download the posts zip and the subscriber CSV and tell me when both files are saved."

As an API endpoint: Yes. Save the backup run as an endpoint in Cotlet and call it on the first of each month from n8n, Make or a script.

#### How do I move my paid subscribers from Substack to Ghost or WordPress without losing them?

Partly. Substack exports your lists and posts, and the importer sits on the new platform. Paid subscriptions live in Stripe, so the move needs Stripe and the new platform. With Cotlet, your agent exports the lists and checks what arrived. Cotlet does not move payments.

Ask your agent: "Export my Substack subscribers and posts, then check the Ghost import and list any paid subscriber that did not arrive."

#### How do I move my podcast between Substack and another host and keep my RSS feed and episodes?

Partly. Substack imports an existing podcast from its RSS URL, in the Podcast section of the dashboard. With Cotlet, your agent starts that import in your signed-in Substack and checks that the episodes came across. Nothing is imported until you say yes.

Ask your agent: "Import my podcast into Substack from this RSS URL and list the episodes that came across."

### Readers and AI

#### Can ChatGPT answer questions from the content of my Substack, so readers can ask it?

Not on its own. Cotlet is not a public chat widget for your readers. For you, ChatGPT or Claude reads your published posts in your signed-in Substack and answers from them, for example to find what you wrote on a topic.

Ask your agent: "Read my published Substack posts and tell me what I have written about pricing, with the post titles and dates."

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

Yes. It writes Notes from your post and schedules them at the times you choose, after you approve the text.

**Can AI tell me why my open rate or subscriber growth dropped?**

Yes. It reads your post stats, daily stats and traffic sources in your account and compares two periods in plain words.

**How do I export all my Substack posts and subscribers?**

Substack exports them by hand in Settings. Your AI can download the subscriber CSV and your posts for you, and a monthly backup can run from your own endpoint.

**Can I connect Substack to Google Sheets?**

Yes. Build a `post-stats` or `publication-stats` endpoint and call it from Google Sheets, or from a Zapier or Make step that writes the rows.

**Can I move my subscribers to Ghost, beehiiv or Kit?**

Your AI exports your posts and the subscriber list with each person's plan, in the files those importers take. Stripe and paid subscriptions stay in your hands.

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
