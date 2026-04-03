# Deploy on a VPS (No Physical Device)

> Run OpenClaw 24/7 in the cloud. No Mac Mini, no hardware, no closet server. Just a $5/month VPS that runs everything.

---

## Why VPS

- No physical device to buy or maintain
- Runs 24/7 in a datacenter with better uptime than your house
- Accessible from anywhere
- Takes 15 minutes to set up
- $4-6/month for personal use

---

## Part 1: Pick a Provider

| Provider | Cheapest Plan | Location | Notes |
|----------|--------------|----------|-------|
| **Hetzner** | ~$4/month (CX22) | EU (Germany, Finland) | Best value for EU users |
| **DigitalOcean** | $6/month (Basic) | US, EU, Asia | Good docs, easy dashboard |
| **Vultr** | $5/month (Cloud Compute) | Worldwide | Lots of locations |
| **Linode (Akamai)** | $5/month (Nanode) | US, EU, Asia | Solid, simple |
| **Oracle Cloud** | Free tier (always free) | US, EU | 1 vCPU, 1GB RAM — tight but works |

**Minimum specs:** 1 vCPU, 1GB RAM, 20GB SSD, Ubuntu 22.04+ LTS

Pick whatever's cheapest in a region close to you. They're all fine. AWS with free credits is best and easiest to setup.

---

## Part 2: Set Up the VPS

### 2.1 Create the Server

On your provider's dashboard:
1. Create a new server / droplet / instance
2. Choose **Ubuntu 24.04 LTS**
3. Pick the cheapest plan (1 vCPU, 1GB RAM)
4. Add your **SSH key** (don't use password auth)
5. Pick a region close to you
6. Create it

### 2.2 First Login

```bash
ssh root@YOUR_SERVER_IP
```

### 2.3 Secure the Server

Do this first, before anything else:

```bash
# Update everything
apt update && apt upgrade -y

# Create a non-root user
adduser openclaw
usermod -aG sudo openclaw

# Copy your SSH key to the new user
mkdir -p /home/openclaw/.ssh
cp ~/.ssh/authorized_keys /home/openclaw/.ssh/
chown -R openclaw:openclaw /home/openclaw/.ssh
chmod 700 /home/openclaw/.ssh
chmod 600 /home/openclaw/.ssh/authorized_keys

# Disable root SSH login and password auth
sed -i 's/^PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
systemctl restart sshd

# Set up firewall
ufw allow 22/tcp
ufw --force enable

# Install fail2ban (blocks brute force SSH attempts)
apt install -y fail2ban
systemctl enable fail2ban
```

Now log out and log back in as the new user:
```bash
ssh openclaw@YOUR_SERVER_IP
```

### 2.4 Install Node.js + Claude Code + OpenClaw

```bash
# Install Node.js 22+
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs git

# Verify
node --version  # Should be 22+
npm --version

# Install OpenClaw
sudo npm install -g openclaw
openclaw --version

# Install Claude Code CLI (for running the setup one-liner or future agent tasks)
npm install -g @anthropic-ai/claude-code
```

Once Node + Claude Code are installed, you can either:
- Run `claude` and paste the **one-liner** from [README.md](./README.md) to automate the rest
- Or keep following this guide manually

### 2.5 Set API Key

```bash
echo 'export ANTHROPIC_API_KEY="YOUR_KEY_HERE"' >> ~/.bashrc
source ~/.bashrc
```

### 2.6 Run Onboarding

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

### 2.7 Verify the Daemon

```bash
systemctl status openclaw
# Should show "active (running)"

openclaw health
```

---

## Part 3: Connect a Channel

**Telegram is the best option for VPS** — no QR code scanning needed, just a token.

```bash
# 1. Open Telegram, find @BotFather
# 2. Send /newbot, pick a name, get the bot token
# 3. Configure:
openclaw config set channels.telegram.enabled true
openclaw config set channels.telegram.botToken "YOUR_BOT_TOKEN"
openclaw gateway restart

# 4. Verify
openclaw channels list
```

Send a test message to your bot. If it replies, you're live.

**WhatsApp** works too but requires a QR scan. You'll need to run the pairing command and scan from your phone:
```bash
openclaw channels login --channel whatsapp --verbose
# Scan the QR code on your phone (WhatsApp > Linked Devices > Link a Device)
```

---

## Part 4: Tailscale — Access Dashboard Remotely

### 4.1 Install Tailscale

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Follow the auth URL to log in. Get your VPS Tailscale IP:
```bash
tailscale ip -4
# Example: 100.64.0.3
```

### 4.2 Install Tailscale on Your Laptop/Phone

- **Mac/Windows:** Download from https://tailscale.com/download
- **iPhone/Android:** App Store / Play Store
- Log in with the same Tailscale account

### 4.3 Access Dashboard Remotely

```bash
# Change gateway bind to Tailscale IP
openclaw config set gateway.bind "100.64.0.3"
# Replace with your actual Tailscale IP

openclaw gateway restart
```

Now open the dashboard from any device on your tailnet:
```
http://100.64.0.3:18789
```

### 4.4 Optional: SSH via Tailscale

Once Tailscale is working, you can remove the public SSH rule and only SSH via Tailscale:

```bash
# On the VPS:
sudo ufw delete allow 22/tcp
sudo ufw allow in on tailscale0 to any port 22
sudo ufw reload
```

Now SSH with the Tailscale IP:
```bash
ssh openclaw@100.64.0.3
```

Nobody can reach your server from the public internet anymore.

---

## Part 5: Keep It Running

### Auto-Restart on Crash

systemd handles this. Verify:
```bash
systemctl is-enabled openclaw
# Should output: enabled
```

If it's not enabled:
```bash
sudo systemctl enable openclaw
```

### Log Rotation

Set up log rotation so logs don't fill your disk:

```bash
sudo tee /etc/logrotate.d/openclaw > /dev/null << 'EOF'
/home/openclaw/.openclaw/logs/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
}
EOF
```

### Daily Backup

Set up the backup cron from [CRON-LIBRARY.md](./CRON-LIBRARY.md):

```bash
openclaw cron add \
  --schedule "0 23 * * *" \
  --task "Git commit all workspace changes and push to remote." \
  --tz "YOUR_TIMEZONE"
```

Make sure git is configured in your workspace:
```bash
WORKSPACE=$(openclaw config get agents.defaults.workspace)
cd "$WORKSPACE"
git init
git remote add origin YOUR_GIT_REPO_URL
```

### Health Monitoring

Set up the health check cron from [CRON-LIBRARY.md](./CRON-LIBRARY.md) section 8.1:

```bash
openclaw cron add \
  --schedule "*/30 * * * *" \
  --task "Run openclaw health. Check gateway and channels. Only message me if something is down." \
  --tz "YOUR_TIMEZONE"
```

---

## Part 6: Optional — Custom Domain + HTTPS

If you want a nice URL for the dashboard instead of a Tailscale IP:

### Using Caddy (Easiest)

```bash
# Install Caddy
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudflare.com/deb/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudflare.com/deb/debian.list' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

```bash
# Configure reverse proxy
sudo tee /etc/caddy/Caddyfile > /dev/null << 'EOF'
openclaw.yourdomain.com {
    reverse_proxy 127.0.0.1:18789
}
EOF

sudo systemctl restart caddy
```

Point your domain's DNS A record to your VPS public IP. Caddy auto-provisions HTTPS via Let's Encrypt.

**Note:** If you go this route, you need to open ports 80 and 443:
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw reload
```

And set gateway bind back to loopback:
```bash
openclaw config set gateway.bind loopback
openclaw gateway restart
```

Caddy proxies public HTTPS traffic to localhost. Best of both worlds.

---

## Cost Breakdown

| Item | Monthly Cost |
|------|-------------|
| VPS (1 vCPU, 1GB RAM) | $4-6 |
| Tailscale | $0 (free personal) |
| Domain (optional) | ~$1 (if you need one) |
| Anthropic API | Varies — Sonnet ~$3/1M tokens, Opus ~$15/1M tokens |
| Brave Search API | Free tier: 2,000 queries/month |
| Prospeo | Free tier: 75 credits/month |
| Findymail | Free tier: 50 credits/month |
| **Total (typical personal use)** | **$5-15/month + API costs** |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Can't SSH in | Check security group allows port 22. Check you're using the right SSH key. |
| OpenClaw not starting | `systemctl status openclaw` — check for errors. Try `openclaw doctor`. |
| Dashboard not loading | Check `gateway.bind` setting. If using Tailscale, bind to Tailscale IP. If using Caddy, bind to loopback. |
| Telegram bot not responding | `openclaw channels list` — check status. `openclaw gateway restart`. |
| WhatsApp disconnected | Re-pair: `openclaw channels login --channel whatsapp --verbose` |
| Disk full | Check with `df -h`. Rotate logs. Remove old memory files. |
| High memory usage | OpenClaw + Node use ~200-400MB. If hitting limits, consider upgrading to 2GB plan. |

For more, see [TROUBLESHOOTING.md](./TROUBLESHOOTING.md).
