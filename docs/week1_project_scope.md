# Week 1 Project Scope: ResponsibleAI Guide

## 1. Project Name and Domain

**Project name:** ResponsibleAI Guide

**Full title:** ResponsibleAI Guide: A RAG-Based Assistant for Responsible AI Risk Identification in Student Projects

**Domain:** AI Engineering (Generative AI and Responsible AI), in a university setting

ResponsibleAI Guide is a study assistant for students who are building an AI project. The student describes their project in normal language, for example "a chatbot that answers questions using university PDFs". The system then searches a small set of public responsible AI guidelines, explains which risks might matter for that project, shows where the information came from and gives a simple checklist to think about.

It uses Retrieval-Augmented Generation (RAG). In simple terms, the system first finds the most relevant parts of the documents, then gives those parts to a large language model (LLM) so the answer is based on real sources and not only on what the model remembers.

---

## 2. Problem Statement

When students build an AI project, most of the focus goes on making the model work. Things like privacy, bias, and whether users can trust the output are often left until the end, or forgotten completely.

Guidelines for these topics do exist, but they are long, written in general terms, and spread across different documents. A student may not know which parts apply to their own project, and reading everything takes a lot of time.

ResponsibleAI Guide tries to make this easier. The student describes their project, and the system points to the relevant guidance in simple words with a reference to the source.

An AI-based solution makes sense here because an LLM can understand a project description written in everyday language and explain the guidance clearly. A normal keyword search would not do this well. RAG is used so that the answers are tied to the real documents, which reduces the chance of the model making things up.

ResponsibleAI Guide is for learning and early review only. It does not replace a teacher or an expert, and it does not decide whether a project is "safe".

---

## 3. Evidence of the Problem

The guidelines themselves (NIST, UNESCO, etc.) only show that guidance exists. They do not show that people find it hard to use. So the evidence below is from studies and surveys.

**Evidence 1: Responsible AI guidelines often do not change real practice**
A scoping review in *AI & Society* looked at why responsible AI guidelines have little impact in practice. One of the main reasons it found is that the principles are too abstract, so practitioners have to work out for themselves how to turn them into actual tasks.
Source: https://link.springer.com/article/10.1007/s00146-024-01880-9

**Evidence 2: Practitioners find some principles harder to apply**
Leça, Bento and de Souza Santos (2024) interviewed software practitioners about responsible AI. They found that fairness, inclusiveness and reliability get attention more often, while transparency and accountability get less.
Source: https://arxiv.org/abs/2412.07620

**Evidence 3: Universities were not ready with guidance**
In 2023, UNESCO reported that fewer than 10% of the 450+ schools and universities in its survey had policies or formal guidance on using generative AI.
Source: https://www.unesco.org/en/articles/unesco-governments-must-quickly-regulate-generative-ai-schools

**Limitation of this evidence:** These sources are about industry practitioners and institutions, not about students directly.

---

## 4. Initial Related Work

**4.1 NIST AI Risk Management Framework (AI RMF 1.0), 2023**
A voluntary framework from the US National Institute of Standards and Technology. It organises AI risk management into four functions: Govern, Map, Measure and Manage.
*Limitation:* It is a general framework. A student still has to read it and figure out which parts match their own project.
Link: https://doi.org/10.6028/NIST.AI.100-1

**4.2 Malaysia National Guidelines on AI Governance and Ethics (AIGE), 2024**
Launched by MOSTI in September 2024. It gives seven AI principles and guidance for developers, users and policymakers in Malaysia.
*Limitation:* It is voluntary and written in general terms. It is not interactive, so it cannot review a specific student project.
Link: https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/

**4.3 "Let's have a chat with the EU AI Act" (arXiv, 2025)**
A research paper that builds a RAG chatbot to help AI developers check their systems against the EU AI Act and related standards.
*Limitation (based on the abstract):* It focuses on the EU AI Act and on compliance. It is not made for students and does not cover Malaysian or ASEAN guidance.
Link: https://arxiv.org/abs/2505.11946

**4.4 gov-rag (open-source GitHub project)**
A RAG system for answering questions about the NIST AI RMF and the EU AI Act. It gives page citations, can say when it does not know, and is tested with a fixed set of questions.
*Limitation:* It answers questions only. It does not take a project description or produce a checklist. It is also a personal project, not a peer-reviewed study.
Link: https://github.com/rogerramos4/gov-rag

**4.5 AI Governance & Compliance Assistant (open-source GitHub project)**
A RAG tool that takes a described AI use case and classifies it under the EU AI Act risk levels, citing the articles it used.
*Limitation:* It mainly covers the EU AI Act and focuses on risk classification, not on helping students learn. It is also not peer-reviewed.
Link: https://github.com/Zaid0205/-AI_Governance_Assistant

*Note:* This is only a first search, so there may be other similar tools.

---

## 5. Engineering Gap / Limitation

The frameworks give good guidance, but they are long and general. The similar tools I found focus mostly on EU or US documents and on compliance, not on helping students.

The gap ResponsibleAI Guide tries to cover:

- It is made for **students**, so the explanations are in simple language.
- It uses **Malaysian and ASEAN guidance** together with NIST, which the tools above do not.
- It takes a **project description** and gives a **checklist**, not only answers to single questions.
- It is **tested properly**, including a comparison with a normal LLM that has no document retrieval.

The main engineering challenge is making sure the system finds the right passages and that its answers match what the sources actually say. I will test this and not just assume RAG works.

---

## 6. Initial Project Objectives

**O1:** To collect and organise public responsible AI documents (NIST, Malaysia, ASEAN) for use in a RAG assistant.

**O2:** To build a RAG assistant that explains possible responsible AI risks in a student project, with source references and a simple checklist.

**O3:** To test the assistant on retrieval relevance, answer correctness, citation accuracy and how it handles questions the documents cannot answer, and to compare it with a plain LLM without retrieval.

---

## 7. Target Users

The main users are university students who are building AI or machine learning projects and want to know what responsible AI issues to think about.

Other possible users are student project teams and lecturers who want a starting point for discussing responsible AI in class.

It is **not** made for professional compliance officers or for official certification.

---

## 8. Data Sources

| # | Document | Why I want to use it | URL |
|---|----------|---------------------|-----|
| 1 | NIST AI Risk Management Framework (AI RMF 1.0), NIST AI 100-1 | General guidance on finding and managing AI risks | https://doi.org/10.6028/NIST.AI.100-1 |
| 2 | NIST Generative AI Profile, NIST AI 600-1 | Extra risks for projects that use LLMs and generative AI | https://doi.org/10.6028/NIST.AI.600-1 |
| 3 | Malaysia National Guidelines on AI Governance and Ethics (AIGE) | Local guidance that matches where I study | https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/ |
| 4 | ASEAN Guide on AI Governance and Ethics | Regional guidance with principles and a risk assessment template | https://asean.org/wp-content/uploads/2024/02/ASEAN-Guide-on-AI-Governance-and-Ethics_beautified_201223_v2.pdf |
| 5  | UNESCO Guidance for Generative AI in Education and Research | Extra background on privacy and generative AI in education | https://unesdoc.unesco.org/ark:/48223/pf0000386693 |

For each document I will record the title, publisher, year and version, and the link.

---

## 9. Comparison with EduAdvisor

**Similar:**
- Both use RAG to find parts of documents and answer questions from them.
- Both need to give source-based answers and avoid making things up.
- Both are tested on accuracy and on how well they use their sources.

**Different:**
- EduAdvisor answers academic advising questions (courses, policies, graduation) using university documents. ResponsibleAI Guide helps with responsible AI using NIST, Malaysian and ASEAN guidelines.
- EduAdvisor mostly answers a question. ResponsibleAI Guide also takes a project description and produces a checklist.
- ResponsibleAI Guide will be compared with a plain LLM baseline, to see if retrieval really helps.

---

## 10. Responsible AI Risks

**Risk 1: Wrong or made-up answers**
The LLM might state something that is not in the documents, or misunderstand a guideline.
*Plan:* Make the model answer only from retrieved text, show the source, and say "not enough information" when the documents do not cover the question. Test this with a set of checked questions.

**Risk 2: Oversimplifying the guidelines**
Explaining things in simple words may remove important details or apply a guideline in the wrong situation.
*Plan:* Always link back to the original passage and say clearly that the output is a first review only.

**Risk 3: Outdated documents**
Guidelines can be updated, and NIST has said it may revise its framework.
*Plan:* Record the version and date of every document and check them again from time to time.

**Risk 4: Students relying on it too much**
A student might think their project is safe just because the checklist did not mention a problem.
*Plan:* Show a clear message that this is not a full risk assessment and not a safety approval. The student is still responsible for their own project.

**Risk 5 (extra): Privacy**
Students might type private details into the system, and the text may be sent to an external LLM service.
*Plan:* Tell users not to enter personal data, and check how the chosen LLM service handles the data before using it.

---

## Project Boundary

For the first version, ResponsibleAI Guide will only cover a small set of public documents, retrieval, LLM answers with sources, a simple checklist, and basic testing.

It will not try to cover every framework, give legal or compliance decisions, or approve any project.