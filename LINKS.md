# Links & API Keys

> Everything you need to get your OpenClaw setup fully connected.

---

## API Keys

| Service | What It Does | Get Your Key |
|---------|-------------|-------------|
| **Anthropic (Claude)** | Main AI model — the brain | https://console.anthropic.com/settings/keys |
| **OpenAI** | GPT models, Whisper transcription, DALL-E | https://platform.openai.com/api-keys |
| **Google Gemini** | Gemini models + Nano Banana | https://aistudio.google.com/app/api-keys |
| **Brave Search** | Web search API (no browser needed) | https://brave.com/search/api/ |
| **Findymail** | Email finder from LinkedIn/name+company | https://www.findymail.com/api/ |
| **Voyage AI** | Memory embeddings (semantic search) | https://www.voyageai.com/ |
| **ElevenLabs** | AI voice / text-to-speech | https://elevenlabs.io/ |
| **Prospeo** | Email finder + verifier | https://prospeo.io/api/documentation |

---

## Google Workspace (Gmail, Calendar, Sheets, Drive, Docs)

Connect your Google account so your agent can read emails, check calendar, pull spreadsheet data, and manage docs.

- **gog CLI:** https://github.com/googleworkspace/cli
- **Quick install:** https://gogcli.sh/

```bash
# Install
brew install googleworkspace/tap/gog  # or: go install github.com/googleworkspace/cli@latest

# Authenticate
gog auth login
```

---

## Skills & Agent Templates

### ClawHub (Skills Marketplace)
Browse and install pre-built skills that add capabilities to your agent:
- https://clawhub.ai

```bash
openclaw skills install [skill-name]
openclaw skills list
```

### Marketing Skills
30+ AI agent skills for SEO, copywriting, conversion optimization, analytics, and growth engineering. By Corey Haines.
- https://marketing-skills.com/

```bash
npx skills add coreyhaines31/marketingskills
```

### Agency Agents
140+ pre-built agent personalities for sales, marketing, engineering, design, product, and ops. MIT licensed, 69K+ stars.
- https://github.com/msitarzewski/agency-agents

```bash
# Install for Claude Code
cp agency-agents/sales/* ~/.claude/agents/

# Or use the install script
git clone https://github.com/msitarzewski/agency-agents.git
cd agency-agents && ./scripts/install.sh
```

---

## Networking & Remote Access

### Tailscale
Secure remote access to your OpenClaw server. Access your dashboard from your laptop or phone without exposing ports to the internet. Free for personal use.
- https://tailscale.com/

```bash
# macOS
brew install tailscale && tailscale up

# Linux / VPS
curl -fsSL https://tailscale.com/install.sh | sh && sudo tailscale up
```

See [DEPLOY-MAC-MINI.md](./DEPLOY-MAC-MINI.md) and [DEPLOY-VPS.md](./DEPLOY-VPS.md) for full Tailscale setup.

---

## Memory & Knowledge

### Voyage AI (Recommended for Memory Search)
Semantic embeddings so your agent can search its own memory intelligently.
- https://www.voyageai.com/

### Obsidian (Personal Knowledge Base)
Connect your Obsidian vault so your agent can read and search your notes.
- **Obsidian:** https://obsidian.md/
- **OpenClaw Obsidian Skill:** https://clawhub.ai/steipete/obsidian

```bash
openclaw skills install steipete/obsidian
```

---

## OpenClaw Core

- **Site:** https://openclaw.ai
- **Docs:** https://docs.openclaw.ai
- **Full Docs Index (for LLMs):** https://docs.openclaw.ai/llms.txt
- **GitHub:** https://github.com/openclaw/openclaw
- **Community (Discord):** https://discord.com/invite/clawd
- **Skills:** https://clawhub.ai
