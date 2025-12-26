# 🔍 AI Evaluation: Meeting Follow-Up Extractor — v2

## 1. Summary

### The Evaluation
Building on v1, I re-evaluated the system after introducing intent-based action gating, explicit support for unassigned ownership, and send-ready Slack outputs. The same 8 meeting archetypes were re-used to enable direct comparison and regression analysis.

### Top-Line Verdict
The system is reliable in execution-heavy meetings and now handles ambiguous and informal meetings safely, avoiding hallucinated actions while preserving real work with explicit uncertainty.

### Key Improvement vs v1
Hallucinated actions in brainstorming and informal meetings were eliminated, while execution readiness (Slack / Tasks / Meetings) was preserved or improved.

**📄 Raw Evaluation Data (v2):**
[View Google Sheet](https://docs.google.com/spreadsheets/d/1mlgEvVtZj0DnaAw-jidXE_qzvxHbkL8bM4BfQ1kQhvo/edit?usp=sharing)

---

## 2. Methodology: The “User Stress Test”
To measure production readiness, I reused the same Golden Dataset from v1, representing common real-world meeting patterns and edge cases.

| ID | Scenario | Why This Matters |
| :--- | :--- | :--- |
| **T1** | **Clear Commitments** | Baseline check — failure here blocks automation entirely. |
| **T2** | **Ambiguous Owner** | Ensures vague language results in "Unassigned" ownership rather than inferred owners or dropped actions. |
| **T3** | **No Follow-Ups** | Validates resistance to hallucinating work in purely informational meetings. |
| **T4** | **Overloaded** | Tests prioritization and signal-to-noise handling when many actions are discussed. |
| **T5** | **Mixed Async / Sync** | Verifies correct routing to Slack (async), Meetings (sync), and Tasks. |
| **T6** | **Multi-Owner** | Ensures shared ownership is preserved without artificial consolidation. |
| **T7** | **Pivot Decision** | Tests context retention and reflection of final decisions. |
| **T8** | **Technical Planning** | Evaluates task quality and avoidance of meta-task duplication. |

---

## 3. Evaluation Criteria

### 1️⃣ Gating Criterion: Schema Compliance
* **Metric:** Valid JSON with required fields and correct data types.
* **Rule:** Any schema failure is an automatic block (not scored).

### 2️⃣ Scored Criteria (0–2 each)
* **Category Accuracy:** Correct Slack / Meeting / Task classification.
* **Owner Attribution:** Explicit owner or "Unassigned" when unclear.
* **Execution Readiness:** Action usable as-is (send-ready Slack, scoped tasks, valid agendas).

### 3️⃣ Scoring Rubric
* **2 (Pass):** Usable as-is.
* **1 (Weak):** Minor edits needed.
* **0 (Fail):** Incorrect or hallucinated.

*Note: Scores are normalized to a 10-point scale. Schema compliance is treated as a gating requirement and excluded from the numeric score.*

---

## 4. Results Matrix (v2)

| Transcript | Schema | Category | Owner | Execution | Score | Verdict |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **T1 (Clean)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T2 (Ambiguous)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T3 (No Actions)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T4 (Overload)** | ✅ Pass | 2 | 1 | 1 | **6.7/10** | 🟡 Review |
| **T5 (Mixed)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T6 (Multi)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T7 (Pivot)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T8 (Tech)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |

**Average Score (v2):** 9.6 / 10

---

## 5. Failure Spotlight & Improvements

### ✅ Fixed: “Optimistic” Hallucination (v1 → v2)
* **Before (v1):** Brainstorming language created hard tasks; informal check-ins produced placeholder actions.
* **After (v2):** No-action outcomes correctly return `actions: []`. Implicit requirements are preserved with `"owner": "Unassigned"`.
* **Impact:** Significantly reduced downstream noise and improved trust.

### 🟡 Remaining Limitation: Overloaded Meetings (T4)
* **Observation:** In meetings with many parallel workstreams, the system may still over-extract or use role-level owners (e.g., “Eng”, “Design”).
* **Why this is acceptable:** A human-in-the-loop approval step exists, and no latency is introduced. 

---

## 6. Next Steps
* **Refine Overload Handling:** Improve prioritization and consolidation in highly dense meetings.
* **Regression Testing:**
