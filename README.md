# 🚀 Mission Control — Real-Time Agent Dashboard

> **Zero-setup agent monitoring for solo developers**

A beautiful, real-time dashboard for monitoring your OpenClaw AI agents. See active tasks, cron job health, issues requiring attention, and recent activity — all from your browser or phone.

<p align="center">
  <img src="assets/screenshots/dashboard-hero.png" alt="Mission Control Dashboard" width="800">
</p>

**🎯 Perfect for:** Solo AI developers, small teams, OpenClaw power users  
**⚡ Setup time:** 0 seconds (Tier 1) to 2 minutes (Tier 2) to 5 minutes (Tier 3)  
**💰 Cost:** Free forever (optional paid hosting for realtime)

---

## ✨ Features

<table>
<tr>
<td width="50%">

### 🚨 **Action Required**
High-priority items with urgency badges (high/medium/low) — always at the top

### ⚡ **Active Tasks**
See what your agent is doing right now, which model it's using, and for how long

### 📊 **Product Status**
Live/testing/down badges for all your deployed services

</td>
<td width="50%">

### ⏰ **Cron Job Monitoring**
Status indicators, error counts, last run times — click to expand error details

### 📋 **Recent Activity**
Timeline of recent events, deploys, and accomplishments

### 🔴 **Live Updates**
Tier 3: Real websocket updates (<1s) with visual flash animation

</td>
</tr>
</table>

---

## 🎨 Three Deployment Tiers

<table>
<tr>
<th width="33%">Tier 1: Canvas</th>
<th width="33%">Tier 2: GitHub Pages</th>
<th width="33%">Tier 3: Realtime</th>
</tr>
<tr>
<td>

**Zero Setup ⚡**

No external services. Dashboard renders directly in your OpenClaw session.

```
Show me the dashboard
```

✅ 0 seconds setup  
✅ No accounts needed  
✅ No hosting required  
⚠️ Manual refresh only

</td>
<td>

**Free Hosting 🌐**

Static HTML on GitHub Pages with 30-second auto-refresh.

```bash
gh repo create dashboard --public
# Copy files, enable Pages
```

✅ 2 minutes setup  
✅ Free GitHub hosting  
✅ Auto-refresh (30s)  
✅ PIN protected

</td>
<td>

**Production Ready ⚡🔥**

True websocket realtime via Supabase + Vercel.

```bash
# Setup Supabase table
# Deploy to Vercel
```

✅ 5 minutes setup  
✅ Real websocket (<1s)  
✅ Connection indicator  
✅ Free tier available

</td>
</tr>
</table>

---

## 🚀 Quick Start

### Option 1: Zero-Config Canvas (Tier 1)

Just ask your agent:

```
Show me the mission control dashboard
```

The agent will gather current state and render the dashboard inline. That's it.

---

### Option 2: GitHub Pages (Tier 2) — Recommended

**Prerequisites:** Git, GitHub CLI (`gh`)

1. **Create a repo:**
   ```bash
   gh repo create mission-control --public --clone
   cd mission-control
   ```

2. **Copy the dashboard:**
   ```bash
   # Clone this repo
   git clone https://github.com/tahseen137/agent-dashboard.git temp
   
   # Copy files
   cp temp/assets/tier2-github.html index.html
   mkdir -p data
   cp temp/assets/templates/dashboard-data.json data/
   rm -rf temp
   ```

3. **Edit `index.html`:**
   ```javascript
   const CONFIG = {
       PIN: 'YOUR_SECRET_PIN',  // Change this!
       DATA_URL: './data/dashboard-data.json',
       REFRESH_INTERVAL: 30000
   };
   ```

4. **Enable GitHub Pages:**
   - Go to repo Settings → Pages
   - Source: Deploy from branch `main`
   - Folder: `/ (root)`

5. **Deploy:**
   ```bash
   git add -A
   git commit -m "Initial deploy"
   git push
   ```

✅ **Done!** Your dashboard is live at `https://YOUR_USERNAME.github.io/mission-control/`

---

### Option 3: Realtime Websockets (Tier 3)

**Prerequisites:** [Supabase account](https://supabase.com) (free), [Vercel account](https://vercel.com) (free)

1. **Create Supabase table:**
   - Go to Supabase Dashboard → SQL Editor
   - Run [`assets/templates/setup-supabase.sql`](assets/templates/setup-supabase.sql)

2. **Get your keys** (Settings → API):
   - Copy `SUPABASE_URL` (Project URL)
   - Copy `SUPABASE_ANON_KEY` (anon public key)
   - ⚠️ **No service_role key needed!**

3. **Edit `tier3-realtime.html`:**
   ```javascript
   const CONFIG = {
       PIN: 'YOUR_SECRET_PIN',
       SUPABASE_URL: 'https://YOUR_PROJECT.supabase.co',
       SUPABASE_ANON_KEY: 'eyJhbG...'
   };
   ```

4. **Deploy to Vercel:**
   ```bash
   mkdir mission-control && cd mission-control
   cp path/to/tier3-realtime.html index.html
   vercel deploy --prod
   ```

5. **Configure push script:**
   ```bash
   export SUPABASE_URL="https://YOUR_PROJECT.supabase.co"
   export SUPABASE_ANON_KEY="eyJhbG..."
   ```

✅ **Done!** Your realtime dashboard is live with <1s websocket updates.

---

## 🔄 Auto-Update Mechanisms

The dashboard updates itself automatically through three mechanisms:

### 1. Periodic Cron (Every 30 Minutes)

Set up a cron job that collects data and pushes it:

```yaml
name: Dashboard Update
schedule: "*/30 * * * *"
model: sonnet
prompt: |
  Update Mission Control:
  1. Run `cron list` for job statuses
  2. Run `sessions_list` for active tasks
  3. Build JSON from API data
  4. Push to Supabase or GitHub
```

### 2. Event-Driven Pushes

Tell your agent to push updates when significant events happen:

```
After this deploy finishes, push an update to Mission Control
```

### 3. Manual Refresh Button

Every dashboard has a **🔄 Update** button for instant refresh.

---

## 📸 Screenshots

<details>
<summary><b>Click to expand screenshots</b></summary>

### Dark Theme (Default)
![Dashboard Dark Theme](assets/screenshots/dashboard-dark.png)

### Mobile View
<img src="assets/screenshots/dashboard-mobile.png" alt="Mobile View" width="375">

### Action Required Alert
![Action Required](assets/screenshots/action-required.png)

### Cron Job Monitoring
![Cron Jobs](assets/screenshots/cron-jobs.png)

### Product Status Cards
![Products](assets/screenshots/products.png)

### PIN Protection
<img src="assets/screenshots/pin-entry.png" alt="PIN Entry" width="400">

</details>

---

## 🔒 Security & Privacy

### What Data Gets Pushed?

**Only operational status** — never secrets or sensitive data:

| Field | Example | Sensitive? |
|-------|---------|------------|
| Task name | "Deploying v2.0" | ❌ No |
| Cron status | "ok" / "error" | ❌ No |
| Product URL | "https://myapp.com" | ❌ No (public URLs) |
| Error message | "timeout after 30s" | ❌ No |

**Never pushed:** API keys, passwords, tokens, file contents, user data, or PII.

### Security Model

- ✅ **Tier 1:** No external services (all local)
- ✅ **Tier 2:** Public GitHub repo (operational status only)
- ✅ **Tier 3:** Supabase anon key (RLS-protected, single table access)
- ✅ **No service_role key required** (best practice)
- ⚠️ **PIN protection:** Client-side only (not for high-security use cases)

**For stronger protection:**
- Make GitHub repo private (GitHub Pro)
- Add Vercel password protection (Vercel Pro)
- Add Supabase Auth (OAuth, email/password)

---

## 🎨 Customization

See [`references/customization.md`](references/customization.md) for:

- Changing colors and themes
- Adding custom sections
- Modifying the grid layout
- Adding charts (Chart.js)
- Internationalization
- Accessibility improvements

Quick theme example:

```css
:root {
    --accent-blue: #3b82f6;   /* Change primary color */
    --bg-card: #1a1a1d;       /* Card background */
    --text-primary: #fafafa;  /* Text color */
}
```

---

## 🐛 Troubleshooting

<details>
<summary><b>Dashboard shows "Disconnected" (Tier 3)</b></summary>

- Check Supabase project is running
- Verify `SUPABASE_ANON_KEY` is correct
- Ensure realtime is enabled on the table (run `setup-supabase.sql`)
- Check browser console for errors

</details>

<details>
<summary><b>Data not updating (Tier 2)</b></summary>

- Verify GitHub Pages is enabled (Settings → Pages)
- Check `data/dashboard-data.json` was pushed to `main` branch
- Hard refresh: Ctrl+Shift+R (Windows) or Cmd+Shift+R (Mac)
- Click the **🔄 Update** button to confirm data is stale

</details>

<details>
<summary><b>PIN not working</b></summary>

- PINs are case-sensitive
- Clear browser cache and cookies
- Verify you edited the PIN in the HTML file
- Check for typos in the `CONFIG.PIN` value

</details>

<details>
<summary><b>Cron status not accurate</b></summary>

- Ensure your "Dashboard Update" cron is running (`cron list`)
- Check cron output for errors
- Manually run: `"Update my Mission Control dashboard now"`
- Verify env vars are set (`SUPABASE_URL`, `SUPABASE_ANON_KEY`)

</details>

---

## 📊 Data Schema

The dashboard expects JSON in this format:

```json
{
  "lastUpdated": "2024-01-15T12:00:00Z",
  "actionRequired": [
    {"title": "Review PR #42", "url": "https://...", "priority": "high"}
  ],
  "activeNow": [
    {"task": "Deploying v2.0", "model": "opus", "startedAt": "2024-01-15T11:45:00Z"}
  ],
  "products": [
    {"name": "My App", "url": "https://...", "status": "live", "lastChecked": "..."}
  ],
  "crons": [
    {"name": "Daily Report", "schedule": "9:00 AM", "lastRun": "...", "status": "ok", "errors": 0}
  ],
  "recentActivity": [
    {"time": "2024-01-15T11:30:00Z", "event": "✅ Deployed v2.1.0"}
  ]
}
```

See [`assets/templates/dashboard-data.json`](assets/templates/dashboard-data.json) for a complete example.

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| **Frontend** | Vanilla HTML/CSS/JS (no build step) |
| **Styling** | CSS Custom Properties, CSS Grid, Flexbox |
| **Font** | [Inter](https://fonts.google.com/specimen/Inter) (Google Fonts) |
| **Backend** | None (Tier 1-2) / Supabase Realtime (Tier 3) |
| **Hosting** | Canvas (Tier 1) / GitHub Pages (Tier 2) / Vercel (Tier 3) |
| **Database** | None (Tier 1-2) / Supabase PostgreSQL (Tier 3) |

**Total size:** ~35KB gzipped (HTML+CSS+JS combined, Tier 2)

---

## 🤝 Contributing

Contributions welcome! This is an **instruction-only OpenClaw skill** — no executable code, just HTML templates and documentation.

**How to help:**
- Report bugs or suggest features (Issues)
- Submit improved templates (Pull Requests)
- Share your customizations (Discussions)
- Add translations (i18n support)

**Development setup:**
1. Fork the repo
2. Edit HTML templates in `assets/`
3. Test locally (Python HTTP server: `python3 -m http.server`)
4. Submit PR with screenshots

---

## 📝 License

MIT License — see [LICENSE](LICENSE) for details.

Free for personal and commercial use. Attribution appreciated but not required.

---

## 🙏 Acknowledgments

- Built for the [OpenClaw](https://openclaw.ai) community
- Inspired by LangSmith, Datadog APM, and Grafana
- Dark theme based on [Vercel Design System](https://vercel.com/design)
- Icons: Emoji (no external dependencies)

---

## 📚 Learn More

- **Full Documentation:** See [SKILL.md](SKILL.md) for comprehensive setup guide
- **Customization:** See [references/customization.md](references/customization.md)
- **OpenClaw Docs:** [docs.openclaw.ai](https://docs.openclaw.ai)
- **Questions?** Open an [issue](https://github.com/tahseen137/agent-dashboard/issues)

---

<p align="center">
  <strong>Ready to monitor your agents?</strong><br>
  ⭐ Star this repo if you find it useful!
</p>

<p align="center">
  Made with ❤️ for the OpenClaw community
</p>
