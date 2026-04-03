# Troubleshooting

> When shit breaks. Every common error and how to fix it.

---

## Gateway Won't Start

```bash
# Check what's wrong
openclaw doctor
openclaw logs --lines 50

# Common fix: port already in use
lsof -i :18789
# Kill whatever's on that port, then:
openclaw gateway restart

# Nuclear option: full restart
openclaw gateway stop
sleep 3
openclaw gateway start
```

## "openclaw: command not found"

```bash
# Check if Node sees it
npm list -g openclaw

# Add npm global bin to PATH
# macOS:
echo 'export PATH="$(npm config get prefix)/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc

# Linux/WSL2:
echo 'export PATH="$(npm config get prefix)/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

## Agent Not Responding to Messages

```bash
# Check channel status
openclaw channels list
openclaw health

# Check if the channel is actually connected
openclaw logs --lines 50 | grep -i "error\|disconnect\|fail"

# Restart gateway to reconnect
openclaw gateway restart
```

## WhatsApp Disconnected

```bash
# Re-pair
openclaw channels login --channel whatsapp --verbose

# Scan the QR code again from your phone
# WhatsApp > Linked Devices > Link a Device
```

WhatsApp disconnects are the most common issue. They happen when:
- Your phone loses internet for extended periods
- WhatsApp updates their protocol
- The session expires (rare, but happens)

## Telegram Bot Not Responding

```bash
# Verify bot token is set
openclaw config get channels.telegram.botToken

# Make sure you started a conversation with the bot first
# Go to Telegram, find your bot, send /start

# Restart
openclaw gateway restart
```

## Agent Gives Bad/Generic Responses

This usually means it's not reading the workspace files properly.

```bash
# Check workspace location
openclaw config get agents.defaults.workspace

# Verify files exist
ls -la $(openclaw config get agents.defaults.workspace)

# Check that SOUL.md, AGENTS.md, USER.md exist and have content
cat $(openclaw config get agents.defaults.workspace)/SOUL.md

# Restart to reload workspace files
openclaw gateway restart
```

## API Key Issues

```bash
# Check if key is set
echo $ANTHROPIC_API_KEY

# If empty, re-export it
export ANTHROPIC_API_KEY="your-key-here"

# Make it permanent
echo 'export ANTHROPIC_API_KEY="your-key-here"' >> ~/.zshrc
source ~/.zshrc

# Verify OpenClaw sees it
openclaw doctor
```

## High API Costs

- Switch default model to Sonnet for daily use: cheaper, still good
- Use Opus only for complex tasks (research, analysis)
- Reduce heartbeat frequency (every 60 min instead of 30)
- Check for runaway cron jobs: `openclaw cron list`
- Check session history for unexpected activity: `openclaw sessions list`

```bash
# Change default model
openclaw config set agents.defaults.model "anthropic/claude-sonnet-4-20250514"
openclaw gateway restart
```

## Memory Not Working

```bash
# Check if memory files exist
ls $(openclaw config get agents.defaults.workspace)/memory/

# Check MEMORY.md
cat $(openclaw config get agents.defaults.workspace)/MEMORY.md

# If memory search returns nothing, the embedding model may not be configured
openclaw doctor
```

## WSL2-Specific Issues

### Gateway won't auto-start
WSL2 doesn't have systemd by default. Start manually:
```bash
nohup openclaw gateway > /tmp/openclaw-gateway.log 2>&1 &
```

Or enable systemd in WSL2:
```bash
# Edit /etc/wsl.conf
sudo bash -c 'echo -e "[boot]\nsystemd=true" >> /etc/wsl.conf'
# Then restart WSL from PowerShell: wsl --shutdown
```

### Can't access dashboard from Windows browser
```bash
# WSL2 runs on a different IP. Find it:
hostname -I

# Access dashboard at that IP:
# http://[WSL_IP]:18789
```

## Config Is Broken

```bash
# Validate config
openclaw doctor

# If doctor says config is invalid:
# Option 1: Fix specific fields
openclaw config set [field] [value]

# Option 2: Re-run onboarding (preserves workspace files)
openclaw onboard --install-daemon

# Option 3: Nuclear — reset config only (keeps workspace)
openclaw onboard --reset --reset-scope config
```

## Everything Is Broken

```bash
# Full diagnostic
openclaw doctor

# Check all logs
openclaw logs --lines 100

# Restart everything
openclaw gateway stop
sleep 5
openclaw gateway start

# Verify
openclaw health

# If still broken, re-onboard (keeps your workspace files):
openclaw onboard --install-daemon
```

## Getting Help

- Docs: https://docs.openclaw.ai/help/troubleshooting
- Discord: https://discord.com/invite/clawd
- GitHub Issues: https://github.com/openclaw/openclaw/issues
