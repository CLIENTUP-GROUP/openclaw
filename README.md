# OpenClaw + Claude Masterclass

Your AI runs 24/7, messages you on WhatsApp/Telegram, checks your inbox, manages your calendar, does research, writes emails in your voice, and automates your business ops.

## How to Use This

Two ways to get started. Pick whichever fits you.

### Option A: One-Liner (Fastest)

Paste this into Claude Code, Cursor, or any coding agent with terminal access:

```
Fetch https://raw.githubusercontent.com/CLIENTUP-GROUP/openclaw/main/SETUP.md and follow it step by step. Ask me for input when needed. When you need other files from the repo (templates, BOOTSTRAP.md), fetch them from https://raw.githubusercontent.com/CLIENTUP-GROUP/openclaw/main/
```

The agent reads SETUP.md, installs OpenClaw, connects your channels, then runs the onboarding conversation from BOOTSTRAP.md. You don't need to clone anything.

### Option B: Clone the Repo

```bash
git clone https://github.com/CLIENTUP-GROUP/openclaw.git
cd openclaw
```

Then tell your coding agent:

```
Read SETUP.md in this directory and follow it step by step. Ask me for input when needed.
```

This way you have all the files locally — you can edit templates before the agent uses them.

---

## What Happens During Setup

1. **Install & connect** — Agent installs OpenClaw, sets up your API key, connects Telegram/WhatsApp/Slack (SETUP.md)
2. **Get to know you** — Agent asks about you, your businesses, and your goals. Writes it all to your workspace files (BOOTSTRAP.md)
3. **Configure your agent** — Personality, rules, communication style (SOUL-TEMPLATE.md, AGENTS-TEMPLATE.md)
4. **Set up automations** — Agent recommends cron jobs based on your priorities (CRON-LIBRARY.md)

After that, you browse the reference docs to add more:

---

## What's Inside

### Agent reads these (setup + onboarding):

| File | What It Does |
|------|-------------|
| [SETUP.md](./SETUP.md) | Full install guide — Mac + Windows + Linux. The agent executes this step by step. |
| [BOOTSTRAP.md](./BOOTSTRAP.md) | First-run onboarding — the agent asks who you are, your businesses, and your goals. |
| [AGENTS-TEMPLATE.md](./AGENTS-TEMPLATE.md) | Drop-in operating rules the agent copies to your workspace. |
| [SOUL-TEMPLATE.md](./SOUL-TEMPLATE.md) | Personality template the agent fills in based on your preferences. |

### You browse these (reference docs):

| File | What It Does |
|------|-------------|
| [USER-TEMPLATE.md](./USER-TEMPLATE.md) | Fill-in-the-blank USER.md if you'd rather write it yourself than talk through BOOTSTRAP. |
| [CRON-LIBRARY.md](./CRON-LIBRARY.md) | Ready-to-paste cron jobs: email, outreach, calendar, lead gen, reporting, Slack. |
| [WORKFLOWS.md](./WORKFLOWS.md) | Ready-to-use automation workflows: email, calendar, research, outreach, reporting. |
| [PROMPT-LIBRARY.md](./PROMPT-LIBRARY.md) | Copy-paste prompts for common tasks. |
| [DEPLOY-MAC-MINI.md](./DEPLOY-MAC-MINI.md) | Run OpenClaw 24/7 on a Mac Mini + AWS free tier. Access from anywhere via Tailscale. |
| [DEPLOY-VPS.md](./DEPLOY-VPS.md) | Run OpenClaw in the cloud. No hardware needed. $5/month. |
| [LINKS.md](./LINKS.md) | All API key links, tools, and resources in one place. |
| [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) | When shit breaks. Every common error and how to fix it. |

---

## Resources

- Site: https://openclaw.ai
- Docs: https://docs.openclaw.ai
- Full docs index: https://docs.openclaw.ai/llms.txt
- GitHub: https://github.com/openclaw/openclaw
- Community: https://discord.com/invite/clawd
- Skills marketplace: https://clawhub.ai
