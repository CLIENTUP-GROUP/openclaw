# Cron Library — Ready-to-Paste Automations

> Real cron jobs for real business. Each one has the exact command, schedule, what APIs you need, and whether it runs on its own or waits for your OK.

---

## How Cron Jobs Work

OpenClaw cron jobs fire at a set time, run the task, and send you the result on your connected channel (Telegram, WhatsApp, etc.). See [SETUP.md](./SETUP.md) Phase 4 for basics.

### Schedule Cheat Sheet

| Expression | When |
|-----------|------|
| `0 8 * * *` | Every day at 8 AM |
| `0 8 * * 1-5` | Weekdays at 8 AM |
| `0 9 * * 1` | Every Monday at 9 AM |
| `0 */2 * * 1-5` | Every 2 hours on weekdays |
| `0 8,17 * * *` | Twice daily: 8 AM and 5 PM |
| `0 8 1 * *` | First of every month at 8 AM |

### How Approval Works

Every cron below is tagged with one of these:

- **Auto** — runs fully, sends you the result. No input needed.
- **Draft + confirm** — prepares everything, shows it to you, waits for your OK before any external action (sending emails, posting, etc.)
- **Alert only** — checks something, only messages you if there's something worth acting on. Silent otherwise.

---

## 1. Email

> Requires: `gog` CLI for Gmail access

**One-time setup:**
```bash
openclaw skills install gog
gog auth login
```

---

### 1.1 Morning Inbox Scan

**Schedule:** `0 7 * * 1-5` — weekdays at 7 AM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 7 * * 1-5" \
  --task "Check my inbox for unread emails from the last 12 hours. For each: if it's from a client or partner and needs a reply today, draft a reply in my voice and show it to me. If it's FYI, give me one line. Skip spam and marketing. Group by category. Do NOT send anything — just show me the drafts." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A categorized inbox summary + draft replies waiting for your approval.

---

### 1.2 Follow-Up Nudges

**Schedule:** `0 16 * * 1-5` — weekdays at 4 PM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 16 * * 1-5" \
  --task "Check my sent emails from the last 5 days. Find any that got no reply and seem like they should have. Draft a short, casual follow-up for each. Don't be pushy. Show me the drafts — don't send." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Follow-up drafts for emails that fell through the cracks.

---

### 1.3 Weekend Emergency Scan

**Schedule:** `0 10 * * 0,6` — weekends at 10 AM
**Approval:** Alert only

```bash
openclaw cron add \
  --schedule "0 10 * * 0,6" \
  --task "Quick scan of my inbox. Only flag emails from clients, partners, or my team that are genuinely urgent and need a response before Monday. Ignore everything else. If nothing urgent, don't message me." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Nothing, unless something actually needs your attention.

---

### 1.4 End-of-Day Inbox Report

**Schedule:** `0 18 * * 1-5` — weekdays at 6 PM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 18 * * 1-5" \
  --task "End of day email summary: how many emails came in today, how many got replies, what's still pending. Keep it short — 5 lines max." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Quick scorecard so you know where things stand.

---

## 2. Finding Clients

> Requires: Brave Search API, Prospeo, Findymail

**One-time setup:**
```bash
# Brave Search — get key from https://brave.com/search/api/
echo 'export BRAVE_API_KEY="YOUR_KEY"' >> ~/.zshrc && source ~/.zshrc

# Prospeo — get key from https://prospeo.io/api/documentation
mkdir -p ~/.config/prospeo
echo "YOUR_PROSPEO_KEY" > ~/.config/prospeo/api_key

# Findymail — get key from https://www.findymail.com/api/
mkdir -p ~/.config/findymail
echo "YOUR_FINDYMAIL_KEY" > ~/.config/findymail/api_key
```

---

### 2.1 Weekly Prospect List

**Schedule:** `0 9 * * 1` — Mondays at 9 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 9 * * 1" \
  --task "Search the web for 10-15 companies that match my ideal client profile (see USER.md). For each: company name, what they do, website, size/stage if available, and why they might be a fit. Save to a file and send me the list." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A fresh prospect list every Monday.

---

### 2.2 Enrich the List

**Schedule:** `0 10 * * 2` — Tuesdays at 10 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 10 * * 2" \
  --task "Take last Monday's prospect list. For each company, find the right person to contact (founder, CEO, or head of [YOUR_DEPARTMENT]). Get their professional email using Prospeo or Findymail, LinkedIn URL, and role. Output as CSV: name, email, company, role, website, linkedin. Save the file." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Enriched leads with emails, ready for outreach.

---

### 2.3 Spot Opportunities

**Schedule:** `0 11 * * 1-5` — weekdays at 11 AM
**Approval:** Alert only

```bash
openclaw cron add \
  --schedule "0 11 * * 1-5" \
  --task "Search the web for job changes, new funding rounds, and hiring signals at my target companies (see USER.md). Only message me if you find something worth acting on — a warm lead, a trigger event, or a new decision-maker. If nothing interesting, stay quiet." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Pinged only when there's a real opportunity.

---

### 2.4 Score and Rank Leads

**Schedule:** `0 14 * * 3` — Wednesdays at 2 PM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 14 * * 3" \
  --task "Review all leads from this week's prospect list and enrichment. Score each 1-5 based on: company size fit, role relevance, industry match, and any recent signals (funding, hiring, job changes). Rank them. Flag the top 5 for outreach. Save the scored list." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Prioritized list so you spend time on the best leads.

---

## 3. Outreach

> Requires: `gog` CLI (Gmail) + Brave Search API
> Setup: same as Email (section 1) + Finding Clients (section 2)

---

### 3.1 Draft Cold Emails

**Schedule:** `0 8 * * 3` — Wednesdays at 8 AM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 8 * * 3" \
  --task "Take the top-scored leads from this week. Research each company (what they do, recent news, what they might need). Then draft a personalized cold email for each in my voice (see outreach style in AGENTS.md). Short, casual, peer-to-peer. One specific reason for reaching out. Include my calendar link. Show me all drafts — don't send." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Personalized outreach emails ready to review and send.

---

### 3.2 Follow-Up Sequence

**Schedule:** `0 9 * * 1,4` — Mondays and Thursdays at 9 AM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 9 * * 1,4" \
  --task "Check all outreach emails I've sent. For emails with no reply after 3+ days, draft a short follow-up — different angle, still casual. For emails with no reply after 7+ days, draft a final 'no worries either way' breakup email. Show me the drafts — don't send." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Follow-up drafts that don't sound desperate.

---

### 3.3 Warm Intro Requests

**Schedule:** `0 10 * * 5` — Fridays at 10 AM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 10 * * 5" \
  --task "Review my pending leads. For any where a mutual connection exists (check my contacts in memory, LinkedIn connections), draft a warm intro request email to the mutual contact. Keep it easy for them to forward — include a one-liner about what I do and why I want to connect. Show me the drafts — don't send." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Warm intro drafts that make it easy for your contact to make the connection.

---

## 4. Calendar

> Requires: `gog` CLI for Google Calendar + Brave Search API
> Setup: same as Email (section 1)

---

### 4.1 Daily Schedule Briefing

**Schedule:** `0 7 * * 1-5` — weekdays at 7 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 7 * * 1-5" \
  --task "Check my calendar for today. List every event with time, attendees, and location/link. For meetings with external people, do a quick lookup — who they are, their company, what we last talked about (check memory files). Keep it tight." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Your day laid out with context on who you're meeting.

---

### 4.2 Pre-Meeting Prep

**Schedule:** `0 */2 * * 1-5` — every 2 hours on weekdays
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 */2 * * 1-5" \
  --task "Check my calendar for meetings in the next 2 hours. For each meeting with someone outside my team: research the attendees (LinkedIn, company, recent news), check our previous interactions in memory files, and create a prep brief. Include: who they are, what they do, what we might discuss, and questions I should ask. If no external meetings coming up, stay quiet." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Prep notes before every call, automatically.

---

### 4.3 End-of-Day Debrief

**Schedule:** `0 17 * * 1-5` — weekdays at 5 PM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 17 * * 1-5" \
  --task "List all meetings I had today. For each, ask me: 'What were the key takeaways from [meeting name]?' — I'll reply with notes. Save whatever I say to memory/call-notes/[date]-[name].md." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A prompt to capture meeting notes while they're fresh.

---

### 4.4 Weekly Calendar Audit

**Schedule:** `0 9 * * 0` — Sundays at 9 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 9 * * 0" \
  --task "Review my calendar for next week. Flag any conflicts, days with back-to-back meetings and no breaks, and days with nothing scheduled. Suggest where to add focus time. Give me the overview." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A heads-up on your week before it starts.

---

## 5. Writing

> Requires: Brave Search API + `gog` CLI (for sending)
> Setup: same as sections 1 + 2

---

### 5.1 Weekly LinkedIn Post

**Schedule:** `0 8 * * 2` — Tuesdays at 8 AM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 8 * * 2" \
  --task "Based on what I've been working on this week (check memory files, recent meetings, any wins or interesting findings), draft a LinkedIn post in my style. Conversational, not cringe. Hook in the first line. Value in the middle. No hashtag spam. Max 150 words. Show me the draft." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A post draft based on what you're actually doing, not some generic thought leadership BS.

---

### 5.2 Weekly Update / Newsletter

**Schedule:** `0 9 * * 5` — Fridays at 9 AM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 9 * * 5" \
  --task "Compile the week: key things I worked on, client wins, interesting findings, and anything worth sharing. Draft it as a short weekly update I can send to my team or newsletter list. Keep it under 300 words. Show me the draft — don't send." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A weekly recap you can send as-is or tweak.

---

### 5.3 Post-Meeting Client Summary

**Schedule:** `0 18 * * 1-5` — weekdays at 6 PM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 18 * * 1-5" \
  --task "Check if I had any client meetings today (calendar + call notes in memory). For each client meeting, draft a follow-up email summarizing: what we discussed, decisions made, action items, and next steps. Use a professional but warm tone. Show me the drafts — don't send." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Client follow-up emails drafted automatically after every call.

---

## 6. Numbers

> Requires: `gog` CLI for Google Sheets, Gmail, Calendar
> Setup: same as Email (section 1)

---

### 6.1 Daily Metrics Pull

**Schedule:** `0 7 * * 1-5` — weekdays at 7 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 7 * * 1-5" \
  --task "Pull key numbers from Google Sheet [SHEET_ID], tab [TAB_NAME]. Calculate: [YOUR_METRICS — e.g., revenue, new leads, conversion rate, pipeline value]. Format as a clean daily snapshot. Compare to yesterday if possible." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Your key numbers every morning, no dashboard login needed.

---

### 6.2 Weekly Business Review

**Schedule:** `0 8 * * 1` — Mondays at 8 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 8 * * 1" \
  --task "Weekly review: pull numbers from Google Sheet [SHEET_ID]. Calculate this week vs last week: [YOUR_METRICS — revenue, new leads, deals closed, emails sent, meetings held]. Highlight what went up, what went down, and anything that needs attention. Keep it to one screen." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A Monday morning snapshot of how last week went.

---

### 6.3 Monthly Pipeline

**Schedule:** `0 9 1 * *` — first of every month at 9 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 9 1 * *" \
  --task "Monthly pipeline report from Google Sheet [SHEET_ID]: total leads, deals in each stage, conversion rates stage by stage, expected revenue this month, top 5 deals by value. Compare to last month. Flag anything stuck or overdue." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Full pipeline health check once a month.

---

### 6.4 Per-Client Status

**Schedule:** `0 16 * * 5` — Fridays at 4 PM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 16 * * 5" \
  --task "For each active client (check memory + Google Sheet [SHEET_ID]): when did I last interact with them (email, meeting), are there any pending items, any upcoming meetings next week? Flag anyone I haven't talked to in 2+ weeks." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A Friday check so no client falls through the cracks over the weekend.

---

## 7. Staying in Touch

> Requires: `gog` CLI for Gmail, Calendar, Sheets
> Setup: same as Email (section 1)

---

### 7.1 Going-Cold Alert

**Schedule:** `0 9 * * 1` — Mondays at 9 AM
**Approval:** Alert only

```bash
openclaw cron add \
  --schedule "0 9 * * 1" \
  --task "Check my contacts in memory and email history. Find anyone important (clients, partners, key contacts) I haven't interacted with in 30+ days. For each, suggest a re-engagement action — a quick check-in email, a share of something relevant, or a meeting. Only flag VIPs, not everyone." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A nudge before relationships go stale.

---

### 7.2 Post-Meeting Notes Reminder

**Schedule:** `0 18 * * 1-5` — weekdays at 6 PM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 18 * * 1-5" \
  --task "Check today's calendar. For every meeting that happened, check if I saved notes (memory/call-notes/). If any meeting has no notes, remind me to jot down key points. Keep it short." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A reminder so you don't lose what was discussed.

---

### 7.3 Birthdays & Milestones

**Schedule:** `0 8 * * *` — daily at 8 AM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 8 * * *" \
  --task "Check my contacts for birthdays or work anniversaries today or this week (stored in memory or Google Sheet [CONTACTS_SHEET_ID]). If there's one, draft a short personal message. Show me the draft — don't send. If nothing, stay quiet." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Never miss a birthday or work anniversary again.

---

## 8. System

> No external APIs needed — uses built-in OpenClaw commands and Git.

---

### 8.1 Health Check

**Schedule:** `*/30 * * * *` — every 30 minutes
**Approval:** Alert only

```bash
openclaw cron add \
  --schedule "*/30 * * * *" \
  --task "Run openclaw health. Check gateway is running, all channels are connected, heartbeat is active. Only message me if something is down or erroring. Otherwise stay quiet." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Peace of mind. You'll know if something breaks.

---

### 8.2 Daily Backup

**Schedule:** `0 23 * * *` — daily at 11 PM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 23 * * *" \
  --task "Git commit all workspace changes with a message describing what changed today. If there's a remote configured, push. If nothing changed, skip." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Your workspace backed up every night automatically.

---

### 8.3 Weekly Cost Check

**Schedule:** `0 9 * * 0` — Sundays at 9 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 9 * * 0" \
  --task "List all active cron jobs. Check for any that look redundant or are firing too often. Estimate weekly API cost based on how many tasks fire and which model they use. Suggest any optimizations — like switching routine tasks from Opus to Sonnet, or reducing frequency." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** A cost sanity check so things don't get out of hand.

---

### 8.4 Memory Cleanup

**Schedule:** `0 22 * * 5` — Fridays at 10 PM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 22 * * 5" \
  --task "Review this week's daily memory files (memory/YYYY-MM-DD.md). Extract key learnings, patterns, and important facts. Add them to MEMORY.md long-term storage. Show me what you'd consolidate and what you'd archive before doing it." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Clean memory, key stuff preserved, cruft archived.

---

## 9. Slack

> Requires: Slack channel configured in OpenClaw

**One-time setup:**
```bash
openclaw config set channels.slack.enabled true
openclaw config set channels.slack.mode socket
openclaw config set channels.slack.appToken "xapp-..."
openclaw config set channels.slack.botToken "xoxb-..."
```

---

### 9.1 Morning Slack Digest

**Schedule:** `0 8 * * 1-5` — weekdays at 8 AM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 8 * * 1-5" \
  --task "Scan Slack channels [#channel1, #channel2] for messages from the last 12 hours. Surface anything that needs my attention: questions directed at me, decisions being made, urgent issues. Skip casual chat. Keep the digest short." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** Slack catch-up without scrolling through 200 messages.

---

### 9.2 Unanswered Questions

**Schedule:** `0 12,16 * * 1-5` — weekdays at noon and 4 PM
**Approval:** Draft + confirm

```bash
openclaw cron add \
  --schedule "0 12,16 * * 1-5" \
  --task "Check Slack for any direct questions to me that I haven't answered in 2+ hours. Draft a quick response for each. Show me the drafts — don't post." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** No questions left hanging.

---

### 9.3 Weekly Slack Summary

**Schedule:** `0 17 * * 5` — Fridays at 5 PM
**Approval:** Auto

```bash
openclaw cron add \
  --schedule "0 17 * * 5" \
  --task "Summarize the week's key Slack discussions across [#channel1, #channel2]: decisions made, open items, recurring themes. Skip the noise. Keep it under 10 bullet points." \
  --tz "YOUR_TIMEZONE"
```

**What you get:** The week's Slack activity in 30 seconds of reading.

---

## Quick Setup Checklist

Before using cron tasks, connect the APIs they need:

| API | Setup Command | Used By |
|-----|--------------|---------|
| **gog CLI** (Gmail, Calendar, Sheets) | `openclaw skills install gog && gog auth login` | Email, Outreach, Calendar, Writing, Numbers, Staying in Touch |
| **Brave Search** | Set `BRAVE_API_KEY` in shell profile | Finding Clients, Outreach, Calendar, Writing |
| **Prospeo** | Save key to `~/.config/prospeo/api_key` | Finding Clients |
| **Findymail** | Save key to `~/.config/findymail/api_key` | Finding Clients |
| **Slack** | `openclaw config set channels.slack.enabled true` + tokens | Slack |

See [LINKS.md](./LINKS.md) for all API key links.
See [SETUP.md](./SETUP.md) Phase 4 for cron basics.
See [WORKFLOWS.md](./WORKFLOWS.md) for related workflow patterns.

---

## Managing Your Crons

```bash
# List all active crons
openclaw cron list

# Remove a cron by ID
openclaw cron remove [CRON_ID]

# Test a task manually before scheduling
openclaw cron add --in 1m --task "TEST: [paste task description]"

# Change timezone
openclaw cron update [CRON_ID] --tz "NEW_TIMEZONE"
```

---

## Tips

- **Start with 3-5 crons.** Pick one from each category that matters to you. Add more once you trust the output.
- **Use Sonnet for routine stuff** (inbox scan, health checks, digests). **Opus for things that need judgment** (outreach drafting, lead scoring, research).
- **Every cron = an API call.** More crons = more cost. Start with daily tasks before adding hourly ones.
- **Always test first.** Run any task with `--in 1m` before putting it on a schedule.
- **Draft + confirm for anything external.** If it sends an email, posts something, or contacts someone — make the agent show you first. You can relax to Auto later once you trust it.
