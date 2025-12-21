# n8n Workflows

This folder contains the core **n8n workflow exports** that power the *Meeting Follow-up Agent*.

Each workflow is provided as a JSON file and can be imported directly into an n8n instance.  
The workflows are designed to work **together** as a single system that converts meeting notes into execution-ready follow-ups with human-in-the-loop control.

---

## 📂 Workflows Included

### 1️⃣ `meeting-followup-agent.json`
**Purpose:** Detection, context ingestion, and AI reasoning

This workflow is responsible for:
- Monitoring a Google Drive folder for newly created meeting notes
- Extracting the **raw meeting transcript** and metadata (meeting title, date, participants)
- Sending the transcript to an LLM using a structured prompt
- Producing **execution-ready follow-up proposals** in a strict JSON schema

The AI proposes actions across three categories:
- Slack follow-up messages
- Jira task creation
- Calendar scheduling needs

⚠️ This workflow **does not execute actions**.  
It only proposes structured follow-ups.

Think of this workflow as the **AI Brain** of the system.

---

### 2️⃣ `meeting-followup-slack-actions.json`
**Purpose:** Human approval, editing, and execution

This workflow is responsible for:
- Rendering AI-proposed follow-ups as **interactive Slack cards**
- Allowing users to:
  - ✅ Approve & execute actions
  - ✏️ Edit AI-generated content
  - ❌ Discard irrelevant or incorrect actions
- Executing approved actions via:
  - Slack API (messages)
  - Jira API (task creation)
  - Google Calendar API (meeting scheduling)

All execution happens **only after explicit human approval**.

Think of this workflow as the **Execution & Control Plane**.

---

## 🔄 How the Workflows Work Together

Google Drive (Meeting Notes Created)
↓
[ Meeting follow-up agent ]
↓
AI-proposed Actions (Structured JSON)
↓
[ Slack Actions Workflow ]
↓
Slack Approval Dashboard
↓
Approved Actions → Slack / Jira / Google Calendar


