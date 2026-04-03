# Prompt Library

> Copy-paste prompts for common tasks. Send these directly to your OpenClaw agent.

---

## Research

### Company Deep Dive
```
Deep research on [COMPANY NAME]:
- What do they do exactly? How do they make money?
- Revenue, funding, team size if available
- Key people (CEO, founders)
- Recent news (last 6 months)
- Competitors
- Only verified facts, don't make stuff up
```

### Person Research
```
Research [FULL NAME]:
- Current role and company
- LinkedIn profile
- Previous experience
- Any public content (tweets, articles, podcasts)
- Mutual connections or relevance to my business
```

### Market/Industry Research
```
Give me a breakdown of the [INDUSTRY] market:
- Market size and growth rate
- Top 5 players and their market share
- Business models used
- Recent trends (last 12 months)
- Opportunities and threats
- Only cite real data, no speculation
```

---

## Email

### Triage Inbox
```
Check my inbox for unread emails. For each:
- URGENT: needs reply today → draft a reply
- ACTION: needs reply this week → one-line summary + what I need to do
- FYI: just summarize in one line
- SKIP: spam/marketing
```

### Draft Cold Outreach
```
Draft a cold email to [NAME] at [COMPANY].
Context: [WHY YOU'RE REACHING OUT]
Style: short, casual, peer-to-peer. No pitch paragraphs. Max 4-5 sentences.
Include my calendar link: [LINK]
```

### Draft Follow-Up
```
Draft a follow-up to [NAME] who I emailed [X days] ago about [TOPIC].
Keep it short. Reference the original email. Don't be pushy.
```

### Reply to Email
```
Here's an email I received:
[PASTE EMAIL]

Draft a reply. Tone: [professional/casual/direct]. Key points to hit: [LIST].
Show me the draft before sending.
```

---

## Calendar & Scheduling

### Daily Schedule
```
What's on my calendar today? List all events with times, attendees, and any prep needed.
```

### Find Meeting Time
```
I need to schedule a [DURATION] meeting with someone in [THEIR_TIMEZONE].
Check my calendar for open slots this week and suggest 3 options.
```

### Meeting Prep
```
I have a call with [NAME] from [COMPANY] at [TIME].
Research them and create a prep brief:
- Who they are, what they do
- Their company background
- What we might discuss
- Questions I should ask
```

---

## Writing

### Rewrite in My Voice
```
Rewrite this in my voice (direct, concise, no fluff):
[PASTE TEXT]
```

### Summarize
```
Summarize this in 3-5 bullet points. Only the key takeaways:
[PASTE TEXT OR URL]
```

### Expand an Idea
```
I have a rough idea: [DESCRIBE IDEA]
Flesh it out into a structured document:
- Problem it solves
- How it works
- Who it's for
- Why now
- What's needed to build it
```

### LinkedIn Post
```
Write a LinkedIn post about [TOPIC].
Style: conversational, not cringe. No hashtag spam. No "I'm humbled."
Hook in the first line. Value in the middle. CTA at the end.
Max 150 words.
```

---

## Data & Analysis

### Spreadsheet Analysis
```
Pull data from Google Sheet [SHEET_ID], tab [TAB_NAME].
Calculate: [WHAT YOU NEED — totals, averages, trends, comparisons]
Format as a clean report.
```

### Compare Options
```
Compare these options for me:
Option A: [DESCRIBE]
Option B: [DESCRIBE]
Option C: [DESCRIBE]

Criteria: [WHAT MATTERS — cost, speed, quality, risk]
Give me a clear recommendation with reasoning.
```

### Financial Quick Math
```
Calculate:
- [DESCRIBE THE MATH]
Show your work. Double-check the numbers.
```

---

## Automation Setup

### Create a Daily Check
```
Set up a daily cron job at [TIME] [TIMEZONE] that:
1. [FIRST THING TO CHECK]
2. [SECOND THING TO CHECK]
3. Send me the results on [CHANNEL]
Test it once manually first before scheduling.
```

### Create a Reminder
```
Remind me in [TIME]: [WHAT TO REMEMBER]
```

### Create a Recurring Report
```
Every [Monday/day/week] at [TIME], pull [DATA SOURCE], calculate [METRICS], and send me a formatted report.
```

### Monitor Something
```
Check [WHAT TO MONITOR] every [FREQUENCY].
Alert me only if: [CONDITION].
Otherwise stay quiet.
```

---

## Lead Gen & Outreach

### Enrich Leads
```
For each of these leads, find their professional email and company info:
[PASTE LIST: name + company or LinkedIn URL]
Output as: name, email, company, role, website
```

### Build a Prospect List
```
Find [NUMBER] [ROLE/TITLE] at [TYPE OF COMPANY] in [LOCATION/INDUSTRY].
For each: name, company, role, email, LinkedIn, company website.
Use web search + enrichment tools.
```

### Score Leads
```
Here's a list of leads. Score each 1-5 based on:
- Company size/revenue fit
- Role relevance
- Industry match
- Engagement signals (recent activity, content)

[PASTE LIST]
```

---

## Content Processing

### Analyze a PDF
```
Read this PDF and tell me:
- Key points and takeaways
- Any obligations or deadlines
- Anything unusual or risky
- One-paragraph summary
```

### Transcribe & Summarize
```
Transcribe this audio/video and give me:
- Full transcript
- Key points summary (5 bullets)
- Action items mentioned
- Any decisions made
```

### Extract from Image
```
[SEND IMAGE]
Extract all text from this image. If it's a receipt/invoice: pull vendor, amount, date, items.
```

---

## System & Maintenance

### Check Everything
```
Run a full health check:
- Gateway status
- Channel connections
- Memory files (any corruption?)
- Cron jobs running
- Last heartbeat status
Report anything that needs attention.
```

### Clean Up
```
Review the workspace:
- Any temp files to delete?
- Memory files that need consolidation?
- Outdated info in MEMORY.md?
Clean up what you can, show me what needs my decision.
```

### Backup
```
Commit and push the workspace to git. Include a meaningful commit message about what changed today.
```
