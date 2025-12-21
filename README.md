# Meeting-Followup-Agent-n8n  
### Closing the “Post-Meeting Action Gap”

AI-powered meeting follow-up workflow built in n8n that automatically triggers when meeting notes are created in Google Drive, extracts meeting context, and converts it into actionable Slack follow-ups, Jira tasks, and Google Calendar scheduling with human-in-the-loop control.

## 📌 Problem Statement

High-stakes meetings still suffer from **Action Item Fragmentation**.

Modern tools (Google Meet Notes) have significantly improved **capture**:
- They generate transcripts  
- They extract summaries  
- They surface action items with owners  

However, the real problem begins ***after*** the meeting.

### The Pain Point
Product Managers, Engineering Managers, Tech Leads, and other senior stakeholders attend **dozens of meetings per week**. Follow-ups are scattered across individual meeting notes, forcing leaders to:

- Revisit multiple meeting-note documents across days or weeks  
- Mentally reconstruct what is still pending vs. already completed  
- Manually create Jira tickets, send Slack updates, or schedule follow-ups  
- Track execution without a single, unified view  

There is **no single pane of glass** that converts meeting outcomes into **ready-to-execute actions** inside the tools where work actually happens.

The administrative cost isn’t interpretation — it’s **coordination and execution at scale**.

---

## The Solution  
### A Human-in-the-Loop, AI-Powered Meeting Follow-Up Workflow

This project is an **event-driven workflow built in n8n** that activates automatically **when meeting notes are created in Google Drive at the end of a meeting**.

Instead of asking leaders to revisit every meeting-notes document, the workflow:
- Reads the **raw meeting transcript**
- Proposes concrete, execution-ready follow-up actions
- Centralizes all proposed actions into a **single approval surface**
- Executes only what a human explicitly approves

Follow-ups are delivered directly into **Slack as interactive cards**, turning post-meeting work into a lightweight review-and-approve flow.

This acts as a **lightweight Chief of Staff for post-meeting execution** — reducing cognitive load while preserving human judgment.

---

## Why This Approach?

### Why AI?
Meetings are inherently unstructured:
- Action items are often implied rather than explicitly stated  
- Ownership may be distributed across participants  
- Priority and urgency are contextual  

AI is used here to:
- Reason over **raw conversational transcripts**
- Identify implicit and explicit follow-ups
- Normalize outcomes into **structured, executable proposals**

This is not summarization.  
This is **decision support for execution**.

---

### Why not rely solely on Google Meet “Action Items”?
#### Strategic Rationale: Custom LLM vs. Native Summarization

Google Meet action items are useful — but insufficient for execution at scale.

**Key limitations of native summaries:**
- Optimized for human readability, not automation  
- Black-box outputs with no schema guarantees  
- Limited to explicitly stated tasks  
- No awareness of historical or cross-meeting context  

**Why a custom LLM approach works better:**

| Dimension     | Native Google AI       | Custom Agent       | Why It Matters                            |
|---------------|------------------------|--------------------|-------------------------------------------|
| Output Format | Narrative text         | Strict JSON schema | Enables safe, deterministic API execution |
| Inference     | Explicit tasks only    | Intent recognition | Captures implicit follow-ups              |
| Delivery      | Pull-based (docs)      | Push-based (Slack) | Meets users where work happens            |

This workflow treats meeting notes as **input**, not the final artifact — and focuses on **execution orchestration**, not documentation.

---

### Why Slack?
Slack is where execution already happens.

From a product standpoint:
- Slack is the **system of record for coordination**
- It’s where PMs, EMs, and ICs notice work
- It minimizes context switching compared to dashboards or email

By delivering follow-ups as **interactive Slack cards**, this workflow:
- Meets users where they already work  
- Reduces friction to review and act  
- Turns follow-up into an inline decision  

Slack becomes the **approval and execution layer**, not just a notification channel.

---

### Why Human-in-the-Loop (HITL)?
In leadership contexts, automated execution carries real risk.

Incorrect messages, tickets, or meetings can:
- Create confusion  
- Damage trust  
- Introduce downstream cleanup work  

This workflow deliberately enforces a **Draft → Review → Approve** model:
- AI accelerates sense-making and proposal  
- Humans retain final authority  
- Irrelevant or low-confidence actions are safely discarded  

The AI remains an assistant — not an autonomous actor.

---

## 🛠 Product Architecture

### 1️⃣ Trigger & Context Ingestion
- **Watchdog:** Monitors a Google Drive folder for newly created meeting notes  
- **Context Retrieval:** Extracts the **raw transcript** plus metadata (meeting title, date, participants)  

The workflow activates at the **natural end of a meeting**, without manual input.

---

### 2️⃣ The AI Brain (The “Product Thinking” Core)
- **Prompt Engineering:** Classifies outcomes into:
  - Internal execution (Jira tasks)  
  - Internal coordination (Slack messages)  
  - Scheduling needs (calendar follow-ups)  
- **Structured Output:** Enforced JSON schema ensures deterministic execution  

This mirrors how you translate discussion into action.

---

### 3️⃣ The Approval Dashboard
- **Human Review Surface:** Interactive Slack cards  
- Users can:
  - ✅ Approve & Send / Create  
  - ✏️ Edit AI-drafted content  
  - ❌ Discard incorrect or irrelevant actions  

This preserves control while eliminating repetitive admin work.

---

## 📈 Success Metrics (KPIs)

This workflow is designed to optimize for:

- **Administrative Time Saved**  
- **Action Acceptance Rate**  
- **Follow-Up Latency**  
- **Discard Rate (Signal Quality)**  

These metrics directly measure cognitive load reduction without sacrificing execution quality.

---

## 🚀 Future Roadmap 

- **Formal AI Evaluation**  [IN PROGRESS]
  Precision, recall, and relevance scoring of proposed actions  

- **Priority & Risk Scoring**  
  Flag high-impact follow-ups for extra review  


---

## ⚠️ Disclaimer

This repository contains exported n8n workflows intended for experimentation and learning.

Some nodes (e.g., Jira, Google Calendar) require users to configure their own credentials after import.
