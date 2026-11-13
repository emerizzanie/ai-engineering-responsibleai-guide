# Week 2: Requirements Specification (ResponsibleAI Guide)

[Back to README](../README.md) | Same week: [evaluation criteria](week2_evaluation_criteria.md), [architecture decisions](week2_architecture_decisions.md)

## Section 0: Project Framing and Application Scope Checkpoint

| Element | Details |
|---|---|
| **Problem Evidence** | Responsible AI guidelines often have little effect in real practice, partly because the principles are too abstract (review in *AI & Society*: https://link.springer.com/article/10.1007/s00146-024-01880-9). Practitioners also give less attention to some principles, like transparency and accountability (Leça et al., 2024: https://arxiv.org/abs/2412.07620). These studies are about practitioners, not students, so I treat them as supporting evidence only |
| **Existing Approaches** | 1) **NIST AI RMF 1.0**: General framework for managing AI risks. 2) **Malaysia AIGE (2024)**: National guidelines with seven AI principles. 3) **"Let's have a chat with the EU AI Act" (2025)**: RAG chatbot for checking systems against the EU AI Act. 4) **gov-rag**: an open-source RAG system for questions about NIST AI RMF and the EU AI Act. 5) **AI Governance & Compliance Assistant**: Open-source tool that classifies a described AI use case under EU AI Act risk levels. |
| **Project / Engineering Gap** | The guidelines are long and general and the similar tools I found mostly focus on EU or US documents and on compliance. I did not find one made for students that also uses Malaysian and ASEAN guidance and turns a project description into a checklist. This is a practical gap, not a claim that nothing similar exists. |
| **Objective O1** | To collect and organise public responsible AI documents (NIST, Malaysia, ASEAN) for use in a RAG assistant. |
| **Objective O2** | To build a RAG assistant that explains possible responsible AI risks in a student project, with source references and a simple checklist. |
| **Objective O3** | To evaluate retrieval relevance, answer correctness, citation accuracy and handling of questions the documents cannot answer and to compare the assistant with a plain LLM without retrieval. |
| **Intended Users** | University students building AI or ML projects (main users). Student project teams and lecturers may also use it as a starting point for discussion. |
| **Supported Tasks** | (1) Take a project description and point out responsible AI risk areas that may matter. (2) Answer questions about what the approved guideline documents say. (3) Give a preliminary checklist linked to the guideline passages. |
| **Information / Knowledge Domain** | Public responsible AI guidance only: NIST AI RMF 1.0, NIST Generative AI Profile, Malaysia AIGE, ASEAN Guide on AI Governance and Ethics, and optionally the UNESCO guidance on generative AI in education. |
| **Operating Context** | A web app used by students during the early planning stage of an AI project, for learning and first review. Not used for final approval or official assessment. |
| **Important Constraints** | Only public documents are used. About 4 to 5 documents because it is a one-semester individual project. Limited budget, so a low-cost or free LLM service is preferred. Document licences must be checked before copying files into the public repo. Students may type private details, so privacy must be handled. |
| **Explicit Out-of-Scope Tasks** | The system must not certify a project as safe or approved, give legal or compliance decisions, give medical or legal advice, review the actual code or model of a project, or answer general questions that are not about responsible AI guidance. |
| **Evaluation Focus** | Does the assistant find the right guideline passages, give correct answers with citations that really support them, say "not enough information" when the documents do not cover a question, and do this better than a plain LLM without retrieval? |

---

## Section 1: Functional Requirements

|  | Requirement: The system SHALL | Scope Boundary: SHALL NOT (where relevant) |
|---|---|---|
| **FR1** | Accept a project description written in normal language and identify the responsible AI risk areas (privacy, fairness, transparency) that are relevant to it, based on the approved documents. | State or suggest that the project is safe, compliant or approved. |
| **FR2** | Answer questions about what the approved guideline documents say. | Present information from outside the approved documents as if it came from them. |
| **FR3** | Provide a source reference for each risk or answer based on the documents. | Invent a source or cite a passage that was not retrieved. |
| **FR4** | Produce a preliminary checklist from the project description, with each checklist item linked to a retrieved guideline passage. | Present the checklist as a complete risk assessment. |
| **FR5** | Say clearly when the approved documents do not contain enough information to answer, and decline out-of-scope requests (such as certification, legal advice or unrelated topics) with a short redirect. | Guess an answer when there is not enough evidence. |
| **FR6** | Give answers in clear, plain language suitable for undergraduate students. | - |

---

## Section 2: Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| **NFR1** | Performance | The system shall respond within a reasonable time for interactive use. | Measure end-to-end response time on a defined set of test queries. | p90 latency | < 10,000 ms |
| **NFR2** | Correctness | Answers shall agree with the approved source documents. | Score answers on a manually checked set of about 30 questions using a simple rubric. | Correct response rate (%) | ≥ 80% |
| **NFR3** | Retrieval quality | The system shall retrieve at least one relevant passage for each supported question. | Run the checked question set and record whether a relevant passage appears in the top 5 retrieved results. | Hit rate at top 5 (%) | ≥ 80% |
| **NFR4** | Cost | Running costs shall stay within a student project budget. | Record LLM/API usage over the evaluation workload. | Cost per 100 queries | < RM10.00 (estimate, to be updated after the LLM is chosen) |
| **NFR5** | Maintainability | A new or updated guideline document shall be added without rebuilding unrelated parts of the system. | Run a document-update test: add one new document and rebuild only what is needed. | Time to add one document | < 15 minutes |

---

## Section 3: Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| **RAI1** | Wrong or made-up answers (transparency and grounding) | The system shall give a verifiable source reference for answers based on the documents, and the cited source shall support the answer. | Check grounded responses for citation presence, then manually check whether each cited passage supports the claim. | Citation presence rate; citation-source match rate | 100% presence; ≥ 90% match |
| **RAI2** | Students relying on it too much (scope and safety) | The system shall decline requests to certify or approve a project or to give legal advice, and shall show a "preliminary guidance only" notice with every checklist. | Run a test set of at least 20 out-of-scope or certification requests, and check every checklist output for the notice. | Refusal-and-redirect success rate; notice presence rate | ≥ 90% refusal-and-redirect; 100% notice presence |
| **RAI3** | Guessing when the documents do not cover the question (unsupported answers) | The system shall indicate when there is not enough evidence in the documents instead of inventing guidance. | Run at least 10 questions that are not covered by the documents and check the responses. | Correct "not enough information" rate | ≥ 80% |
| **RAI4** | Privacy | The system shall warn the user before sending input that appears to contain personal data, and shall not store user project descriptions after the session ends. | Run privacy test cases using made-up personal data (emails, phone numbers, ID numbers) and inspect what is stored. | Warning rate on test inputs; number of stored user inputs | ≥ 90% warning rate; 0 stored inputs |

---

## Reflection

**Which Responsible AI requirement in my project will be the most difficult to measure or enforce technically? Why?**

I think RAI1 (the citation-source match) will be the hardest. Checking if a citation exists is easy, but deciding whether the cited passage really supports the claim needs a person to read both and judge, and that takes time for every test question. It is also easy to be too generous when I check my own system's answers. A second hard point is RAI3, because a model can sound confident even when the documents do not cover the question, so it is difficult to force it to say "not enough information" every time.
