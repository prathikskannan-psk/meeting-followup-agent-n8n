# 🔍 AI Evaluation: Meeting Follow-Up Extractor

## 1. Summary
[cite_start]**The Evaluation:** I designed a "User Stress Test" covering **8 distinct meeting archetypes** to measure reliability, safety, and execution readiness[cite: 9, 10].

**Top-Line Verdict:**
[cite_start]The system is robust for execution-focused meetings (100% success on clear handoffs) and requires additional review in more ambiguous scenarios, where it may propose unnecessary actions during brainstorming[cite: 11, 48, 54].

---

## 2. Methodology: The "User Stress Test"
To validate "Production Readiness," I created a Golden Dataset representing real-world edge cases.

| ID | Scenario | Why This Matters |
| :--- | :--- | :--- |
| **T1** | **Clear Commitments** | Baseline check. [cite_start]If this fails, the system is not viable for automation[cite: 11]. |
| **T2** | **Ambiguous Owner** | [cite_start]Tests Detection of vague language (“someone should”, “maybe we should”)- Correct use of Unassigned instead of fabricating owners- Avoidance of creating concrete tasks from exploratory discussion
 (Risk: Noise)[cite: 12]. |
| **T3** | **No Follow-Ups** | "Watercooler" chat. [cite_start]Tests resistance to hallucinating work where none exists[cite: 12]. |
| **T4** | **Overloaded** | [cite_start]Prioritization logic when many potential actions are mentioned[cite: 12]. |
| **T5** | **Mixed Async/Sync** | [cite_start]Correct routing of actions to Slack (async), Meetings (sync), and Tasks[cite: 12]. |
| **T6** | **Multi-Owner** | [cite_start]Handling shared ownership without forcing artificial assignments[cite: 12]. |
| **T7** | **Pivot Decision** | [cite_start]Context retention: Reflecting final decisions rather than intermediate discussions[cite: 12]. |
| **T8** | **Technical Planning** | [cite_start]Quality of technical tasks and avoidance of duplicate meta-tasks[cite: 16, 17]. |

### Evaluation Criteria
I defined rigorous criteria to ensure automation safety:

**1. Gating Criterion: Schema Compliance**
* [cite_start]**Metric:** Valid JSON with correct data types and required fields[cite: 21, 22, 23].
* [cite_start]**Rule:** Any schema failure is an automatic block, regardless of other scores[cite: 24].

**2. Scored Criteria (Quality Checks)**
* [cite_start]**Category Accuracy:** Correct classification: Slack, Meeting, or Task[cite: 28].
* **Owner Attribution:** Correct owner when explicit; [cite_start]"Unassigned" when unclear[cite: 29, 30].
* [cite_start]**Execution Readiness:** Action is clear, scoped, and usable as-is for downstream execution (Slack/Calendar/Task) without requiring human rewriting[cite: 31, 32].

**3. Scoring Rubric (Per Transcript)**
* [cite_start]**2 (Pass):** Usable as-is (Ready for API)[cite: 34].
* [cite_start]**1 (Weak):** Minor edits needed[cite: 35].
* [cite_start]**0 (Fail):** Not Correct (Hallucinated or incorrect)[cite: 36].

---

## 3. Results Matrix

| Transcript | Schema | Category | Owner | Execution | Score | Verdict |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **T1 (Clean)** | ✅ Pass | 2 | 2 | 2 | **10/10** | [cite_start]🟢 Ready [cite: 38] |
| **T2 (Ambiguous)**| ✅ Pass | 0 | 1 | 0 | **1.7/10** | [cite_start]🔴 Needs Review [cite: 38] |
| **T3 (No Actions)**| ✅ Pass | 0 | 0 | 0 | **0/10** | [cite_start]🔴 Needs Review [cite: 38] |
| **T4 (Overload)** | ✅ Pass | 2 | 2 | 2 | **10/10** | [cite_start]🟢 Ready [cite: 38] |
| **T5 (Mixed)** | ✅ Pass | 2 | 2 | 2 | **10/10** | [cite_start]🟢 Ready [cite: 39] |
| **T6 (Multi)** | ✅ Pass | 2 | 2 | 2 | **10/10** | [cite_start]🟢 Ready [cite: 39] |
| **T7 (Pivot)** | ✅ Pass | 2 | 2 | 2 | **10/10** | [cite_start]🟢 Ready [cite: 39] |
| **T8 (Tech)** | ✅ Pass | 0 | 1 | 1 | **3.3/10** | [cite_start]🟡 Needs Review [cite: 39] |

[cite_start]**Average Score:** 6.9/10 [cite: 40]

Note: Scores are normalized to a 10-point scale for readability. Schema compliance is treated as a gating requirement and not included in the numeric score.
---

## 4. Failure Spotlight (The "Why")

### 🔴 Critical Risk: The "Optimistic" Hallucination (T2, T3)
[cite_start]**Observation:** In scenarios with vague language (T2) or casual chat (T3), the model aggressively tried to "be helpful" by inventing tasks[cite: 53, 54].
* **The Error:** Exploratory discussions like *"Maybe someone should look at logs"* were converted into hard commitments.
* [cite_start]**Product Impact:** This creates noise in downstream systems, breaking user confidence[cite: 12].

### 🟡 Quality Issue: Meta-Task Duplication (T8)
[cite_start]**Observation:** In complex technical planning, the model created "Meta-Tasks" (e.g., "Plan the meeting") alongside the actual work, instead of folding them into a single task[cite: 55].
* [cite_start]**Product Impact:** Increases administrative overhead for engineers[cite: 17].

---

## 6. Next Steps
* [cite_start]* [cite_start]**Improve Prompt:** When follow-ups are discussed without an explicit owner, extract the action with `"owner": "Unassigned"` rather than inferring ownership or suppressing the action.[cite: 67].
* [cite_start]**Improve Prompt:** Explicitly support a no-action outcome when no follow-up intent is expressed, rather than proposing placeholder or inferred actions.[cite: 67].
* [cite_start]**Regression Testing:** Re-run T2 and T3 to confirm fewer unnecessary actions after prompt updates[cite: 67].
* [cite_start]**Regression Check:** Ensure fixes do not degrade execution-heavy meetings (T1, T4-T7)[cite: 68].
* [cite_start]**Behavior Documentation:** Make system behavior transparent as it evolves[cite: 68].
