# Deploy on Mac Mini + AWS Free Tier

> Run OpenClaw 24/7 on a Mac Mini at home, access it from your laptop or phone anywhere via Tailscale. Optionally add an AWS free tier instance as a backup or for lightweight cloud tasks.

---

## Why Mac Mini

- Always on, low power (~6-10W idle)
- Runs macOS natively — no compatibility issues with OpenClaw
- Sits on your desk or in a closet, runs silently
- No monthly hosting bill (you already own it)
- Apple Silicon M-series is fast enough for everything

---

## Part 1: Set Up the Mac Mini

### 1.1 Initial Configuration

If this is a fresh Mac Mini (or one you're repurposing):

```bash
# Enable Remote Login so you can SSH in from your laptop
# System Settings > General > Sharing > Remote Login → ON

# Set the computer to restart automatically after power failure
# System Settings > Energy > Options > Start up automatically after a power failure → ON

# Prevent sleep
# System Settings > Energy > Turn display off after → Never (or 5 min — display off is fine, sleep is not)
# System Settings > Energy > Prevent automatic sleeping when the display is off → ON
```

### 1.2 Install Claude Code

You need Claude Code running on the Mac Mini to execute the setup. SSH in (or use it directly):

```bash
# Install Claude Code CLI (requires Node — we'll install that next if missing)
npm install -g @anthropic-ai/claude-code 2>/dev/null

# If npm isn't available yet, install Node first (next step), then come back to this
```

Once Claude Code is installed, you can either:
- Paste the **one-liner** from [README.md](./README.md) and let it handle everything below
- Or keep following this guide manually

### 1.3 Install Prerequisites

SSH into your Mac Mini (or use it directly):

```bash
# Install Homebrew
command -v brew >/dev/null || /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Node.js 22+
brew install node

# Verify
node --version  # Should be 22+
npm --version
```

### 1.3 Install OpenClaw

```bash
npm install -g openclaw
openclaw --version
```

### 1.4 Set API Key

```bash
echo 'export ANTHROPIC_API_KEY="YOUR_KEY_HERE"' >> ~/.zshrc
source ~/.zshrc
```

### 1.5 Run Onboarding

```bash
openclaw onboard \
  --non-interactive \
  --accept-risk \
  --auth-choice token \
  --token-provider anthropic \
  --token "$ANTHROPIC_API_KEY" \
  --gateway-bind loopback \
  --gateway-auth token \
  --gateway-token "$(openssl rand -hex 32)" \
  --install-daemon \
  --skip-channels \
  --skip-skills \
  --skip-ui
```

This installs the daemon via launchd — OpenClaw starts automatically on boot.

### 1.6 Connect a Channel

See [SETUP.md](./SETUP.md) Step 5. Telegram is the fastest:

```bash
openclaw config set channels.telegram.enabled true
openclaw config set channels.telegram.botToken "YOUR_BOT_TOKEN"
openclaw gateway restart
```

### 1.7 Verify

```bash
openclaw health
openclaw channels list
```

Send a test message from your phone. If it replies, your Mac Mini is live.

---

## Part 2: Tailscale — Access From Anywhere

This is how you access your Mac Mini's OpenClaw dashboard from your laptop, phone, or any other device — without exposing anything to the public internet.

### 2.1 What Tailscale Does

Tailscale creates a private encrypted network between your devices. Your Mac Mini, laptop, and phone all get private IPs (like `100.x.x.x`) that only your devices can reach. No port forwarding, no firewall holes, no VPN server to manage.

**Get it at:** https://tailscale.com/ (free for personal use, up to 100 devices)

### 2.2 Install on Mac Mini

```bash
brew install tailscale

# Start Tailscale and log in
tailscale up
```

Follow the auth URL to log in with your Tailscale account. Once connected:

```bash
# Get the Mac Mini's Tailscale IP
tailscale ip -4
# Example output: 100.64.0.1
```

Write this IP down — you'll use it to access OpenClaw remotely.

### 2.3 Install on Your Other Devices

- **Mac/Windows laptop:** Download from https://tailscale.com/download
- **iPhone/Android:** Get the Tailscale app from the App Store / Play Store
- Log in with the same account on every device

### 2.4 Configure OpenClaw for Remote Access

By default, OpenClaw binds to `loopback` (localhost only). To access it from other Tailscale devices:

```bash
# Change gateway bind to the Tailscale interface
openclaw config set gateway.bind "100.64.0.1"
# Replace with your actual Tailscale IP from step 2.2

openclaw gateway restart
```

Now access the dashboard from any device on your tailnet:
```
http://100.64.0.1:18789
```

### 2.5 Security Notes

- Tailscale traffic is end-to-end encrypted (WireGuard)
- Only devices on your tailnet can reach the Mac Mini
- Still keep token auth on (`gateway.auth.mode = token`)
- Don't bind to `0.0.0.0` — that exposes it to your entire local network
- Tailscale free tier includes MagicDNS — you can use `mac-mini.tailnet-name.ts.net` instead of the IP

---

## Part 3: AWS Free Tier (Optional)

Add a cloud instance for backup, webhook hosting, or running lightweight tasks when your Mac Mini is down.

### 3.1 What You Get for Free

AWS Free Tier (12 months from signup):
- **EC2 t2.micro** (or t3.micro in some regions): 1 vCPU, 1GB RAM — 750 hours/month (enough to run 24/7)
- **30GB EBS storage**
- **100GB data transfer out/month**

After 12 months, a t3.micro costs ~$8/month. Switch to Hetzner or DigitalOcean at that point if cost matters.

### 3.2 Launch an EC2 Instance

1. Go to https://console.aws.amazon.com/ec2
2. Click "Launch Instance"
3. Settings:
   - **Name:** `openclaw`
   - **AMI:** Ubuntu 24.04 LTS (free tier eligible)
   - **Instance type:** t2.micro (or t3.micro if available as free tier)
   - **Key pair:** Create new or use existing
   - **Security group:** Allow SSH (port 22) from your IP only. We'll use Tailscale for everything else.
   - **Storage:** 20GB gp3

4. Launch and SSH in:
```bash
ssh -i your-key.pem ubuntu@YOUR_EC2_PUBLIC_IP
```

### 3.3 Install OpenClaw on EC2

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js 22+
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs git

# Install OpenClaw
sudo npm install -g openclaw

# Set API key
echo 'export ANTHROPIC_API_KEY="YOUR_KEY_HERE"' >> ~/.bashrc
source ~/.bashrc

# Run onboarding
openclaw onboard \
  --non-interactive \
  --accept-risk \
  --auth-choice token \
  --token-provider anthropic \
  --token "$ANTHROPIC_API_KEY" \
  --gateway-bind loopback \
  --gateway-auth token \
  --gateway-token "$(openssl rand -hex 32)" \
  --install-daemon \
  --skip-channels \
  --skip-skills \
  --skip-ui
```

### 3.4 Install Tailscale on EC2

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Follow the auth URL. Now your EC2 instance is on the same tailnet as your Mac Mini and laptop.

```bash
# Get the EC2 Tailscale IP
tailscale ip -4
# Example: 100.64.0.2
```

### 3.5 Connect Mac Mini ↔ EC2

Both devices are now on the same Tailscale network. They can reach each other at their `100.x.x.x` IPs without any port forwarding or security group changes.

Use cases:
- **Backup instance:** If your Mac Mini goes offline, your EC2 keeps running crons
- **Webhook endpoint:** Give external services the EC2's public IP for webhooks, forward to Mac Mini via Tailscale
- **Lightweight tasks:** Run simple monitoring crons on EC2 to save Mac Mini resources

### 3.6 Set Up systemd on EC2

OpenClaw's `--install-daemon` should handle this. Verify:

```bash
systemctl status openclaw
```

If it's not running:
```bash
sudo systemctl enable openclaw
sudo systemctl start openclaw
```

### 3.7 Lock Down EC2 Security

```bash
# Tighten security group: remove all inbound rules except SSH from your IP
# Even better: once Tailscale is working, remove the SSH rule too and SSH via Tailscale IP

# File permissions
chmod 700 ~/.openclaw
chmod 600 ~/.openclaw/openclaw.json

# Firewall (belt and suspenders)
sudo ufw allow 22/tcp  # or remove once using Tailscale SSH
sudo ufw enable
```

---

## Part 4: Daily Operations

### Accessing Your Setup

| What | How |
|------|-----|
| Dashboard from laptop | `http://100.64.0.1:18789` (Mac Mini Tailscale IP) |
| SSH into Mac Mini | `ssh your-user@100.64.0.1` |
| SSH into EC2 | `ssh ubuntu@100.64.0.2` |
| Message your agent | Telegram / WhatsApp / Slack from your phone |

### Keeping Things Running

- **Mac Mini:** launchd auto-restarts OpenClaw on boot and crash
- **EC2:** systemd does the same
- **Tailscale:** runs as a system service, reconnects automatically
- **Set up health check cron** (see [CRON-LIBRARY.md](./CRON-LIBRARY.md) section 8.1) to alert you if either goes down

### Backing Up

Set up the daily backup cron from [CRON-LIBRARY.md](./CRON-LIBRARY.md) section 8.2 on both devices:

```bash
openclaw cron add \
  --schedule "0 23 * * *" \
  --task "Git commit all workspace changes and push to remote." \
  --tz "YOUR_TIMEZONE"
```

### Cost Summary

| Item | Cost |
|------|------|
| Mac Mini | $0/month (you own it) |
| Electricity | ~$2-5/month |
| AWS EC2 (free tier) | $0 for 12 months, then ~$8/month |
| Tailscale | $0 (free for personal, up to 100 devices) |
| Anthropic API | Varies — Sonnet ~$3/1M tokens, Opus ~$15/1M tokens |
| Other APIs | Most have free tiers for personal use |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Can't SSH into Mac Mini | Check Remote Login is on. Check Tailscale is running: `tailscale status` |
| Dashboard not loading remotely | Check `gateway.bind` is set to Tailscale IP, not loopback. Restart gateway. |
| Mac Mini went to sleep | Enable "Prevent automatic sleeping" in Energy settings |
| EC2 instance stopped | Check AWS console — free tier is 750 hours/month (enough for 24/7 for 1 instance) |
| Tailscale disconnected | `tailscale up` on the disconnected device. Check https://login.tailscale.com/admin for device status |

For more, see [TROUBLESHOOTING.md](./TROUBLESHOOTING.md).
