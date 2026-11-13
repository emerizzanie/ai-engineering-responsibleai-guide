# Week 2: Architecture Decisions (ResponsibleAI Guide)

[Back to README](../README.md) | Same week: [requirements](week2_requirements.md), [evaluation criteria](week2_evaluation_criteria.md)

Version: 1.0

This file explains why the V1 architecture ([`week2_architecture_v1.png`](week2_architecture_v1.png)) looks the way it does. V1 is a **conceptual** design. I label parts by what they do, not by specific tools. The tools (models, libraries, vector index, etc.) will be chosen in later weeks.

---

## 1. Starting Point

The simplest version is:

**Student → Application → AI capability → Response**

This is not enough for my project, because it has nothing to keep the answers tied to the approved documents, nothing to control scope and privacy, and no place to run my tests. The next section shows what I added and why.

---

## 2. Architecture Needs from My Requirements

| Requirement ID | What does the requirement need? | Architecture Response |
|---|---|---|
| NFR2 / NFR3 / RAI1 | Answers must be based on the approved documents and found quickly. | **Knowledge source** (approved guideline docs) and **Knowledge access** (finds relevant passages) |
| FR3 / RAI1 | Every answer needs a source reference that is real. | Source details kept with each passage (document, section/page, version) and an **Output check** |
| FR5 / RAI3 | The system must say when there is not enough evidence. | **Output check** and rules given to the language model service |
| FR1 / FR4 | Understand a project description and write explanations and a checklist. | **Language model service** |
| FR5 / RAI2 | Refuse out-of-scope requests such as "approve my project". | **Scope & privacy control** |
| RAI4 | Warn before personal data is sent to an external service. | **Scope & privacy control** (placed before the language model service) |
| RAI2 | Show a "preliminary guidance only" notice with every checklist. | **Output check** |
| O3 / NFR1 / NFR2 | Test correctness and speed, and compare with a plain LLM. | **Evaluation point** |

---

## 3. V1 Components

| Component | What it does |
|---|---|
| Student | Describes a project or asks a question. |
| Application interface | Where the student types and reads the answer and checklist. |
| Scope & privacy control | Refuses out-of-scope requests and warns about personal data before anything is sent on. |
| Knowledge source | The approved guideline documents (NIST, Malaysia AIGE, ASEAN, UNESCO) with their source details. |
| Knowledge access | Finds the passages that match the student's input. |
| Language model service | Writes the explanation and checklist using only the retrieved passages. |
| Output check | Checks that sources are included, adds the "preliminary guidance" notice, and uses "not enough information" when needed. |
| Evaluation point | Not part of what the student sees. It runs test questions, the plain LLM baseline, and records scores and response time. |

---

## 4. Architecture Decision Records (ADR)

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
|---|---|---|---|---|
| **D1: Include knowledge access** | Answers must be based on approved documents (NFR2, NFR3, RAI1) and cite sources (FR3). | 1) Plain LLM only. 2) Simple keyword search only. 3) Finding passages from approved documents and giving them to the model. | Knowledge access with source tracking | More parts to build and a bit more waiting time, but better grounding and sources that can be checked. It also lets me compare against a plain LLM. |
| **D2: Add a scope & privacy control before the language model** | Out-of-scope requests must be refused (RAI2, FR5) and personal data must not be sent without a warning (RAI4). | 1) Only write "do not do this" in the model instructions. 2) A separate control step before the model. | Separate control step | Some good questions may be refused by mistake, and it adds a bit of processing time. But it is easier to test (20 out-of-scope requests) than a rule hidden inside a prompt. |
| **D3: Use a hosted language model service** | Limited time, budget and laptop power (NFR4); one student working alone. | 1) Run a small model on my own laptop. 2) Use a hosted service. | Hosted language model service | Faster to build and likely better quality, but I depend on the provider, there may be costs, and student text leaves my computer. This is why D2 includes a privacy warning. |
| **D4: Add an output check after the model** | Citations must be real (RAI1), a notice must always show (RAI2), and uncovered questions must get "not enough information" (RAI3). | 1) Trust the model's output. 2) Check the output before showing it. | Output check | Some extra processing and the risk of blocking a good answer, but it protects against made-up sources and missing notices. |

---

## 5. Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
|---|---|---|---|---|
| O1 | FR2 / NFR3 | Retrieval relevance | Knowledge source + Knowledge access | Hit rate on the 30-question set |
| O2 | FR3 / RAI1 | Source citation and support | Knowledge access (source tracking) + Output check | Citation presence and match results |
| O2 | FR5 / RAI3 | Handling questions the documents do not cover | Output check + Language model service rules | Results on 10 uncovered questions |
| O2 | FR1 / RAI2 / RAI4 | Out-of-scope refusal and notice | Scope & privacy control + Output check | Results on 20 out-of-scope requests, privacy test cases |
| O3 | NFR2 | Answer correctness and improvement over a plain LLM | Evaluation point + Language model service | RAG score vs baseline score |
| O3 | NFR1 | Response time | Whole pipeline, measured at the Evaluation point | p90 latency from the test run |

---

## 6. Final Review

- **Does each component support a requirement or evaluation need?** Yes. Section 2 links every component to at least one requirement, and the Evaluation point supports O3.
- **Is anything important missing?** Not for V1. Things like how documents are split and stored, which retrieval method to use, and how the output check works in detail will be decided in later topics. Cost (NFR4) and maintainability (NFR5) are not shown as separate parts, but D3 and the knowledge source design will affect them.
- **Is anything unnecessary?** I kept V1 small. I did not add things like user accounts, a database for chat history or a login, because no requirement needs them.
