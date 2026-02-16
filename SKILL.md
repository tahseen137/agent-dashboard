---
name: agent-dashboard
description: >
  Real-time agent dashboard for OpenClaw. Monitor active tasks, cron job health,
  issues, and action items from anywhere. Three setup tiers: (1) Zero-config canvas
  rendering inside OpenClaw, (2) GitHub Pages with 30-second polling — free, 2-minute
  setup, (3) Supabase Realtime + Vercel for instant websocket updates. All data stays
  on your machine. PIN-protected. No external services required for Tier 1.
---

# Mission Control 🚀

A real-time dashboard showing what your OpenClaw agent is doing, cron job health, issues requiring attention, and recent activity. Check it from anywhere — your phone, your laptop, wherever.

## Quick Start

### Tier 1 — Canvas (Zero Setup) ⚡

No external services. The agent renders the dashboard directly in your OpenClaw session.

**How to use:**
```
Show me the mission control dashboard
```

The agent will:
1. Gather current state (active tasks, crons, etc.)
2. Generate a dashboard using the canvas tool
3. Present it inline in your session

That's it. No deploy, no accounts, nothing to configure.

---

### Tier 2 — GitHub Pages + Polling (Recommended) 🌐

Free hosting with 30-second auto-refresh. Takes 2 minutes to set up.

**Setup:**

1. **Create a repo:**
   ```bash
   gh repo create mission-control --public --clone
   cd mission-control
   ```

2. **Copy the dashboard:**
   ```bash
   mkdir -p data
   # Copy tier2-github.html to index.html
   # Copy assets/templates/dashboard-data.json to data/
   ```

3. **Edit `index.html`:**
   - Change `YOUR_PIN_HERE` to your chosen PIN

4. **Enable GitHub Pages:**
   - Go to repo Settings → Pages
   - Source: Deploy from branch `main`
   - Folder: `/ (root)`

5. **Deploy:**
   ```bash
   git add -A && git commit -m "Initial deploy" && git push
   ```

Your dashboard is now live at `https://YOUR_USERNAME.github.io/mission-control/`

---

### Tier 3 — Supabase Realtime + Vercel (Premium) ⚡🔥

True websocket realtime — updates appear in under 1 second.

**Prerequisites:**
- Supabase account (free tier works)
- Vercel account (free tier works)

**Step 1: Create Supabase Table**

In your Supabase SQL Editor, run `assets/templates/setup-supabase.sql`.

**Step 2: Get Your Keys**

From Supabase Dashboard → Settings → API:
- Copy `SUPABASE_URL` (Project URL)
- Copy `SUPABASE_ANON_KEY` (anon public key — safe for client)
- Copy `SUPABASE_SERVICE_KEY` (service_role key — keep secret!)

**Step 3: Edit the Dashboard**

In `tier3-realtime.html`:
1. Replace `YOUR_SUPABASE_URL` with your project URL
2. Replace `YOUR_SUPABASE_ANON_KEY` with your anon key
3. Replace `YOUR_PIN_HERE` with your chosen PIN

**Step 4: Deploy to Vercel**

```bash
mkdir mission-control && cd mission-control
# Copy tier3-realtime.html as index.html
vercel deploy --prod
```

**Step 5: Configure Push Script**

```bash
export SUPABASE_URL="https://YOUR_PROJECT.supabase.co"
export SUPABASE_SERVICE_KEY="eyJ..."  # service_role key, NOT anon
```

---

## 🔄 Keeping It Fresh — Auto-Update Mechanism

**The dashboard updates itself automatically.** Here's how:

### 1. Cron Auto-Update (Every 30 Minutes)

Set up a cron job that collects live data and pushes it to your dashboard:

```
Create a cron job called "Dashboard Update" that runs every 30 minutes.
It should:
1. Run `cron list` to get all cron job statuses, error counts, last run times
2. Run `sessions_list` to find any active sub-agents and their current tasks
3. Read HEARTBEAT.md for action items requiring human attention
4. Read today's memory file (memory/YYYY-MM-DD.md) for recent activity
5. Build the dashboard JSON and push to Supabase (or git push for Tier 2)
```

**Sample cron configuration:**
```yaml
name: Dashboard Update
schedule: "*/30 * * * *"  # Every 30 minutes
model: sonnet             # Fast model for quick updates
prompt: |
  Update the Mission Control dashboard with current state:
  
  1. Get cron status: Run `cron list` and parse the output
  2. Get active sessions: Run `sessions_list` to find active sub-agents
  3. Get action items: Read HEARTBEAT.md for pending items
  4. Get recent activity: Read today's memory file
  5. Build JSON matching the dashboard schema
  6. Push to Supabase: curl -X PATCH $SUPABASE_URL/rest/v1/dashboard_state...
  
  Keep activeNow accurate - only include sessions that are actually running.
```

### 2. Real-Time Event Pushes

Beyond the periodic cron, the agent pushes updates **immediately** when significant events happen:

- ✅ Task starts or finishes
- ❌ Errors or failures
- 🚀 Deploys complete
- 📧 Important notifications arrive

This means the dashboard reflects changes within seconds, not just every 30 minutes.

**How to enable:** When you start a major task, tell the agent:
```
After this deploy finishes, push an update to Mission Control.
```

### 3. Force Update Button

Every dashboard tier includes a **🔄 Update** button in the header:
- **Tier 2:** Re-fetches `dashboard-data.json` immediately
- **Tier 3:** Re-fetches from Supabase immediately
- Resets the "Updated X ago" timer
- Shows loading spinner while fetching

Use this when you want to confirm the latest state without waiting for auto-refresh.

### The Result

The combination of **periodic cron + real-time pushes + manual refresh** keeps your dashboard accurate at all times. You'll always see what your agent is actually doing.

---

## Dashboard Features

### 🚨 Action Required
Urgent items that need your attention. Highlighted at the top with priority badges (high/medium/low).

### ⚡ Active Now
What the agent is currently working on, with model name and duration.

### 📊 Products
Your product cards with live/testing/down status badges.

### ⏰ Cron Jobs
Table showing all scheduled jobs with status, last run time, and error counts. Click to expand error details.

### 📋 Recent Activity
Timeline of recent events and accomplishments.

### 🔴 Live Indicator (Tier 3 only)
Green pulsing dot shows websocket is connected. Flash animation when data updates.

---

## Environment Variables

| Variable | Required | Tier | Purpose |
|----------|----------|------|---------|
| `DASHBOARD_PIN` | Yes | All | PIN code for dashboard access |
| `GITHUB_REPO` | Yes | Tier 2 | GitHub repo for Pages hosting (e.g., `username/mission-control`) |
| `SUPABASE_URL` | Yes | Tier 3 | Supabase project URL |
| `SUPABASE_ANON_KEY` | Yes | Tier 3 | Supabase anon key (read-only, safe for client-side) |
| `SUPABASE_SERVICE_KEY` | Yes | Tier 3 | Service role key (server-side push script ONLY — never expose!) |

---

## Permissions Required

| Tier | Permissions | Why |
|------|-------------|-----|
| Tier 1 | None | Canvas is built into OpenClaw |
| Tier 2 | `exec` | To run `git push` to YOUR repo |
| Tier 3 | None | Uses `curl` to YOUR Supabase (no special permissions) |

---

## Data Schema

The dashboard expects JSON in this format:

```json
{
  "lastUpdated": "2024-01-15T12:00:00Z",
  "actionRequired": [
    {
      "title": "Review PR #42",
      "url": "https://github.com/you/repo/pull/42",
      "priority": "high"
    }
  ],
  "activeNow": [
    {
      "task": "Deploying new feature",
      "model": "opus",
      "startedAt": "2024-01-15T11:45:00Z"
    }
  ],
  "products": [
    {
      "name": "My App",
      "url": "https://myapp.example.com",
      "status": "live",
      "lastChecked": "2024-01-15T12:00:00Z"
    }
  ],
  "crons": [
    {
      "name": "Daily Report",
      "schedule": "9:00 AM daily",
      "lastRun": "2024-01-15T09:00:00Z",
      "status": "ok",
      "errors": 0,
      "lastError": null
    }
  ],
  "recentActivity": [
    {
      "time": "2024-01-15T11:30:00Z",
      "event": "✅ Deployed v2.1.0 to production"
    }
  ]
}
```

### Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `lastUpdated` | ISO-8601 | When data was last refreshed |
| `actionRequired[].priority` | `high\|medium\|low` | Urgency level |
| `products[].status` | `live\|testing\|down` | Product health |
| `crons[].status` | `ok\|error\|paused` | Job status |

---

## Security & Privacy

✅ **All data stays on your machine** — Nothing is sent to third parties

✅ **PIN-protected** — Simple client-side access control

✅ **No install scripts** — This is an instruction-only skill; no executable code runs on install

✅ **Credentials use env vars** — No hardcoded secrets; all sensitive values come from environment variables

### Important Security Notes

⚠️ **PIN protection is client-side only** — It prevents casual access but isn't cryptographically secure. For sensitive dashboards:
- Keep your GitHub Pages repo private and use GitHub's authentication
- Use Vercel's password protection feature (paid plan)
- Don't put highly sensitive data in the dashboard

⚠️ **Supabase service key is secret** — Never put your `SUPABASE_SERVICE_KEY` in client-side code. The push script runs server-side on your OpenClaw machine.

⚠️ **RLS policies matter** — The provided SQL uses restrictive RLS: public can read, only service_role can write. Review and adjust if needed.

---

## Files Included

```
agent-dashboard/
├── SKILL.md                      # This file
├── assets/
│   ├── tier1-canvas.html         # Lightweight canvas version
│   ├── tier2-github.html         # GitHub Pages + polling
│   ├── tier3-realtime.html       # Supabase Realtime version
│   └── push-dashboard.sh         # Push script for Tier 3
├── assets/templates/
│   ├── dashboard-data.json       # Sample data structure
│   └── setup-supabase.sql        # Supabase table setup
└── references/
    └── customization.md          # Theme and layout customization
```

---

## Troubleshooting

### Dashboard shows "Disconnected" (Tier 3)
- Check Supabase project is running
- Verify anon key is correct
- Ensure realtime is enabled on the table

### Data not updating (Tier 2)
- Check GitHub Pages is enabled
- Verify `data/dashboard-data.json` was pushed
- Hard refresh the page (Ctrl+Shift+R)
- Click the Force Update button to confirm data is stale

### PIN not working
- PINs are case-sensitive
- Check you're using the same PIN in HTML config

### Cron status not accurate
- Ensure your Dashboard Update cron is running (`cron list`)
- Check for errors in the cron output
- Manually run the update: "Update my Mission Control dashboard now"

---

## Credits

Built for the OpenClaw community. MIT License.
