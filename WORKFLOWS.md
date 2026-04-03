# Workflows — Ready-to-Use Automations

> Each workflow below is something you can set up and have running within minutes. Copy the relevant section into your AGENTS.md or HEARTBEAT.md, or just tell your agent to do it.

---

## 1. Morning Briefing

**What:** Every morning, your agent checks email, calendar, and key metrics, then sends you a summary.

**Setup:**
```bash
openclaw cron add \
  --schedule "0 8 * * *" \
  --task "Morning briefing: 1) Check my email for anything urgent from the last 12 hours. 2) List today's calendar events. 3) Send me the summary on Telegram." \
  --tz "YOUR_TIMEZONE"
```

**Requires:** Gmail API access via `gog` CLI (install: `openclaw skills install gog`)

**Gmail setup:**
```bash
# Install the Google Workspace skill
openclaw skills install gog

# Authenticate (opens browser for OAuth)
gog auth login
```

---

## 2. Email Triage

**What:** Agent reads your unread emails, categorizes them (urgent / needs reply / FYI / spam), and drafts replies for the urgent ones.

**Prompt to send your agent:**
```
Check my inbox. For each unread email:
- If urgent or needs a reply today: draft a reply in my voice and show it to me
- If FYI only: summarize in one line
- If spam/marketing: skip it
Group them by category.
```

**Add to AGENTS.md for ongoing behavior:**
```markdown
## Email Rules
- Never send emails without my explicit approval
- Draft replies in my voice (see writing samples in MEMORY.md)
- Urgent = from clients, partners, or team with time-sensitive requests
- FYI = newsletters, notifications, updates
```

---

## 3. Calendar Management

**What:** Agent checks your calendar before any meeting, prepares context, and warns about conflicts.

**Setup:**
```bash
openclaw skills install gog
gog auth login  # for calendar access
```

**Add to HEARTBEAT.md:**
```markdown
- Check calendar for events in the next 2 hours
- If there's an upcoming meeting: research the attendees/company and prepare a brief
- If there's a conflict: alert me immediately
```

**Add to AGENTS.md:**
```markdown
## Calendar Rules
- Always check calendar before confirming any meeting time
- Convert timezones properly (I'm in [YOUR_TIMEZONE])
- If someone asks for a meeting, suggest my booking link: [YOUR_LINK]
```

---

## 4. Research Pipeline

**What:** Deep research on a company, person, or topic — delivered as a structured brief.

**Prompt:**
```
Deep research on [COMPANY/PERSON/TOPIC]:
1. Search the web for recent info (last 6 months)
2. Check their website, LinkedIn, recent news
3. Summarize: what they do, how they make money, key people, recent developments
4. Flag anything relevant to my business
Save the research to a file and send me the summary.
```

**For recurring competitive monitoring, create a cron:**
```bash
openclaw cron add \
  --schedule "0 9 * * 1" \
  --task "Research [COMPETITOR]: check their website, social media, and recent news for any changes this week. Compare to last week's research in memory/. Send me only what's new." \
  --tz "YOUR_TIMEZONE"
```

---

## 5. Lead Enrichment

**What:** Take a list of leads (names + companies or LinkedIn URLs) and enrich with emails, roles, company info.

**Setup:**
```bash
# Get API keys from these services and save them:
mkdir -p ~/.config/prospeo ~/.config/findymail

# Findymail (https://findymail.com) — better for European contacts
echo "YOUR_FINDYMAIL_KEY" > ~/.config/findymail/api_key
```

**Prompt:**
```
I have a list of leads. For each one:
1. Find their professional email using Prospeo or Findymail
2. Look up their company website
3. Get their role/title
4. Output as a CSV: name, email, company, role, website, linkedin

Here's the list:
[PASTE NAMES + COMPANIES OR LINKEDIN URLS]
```

---

## 6. Outreach Drafting

**What:** Agent drafts cold outreach emails based on your style and the lead's context.

**Add to AGENTS.md:**
```markdown
## Outreach Style
- Short. Casual. Peer-to-peer. No pitch paragraphs.
- Name-drop referrer if there is one
- One specific reason for reaching out.
- Clear CTA (usually a call link)
- Max 4-5 sentences total
- Example: "Hi [Name], are you currently buying leads? We work with [big business name] and looking for new partner.We can supply X-Y leads per day with avg cost per "sale" at ABC. Let me know please."
```

**Prompt:**
```
Draft outreach emails for these leads. Research each company first so the email is specific, not generic. Use my outreach style from AGENTS.md.

[PASTE LEAD LIST]
```

---

## 7. Spreadsheet Reports

**What:** Pull data from Google Sheets and format it into a report.

**Setup:**
```bash
openclaw skills install gog
gog auth login  # for Sheets access
```

**Prompt:**
```
Pull data from Google Sheet [SHEET_ID], tab "[TAB_NAME]".
Calculate: [METRICS YOU WANT — e.g., total revenue, average CPA, week-over-week change]
Format as a daily report and send it to me.
```

**For daily automated reports:**
```bash
openclaw cron add \
  --schedule "0 7 * * *" \
  --task "Pull data from Google Sheet [SHEET_ID] tab [TAB]. Calculate [METRICS]. Format as daily report. Send to me on Telegram." \
  --tz "YOUR_TIMEZONE"
```

---

## 8. Content & Social Monitoring

**What:** Monitor specific accounts, hashtags, or topics for mentions and opportunities.

**Prompt:**
```
Search the web for mentions of [YOUR_BRAND/NAME/TOPIC] in the last 24 hours.
Check Twitter/X, LinkedIn, Reddit, and news sites.
Summarize anything worth knowing. Ignore noise.
```

**As a daily cron:**
```bash
openclaw cron add \
  --schedule "0 10 * * *" \
  --task "Search for mentions of [BRAND] in the last 24 hours across web, Twitter, Reddit. Only alert me if something is worth acting on." \
  --tz "YOUR_TIMEZONE"
```

---

## 9. Meeting Prep

**What:** Before any call, agent researches the person/company and creates a prep note.

**Prompt:**
```
I have a call with [NAME] from [COMPANY] in 1 hour.
1. Research them: LinkedIn, company website, recent news
2. Find what they do, their role, company revenue/size if available
3. Check our previous interactions (search memory files)
4. Create a prep note with: background, talking points, questions to ask
```

**To automate for all calendar events:**
Add to HEARTBEAT.md:
```markdown
- Check calendar for meetings in the next 2 hours
- For any meeting with external people: auto-generate a prep brief
- Save to memory/call-prep/[date]-[name].md
```

---

## 10. Browser Dashboard Checks

**What:** Agent logs into web dashboards, takes screenshots, and reports metrics.

**Prompt:**
```
Open [URL] in the browser, log in with my saved credentials, navigate to the dashboard, take a screenshot, and send it to me. Also extract the key numbers: [DESCRIBE WHAT METRICS TO PULL].
```

**Note:** Browser automation works best with the OpenClaw browser tool. The agent can click, type, screenshot, and extract data from any web page.

---

## 11. File & Document Processing

**What:** Agent processes PDFs, documents, images.

**Prompts:**
```
# Analyze a contract
Read this PDF and summarize: key terms, obligations, deadlines, and anything unusual.

# Process an invoice
Extract: vendor name, amount, due date, line items. Add to my expense tracking sheet.

# Analyze an image
[Send photo] What's in this image? Extract any text.
```

---

## 12. Slack/Team Monitoring

**What:** Monitor Slack channels for important messages and surface them.

**Setup:**
```bash
openclaw config set channels.slack.enabled true
openclaw config set channels.slack.mode socket
openclaw config set channels.slack.appToken "xapp-..."
openclaw config set channels.slack.botToken "xoxb-..."
```

**Add to HEARTBEAT.md:**
```markdown
- Check Slack channels [#channel1, #channel2] for messages in the last hour
- If anything needs my attention (questions directed at me, urgent issues, decisions needed): alert me on Telegram
- Ignore casual conversation
```
