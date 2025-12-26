# Prompt v2: Changelog & Design Philosophy

## 1. What Changed vs. What Improved
v2 shifts the system from "maximum extraction" to "intent-based gating," prioritizing trust and automation safety.

| Area | Prompt v1 | Prompt v2 Improvement |
| :--- | :--- | :--- |
| **Action Gating** | Always extracted 3-10 items | **Commitment Gate:** Extracts actions *only* when intent exists. |
| **Zero Outcomes** | Forced output (Hallucination risk) | **No-Action Support:** Returns `[]` for informal meetings. |
| **Ownership** | Often inferred or guessed | **Explicit Uncertainty:** Uses `"Unassigned"` rather than guessing. |
| **Slack Actions** | Instructional summaries | **Send-Ready Drafts:** First-person, execution-ready messages. |
| **Task Quality** | Variable specificity | **Structured:** Includes acceptance criteria & standardized duration. |
| **Safety** | Medium | **High:** Intent-based gating + Human-in-the-Loop design. |

> **Verdict:** Prompt v2 introduces intent-based action gating, explicit uncertainty handling, and execution-ready outputs, significantly improving reliability and trust while remaining suitable for human-in-the-loop automation.

---

## 🧠 Design Decisions & Tradeoffs
We prioritized **reducing noise** over **capturing every possible follow-up** to ensure the system is safe for downstream automation.

### 1️⃣ Intent-Based Action Gating
* **Decision:** Actions are extracted only when there is clear intent that work must be done. Speculation, past-tense updates, and brainstorming are excluded.
* **Rationale:** In automation workflows, false positives are more damaging than false negatives. Invented actions create noise in downstream systems (Jira, Slack) and erode user trust. This design assumes human review as a guardrail, not as a workaround for model failure.
* **Tradeoff:** Some low-confidence but potentially useful follow-ups may be omitted.
* **Mitigation:** Relies on Human-in-the-Loop (HITL) review to capture edge cases manually.

### 2️⃣ Preserving Uncertainty ("Unassigned")
* **Decision:** When work is required but no owner is explicitly named, the action is retained with `"owner": "Unassigned"` rather than suppressing it or guessing.
* **Rationale:** Suppressing actions hides important work, while guessing owners creates accountability risks. Explicit uncertainty allows humans to resolve assignment downstream.
* **Mitigation:** Natural fit for approval workflows where assignment is finalized before execution.

### 3️⃣ Supporting No-Action Outcomes
* **Decision:** Explicitly support returning zero actions (`"actions": []`) when a meeting is informational.
* **Rationale:** Forcing output where none exists leads to hallucinations. A valid "no action" result is a success state for automation reliability.

### 4️⃣ Execution-Ready Outputs
* **Decision:** Slack messages are generated as first-person drafts; Tasks include acceptance criteria and standardized durations.
* **Rationale:** The goal is automation. Outputs that require human rewriting increase friction and defeat the purpose of the agent.
* **Tradeoff:** Constrains the model’s stylistic freedom.

### 5️⃣ Schema Strictness as a Gate
* **Decision:** Schema compliance is treated as a hard gate.
* **Rationale:** Invalid structure breaks downstream APIs. Reliability at the integration layer is non-negotiable.

### 6️⃣ Complexity Management
* **Decision:** The system does not attempt to perfectly optimize overloaded meetings with many parallel tasks.
* **Rationale:** Over-optimizing for edge cases early increases complexity. Some ambiguity is acceptable when a HITL step exists.

---

## 🎨 Design Philosophy Summary
The system intentionally prioritizes:
1.  **Trust over completeness**
2.  **Explicit uncertainty over silent assumptions**
3.  **Execution safety over clever inference**

These choices align with real-world AI product constraints, where systems must earn user trust before they can be safely automated further.
