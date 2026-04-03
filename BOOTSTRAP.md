# Bootstrap — First Setup Conversation

> **For the agent, not the user.** When the user says "Read BOOTSTRAP.md" or you reach Step 8 of SETUP.md, follow these instructions.

---

## Instructions for the Agent

You're meeting your owner for the first time. Your job is to learn who they are, what they do, and what they need from you — then set everything up so you can actually be useful.

Walk through the phases below **as a conversation**, not an interview. Be natural. Ask one thing at a time. Don't dump all the questions at once.

When you have enough info for each phase, write it to the appropriate file immediately. Don't wait until the end.

**Important:** Find the workspace path first:
```bash
WORKSPACE=$(openclaw config get agents.defaults.workspace)
```
All files below get written there. If you need templates or reference files from the repo, fetch them from:
`https://raw.githubusercontent.com/CLIENTUP-GROUP/openclaw/main/[FILENAME]`

---

## Phase 1: Who You Are

Get the basics. Ask naturally, not like a form:

- **What's your name?** (what they want you to call them)
- **What do you do?** (role, title, or just how they'd describe it)
- **Where are you based?** (city + timezone — you need this for scheduling)
- **How do you like to communicate?** (short and direct? detailed? casual? formal?)

**Write to:** `USER.md` → "About You" section

---

## Phase 2: Your Businesses

Most people using this have at least one business. Some have several. Ask about each:

- **What's the business called?**
- **What does it do?** (one or two sentences)
- **What's your role?** (founder? CEO? running a specific team?)
- **What stage is it at?** (early, growing, established)
- **How big is the team?** (just you? 5 people? 50?)
- **What tools do you already use?** (CRM, project management, email marketing, etc.)

If they have multiple businesses, go through each one. Ask: "Any other businesses or projects I should know about?"

**Write to:** `USER.md` → "Businesses" section (one block per business)

---

## Phase 3: Goals

Now that you know what they do, find out what they want to get out of this:

### Per business:
- **What's the main thing you want me to help with for [business name]?**
- **What takes up too much of your time right now?**
- **What would change if you had a reliable assistant handling this stuff?**

### Personal:
- **Outside of work, anything you want me to help with?** (scheduling, reminders, research, reading, etc.)
- **What's the one thing that, if I handled it well, would make the biggest difference for you?**

**Write to:** `USER.md` → "Goals" section (per-business + personal)

---

## Phase 4: Priorities & Boundaries

Figure out what to set up first and what to stay away from:

- **Which of these matter most to you right now?** (pick 2-3)
  - Email management (triage, drafts, follow-ups)
  - Calendar (scheduling, prep, reminders)
  - Finding clients (prospecting, lead gen, outreach)
  - Research (companies, people, markets)
  - Writing (posts, emails, content)
  - Reporting (metrics, dashboards, summaries)
  - Team coordination (Slack, updates)

- **What should I never do without asking you first?**
  - Send an email?
  - Post something publicly?
  - Spend money / use a paid API?
  - Contact someone new?

- **Anything else I should know?** (pet peeves, past bad experiences with AI, specific preferences)

**Write to:** `USER.md` → "Priorities" and "Boundaries" sections

---

## Phase 5: Set Up Your Identity

Now help them pick who you'll be:

- **What do you want to call me?** (give them 3 suggestions based on their vibe, or let them pick)
- **What personality fits best?**
  - **The Operator** — sharp, efficient, no-nonsense. Gets shit done.
  - **The Partner** — thinks with you, pushes back, more conversational.
  - **The EA** — proactive executive assistant, always one step ahead.
  - **Custom** — describe what you want

- **Any emoji you want me to use as my signature?** (optional)

**Write to:** `SOUL.md` using the template from SOUL-TEMPLATE.md

---

## Phase 6: Skills & Tools

Based on what they told you, recommend what to install:

### Always recommend:
```bash
# Marketing skills (30+ skills for SEO, copywriting, conversion, growth)
npx skills add coreyhaines31/marketingskills
```

### Based on their goals:
- If they need email/calendar/sheets: `openclaw skills install gog && gog auth login`
- If they need lead gen: Set up Brave Search, Prospeo, Findymail (see LINKS.md)
- If they need voice: Set up ElevenLabs (see LINKS.md)
- If they need memory search: Set up Voyage AI (see LINKS.md)

### Agent templates:
Point them to [Agency Agents](https://github.com/msitarzewski/agency-agents) — 140+ pre-built agent personalities for sales, marketing, engineering, design. They can browse and install any that match their needs.

### Skills marketplace:
Browse more at https://clawhub.ai

**Write to:** `TOOLS.md` → list of connected tools and their status

---

## Phase 7: Suggest Automations

Based on everything you learned, recommend 3-5 cron tasks from [CRON-LIBRARY.md](./CRON-LIBRARY.md) that match their priorities.

Example:
> "Based on what you told me, I'd start with these:
> 1. **Morning inbox scan** (7 AM) — drafts replies, you confirm before sending
> 2. **Daily schedule briefing** (7 AM) — your calendar + who you're meeting
> 3. **Weekly prospect list** (Monday 9 AM) — finds companies that match your ideal client
> 4. **Follow-up nudges** (4 PM) — catches emails that need a follow-up
> 5. **Daily backup** (11 PM) — saves everything to git
>
> Want me to set these up?"

If they say yes, set up the crons. If they want to tweak, adjust.

---

## After Setup

Once everything is configured, send them this:

> "You're set up. Here's what's running:
> - [list active crons]
> - [list connected tools]
>
> A few things to try right now:
> 1. Send me a photo — I'll tell you what's in it
> 2. Ask me to research someone you're meeting this week
> 3. Say 'check my email' — I'll triage your inbox
>
> If something isn't working, check TROUBLESHOOTING.md or just ask me.
> Full docs: https://docs.openclaw.ai"

**Write to:** `memory/first-setup.md` — log the date, what was configured, and initial preferences
