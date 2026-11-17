# Week 2: Evaluation Criteria (ResponsibleAI Guide)

[Back to README](../README.md) | Same week: [requirements](week2_requirements.md), [architecture decisions](week2_architecture_decisions.md)

Version: 1.0

In the [requirements file](week2_requirements.md), I wrote down what the system must do, how well it should perform, and what responsible behaviour it needs. This file explains **how I will check** if the most important requirements are met.

In Week 2 I picked 7 criteria. For the proposal I added 2 more (marked *added for the proposal*), because O1 and the checklist (FR4) had no criterion. The 9 criteria now cover all three objectives:

- **O1:** Collect and organise the public responsible AI documents
- **O2:** Build the RAG assistant (risk explanations, sources, checklist)
- **O3:** Evaluate the assistant and compare it with a plain LLM

---

## Evaluation Criteria Table

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
|---|---|---|---|---|---|---|
| O3 / NFR2 | Answer correctness | Domain-specific capability | Ask a manually checked set of about 30 questions about the documents. Mark each answer as correct if the main point matches the source and nothing is made up. | Correct response rate (%) | ≥ 80% | Students get answers about the guidelines that they can trust for early planning. |
| O3 / NFR3 | Retrieval relevance | Domain-specific capability | For each question in the same set, check if at least one relevant passage is in the top 5 retrieved results. | Hit rate at top 5 (%) | ≥ 80% | The assistant finds the right part of the guidelines, so the answer is based on the right evidence. |
| O2 / FR3 / RAI1 | Source citation and support | Instruction-following capability | Check every document-based answer for a citation, then read the cited passage to see if it really supports the claim. | Citation presence rate; citation-source match rate | 100% presence; ≥ 90% match | Students can open the original document and verify what the assistant said. |
| O2 / FR5 / RAI3 | Handling questions the documents do not cover | Generation capability | Ask at least 10 questions that are not in the documents and check if the assistant says there is not enough information. | Correct "not enough information" rate | ≥ 80% | Students are not given invented guidance that sounds real. |
| O2 / FR1 / RAI2 | Out-of-scope refusal and notice | Instruction-following capability | Send at least 20 out-of-scope requests (for example "approve my project" or "is this legal?") and check every checklist output for the "preliminary guidance only" notice. | Refusal-and-redirect success rate; notice presence rate | ≥ 90% refusal-and-redirect; 100% notice | Students do not treat the tool as an approval or as legal advice. |
| O3 / NFR1 | Response time | Cost and latency | Measure end-to-end response time over the same test queries. | p90 latency | < 10,000 ms | Students get an answer without waiting too long. |
| O3 / NFR2 | Improvement over a plain LLM | Domain-specific capability | Run the same question set on a plain LLM without document retrieval and compare its score with the RAG system. | Difference in correct response rate (percentage points) | RAG higher by ≥ 10 points | Shows that using the documents really helps and is worth the extra work. |
| O1 / FR2 / NFR5 | Knowledge base coverage and quality *(added for the proposal)* | Domain-specific capability | Run the data pipeline and the Week 4 Rule 1 and Rule 2 on every prepared source, then check the manifest. | Required sources prepared (S01 to S04); Rule 1 and Rule 2 pass rates; manifest fields still "Not verified" | 4 of 4; 100%; 100% after human review; 0 | The assistant can answer general **and** Malaysian/ASEAN questions with sources that can be traced. |
| O2 / FR4 / RAI1 | Checklist relevance and linking *(added for the proposal)* | Generation capability | Run the 3 to 5 sample project descriptions. For each checklist item, judge "relevant to this project: yes/no" and check that it cites a retrieved chunk. | % of items relevant; % of items linked to a retrieved chunk | ≥ 80% relevant; 100% linked | Students get a checklist that fits their own project, not a generic list. |

---

## Notes

- **Correctness rule:** An answer is "correct" only if its main point matches the source document. I will mark it "wrong" if it adds something that is not in the sources, even if it sounds reasonable.
- **Test sets I need to prepare later:** about 30 questions with reference answers, 10 questions the documents do not cover, 20 out-of-scope requests, and 3 to 5 sample project descriptions (for example a student FAQ chatbot or an image classifier) for checking the checklist. Examples of each set are in [`evaluation/test_set_examples.md`](../evaluation/test_set_examples.md).
- **Thresholds are first guesses.** I may change them after the first test run if they turn out to be unrealistic. The 10-point improvement over the plain LLM is also a starting target, not a fixed number.
- **Human judgement:** Citation match and correctness need me to read the answers myself. To reduce bias, I will write the reference answers before looking at the system's output.
- **Not in this table:** Privacy (RAI4), cost (NFR4) and maintainability (NFR5) are in the requirements file. How each one will be tested is in [proposal design, section 5](proposal_design.md#5-evaluation-additions).

---

## Reflection

**Which evaluation criterion is most important for deciding whether my AI application will be useful to its intended users? Why?**

I think source citation and support is the most important. The assistant gives guidance about responsible AI, and students will use it to decide what to worry about in their own projects. If a citation looks real but does not support the answer, a student could trust something wrong without noticing. Correctness matters too, but a good citation lets the student check the answer by themselves, so it protects them even when the assistant makes a mistake.
