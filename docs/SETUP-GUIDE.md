# Setup Guide — Meeting Follow-up Agent (n8n)

This guide covers **only** how to set up after importing them into **n8n**.

---

## 📦 What’s Included

This repository contains **two n8n workflows** that must both be imported:

| Workflow                              | Purpose                                                                                                                              |
|---------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| `meeting-followup-agent.json`         | Detects new meeting notes in Google Drive, extracts the raw transcript, and generates AI-proposed follow-up actions                  |
| `meeting-followup-slack-actions.json` | Renders follow-ups as interactive Slack cards and executes approved actions (Slack messages, Jira tasks, Google Calendar scheduling) |

---

## ⚙️ Prerequisites

You will need:

- An **n8n instance** (local, Docker, or n8n Cloud)
- A **Slack workspace** where you can install a custom Slack app
- A **Google account** with:
  - Google Drive
  - Google Calendar
- A **Jira Cloud** instance  

---

## 🧩 Step 1 — Import the Workflows into n8n

1. Open **n8n**
2. Click **Import Workflow**
3. Import both JSON files:
   - `meeting-followup-agent.json`
   - `meeting-followup-slack-actions.json`
4. Save both workflows  
   ⚠️ Do **not** activate them yet

---

## 🔐 Step 2 — Create and Configure the Slack App

### 2.1 Create the App
1. Go to https://api.slack.com/apps
2. Click **Create New App → From Scratch**
3. Choose your workspace

---

### 2.2 Add Bot Token Scopes 

Add the following **Bot Token Scopes**:
- `chat:write`
- `chat:write.public` 
- `im:write`
- `mpim:write`
- `users:read`
- `users:read.email`
- `channels:read`
- `groups:read`
- `channels:history`
- `groups:history`
- `im:history`
- `mpim:history`

After adding scopes:
1. Click **Install App to Workspace**
2. Copy the **Bot User OAuth Token** (`xoxb-...`)

---

### 2.3 Enable Interactivity
1. Go to **Interactivity & Shortcuts**
2. Toggle **Interactivity: ON**

---

### 2.4 Set the Interactivity Request URL (CRITICAL)

Slack must send button + modal events to n8n.

1. Open workflow: `meeting-followup-slack-actions`
2. Locate the **Webhook Trigger** that receives Slack interactions
3. Copy the **Production Webhook URL**
4. Paste it into Slack → **Interactivity Request URL**
5. Save

✅ This enables **Approve / Edit / Discard / Find Times** actions.

---

## 🧠 Step 3 — Configure the `Config` Node(s)

Both workflows use a **Config** node for environment variables.

Open the `Config` node and populate:

| Field | Description |
|-----|------------|
| `SLACK_BOT_TOKEN` | Slack bot token (`xoxb-...`) |
| `slack_channel` | Channel name OR channel ID where cards are posted |


### Accepted `slack_channel` formats
All of these are supported:
- `n8n-followups-internal`
- `#n8n-followups-internal`
- `C0123456789`

---

## 📁 Step 4 — Google Drive Setup (Meeting Notes Trigger)

By default, **Google Meet automatically stores meeting artifacts** (recordings, transcripts, notes) in a system-managed folder in Google Drive.

In most workspaces, this folder is named:

**`Meeting recordings`**

This workflow is designed to watch that folder directly.

---

## 🔑 Step 5 — Credentials Setup in n8n

Some nodes are **instance-bound** and require credentials.

Use OAuth credentials configured in n8n in 
- Attach Slack credentials to Slack nodes 
- Attach Google credentials to Google Drive trigger / download nodes
- Attach Google credentials to Google Calendar create-event nodes
- Attach Jira credentials to Jira nodes.

---

## ▶️ Step 6 — Activate the Workflows

Activate **both** workflows:
1. `meeting-followup-agent`
2. `meeting-followup-slack-actions`
