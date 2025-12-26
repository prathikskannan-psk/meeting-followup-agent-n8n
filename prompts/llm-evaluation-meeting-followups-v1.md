# 🔍 AI Evaluation: Meeting Follow-Up Extractor

## 1. Summary
**The Evaluation:** I designed a "User Stress Test" covering **8 distinct meeting archetypes** to measure reliability, safety, and execution readiness.

**Top-Line Verdict:**
The system is robust for execution-focused meetings (100% success on clear handoffs) and requires additional review in more ambiguous scenarios, where it may propose unnecessary actions during brainstorming.

**📄 Raw Evaluation Data (v1):**
[View Google Sheet](https://docs.google.com/spreadsheets/d/1mlgEvVtZj0DnaAw-jidXE_qzvxHbkL8bM4BfQ1kQhvo/edit?usp=sharing)

---

## 2. Methodology: The "User Stress Test"
To validate "Production Readiness," I created a Golden Dataset representing real-world edge cases.

| ID | Scenario | Why This Matters |
| :--- | :--- | :--- |
| **T1** | **Clear Commitments** | Baseline check. If this fails, the system is not viable for automation. |
| **T2** | **Ambiguous Owner** | Tests Detection of vague language (“someone should”, “maybe we should”)- Correct use of Unassigned instead of fabricating owners- Avoidance of creating concrete tasks from exploratory discussion |
| **T3** | **No Follow-Ups** | "Watercooler" chat. Tests resistance to hallucinating work where none exists. |
| **T4** | **Overloaded** | Prioritization logic when many potential actions are mentioned. |
| **T5** | **Mixed Async/Sync** | Correct routing of actions to Slack (async), Meetings (sync), and Tasks. |
| **T6** | **Multi-Owner** | Handling shared ownership without forcing artificial assignments. |
| **T7** | **Pivot Decision** | Context retention: Reflecting final decisions rather than intermediate discussions. |
| **T8** | **Technical Planning** | Quality of technical tasks and avoidance of duplicate meta-tasks. |

### Evaluation Criteria
I defined rigorous criteria to ensure automation safety:

**1. Gating Criterion: Schema Compliance**
* **Metric:** Valid JSON with correct data types and required fields.
* **Rule:** Any schema failure is an automatic block, regardless of other scores.

**2. Scored Criteria (Quality Checks)**
* **Category Accuracy:** Correct classification: Slack, Meeting, or Task.
* **Owner Attribution:** Correct owner when explicit; "Unassigned" when unclear.
* **Execution Readiness:** Action is clear, scoped, and usable as-is for downstream execution (Slack/Calendar/Task) without requiring human rewriting.

**3. Scoring Rubric (Per Transcript)**
* **2 (Pass):** Usable as-is (Ready for API).
* **1 (Weak):** Minor edits needed.
* **0 (Fail):** Not Correct (Hallucinated or incorrect).

---

## 3. Results Matrix

| Transcript | Schema | Category | Owner | Execution | Score | Verdict |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **T1 (Clean)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T2 (Ambiguous)**| ✅ Pass | 0 | 1 | 0 | **1.7/10** | 🔴 Needs Review |
| **T3 (No Actions)**| ✅ Pass | 0 | 0 | 0 | **0/10** | 🔴 Needs Review |
| **T4 (Overload)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T5 (Mixed)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T6 (Multi)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T7 (Pivot)** | ✅ Pass | 2 | 2 | 2 | **10/10** | 🟢 Ready |
| **T8 (Tech)** | ✅ Pass | 0 | 1 | 1 | **3.3/10** | 🟡 Needs Review |

**Average Score:** 6.9/10

*Note: Scores are normalized to a 10-point scale for readability. Schema compliance is treated as a gating requirement and not included in the numeric score.*

---

## 4. Failure Spotlight (The "Why")

### 🔴 Critical Risk: The "Optimistic" Hallucination (T2, T3)
**Observation:** In scenarios with vague language (T2) or casual chat (T3), the model aggressively tried to "be helpful" by inventing tasks.
* **The Error:** Exploratory discussions like *"Maybe someone should look at logs"* were converted into hard commitments.
* **Product Impact:** This creates noise in downstream systems, breaking user confidence.

### 🟡 Quality Issue: Meta-Task Duplication (T8)
**Observation:** In complex technical planning, the model created "Meta-Tasks" (e.g., "Plan the meeting") alongside the actual work, instead of folding them into a single task.
* **Product Impact:** Increases administrative overhead for engineers.

---

## 6. Next Steps
* **Improve Prompt:** When follow-ups are discussed without an explicit owner, extract the action with `"owner": "Unassigned"` rather than inferring ownership or suppressing the action.
* **Improve Prompt:** Explicitly support a no-action outcome when no follow-up intent is expressed, rather than proposing placeholder or inferred actions.
* **Regression Testing:** Re-run T2 and T3 to confirm fewer unnecessary actions after prompt updates.
* **Regression Check:** Ensure fixes do not degrade execution-heavy meetings (T1, T4-T7).
* **Behavior Documentation:** Make system behavior transparent as it evolves.
