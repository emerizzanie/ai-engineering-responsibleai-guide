# ResponsibleAI Guide

**ResponsibleAI Guide: A RAG-Based Assistant for Responsible AI Risk Identification in Student Projects**

ResponsibleAI Guide is my semester project for AI Engineering. A student describes their AI project and the system looks through public responsible AI guidelines. Which is to explain possible risks, show where the information came from and give a simple checklist to think about.

> **Current status (Week 3):** The requirements, evaluation plan and first architecture are done. A first data pipeline now turns one guideline PDF into text pieces with page numbers. The assistant itself (search, answers, checklist) is not built yet.

---

## Problem Statement

When students build AI projects, they mostly focus on getting the model to work. Privacy, bias and trust in the output are often left until the end or forgotten.

Guidelines about these topics exist, but they are long which is written in general terms and spread across different documents. It hard for student to know which parts apply to their own project.

ResponsibleAI Guide tries to help by finding the relevant parts of the guidelines and explaining them in simple words with the source shown.

---

## Evidence of the Problem

- A scoping review in *AI & Society* found that responsible AI guidelines often have little effect in practice. Partly because the principles are too abstract. ([link](https://link.springer.com/article/10.1007/s00146-024-01880-9))
- Leça et al. (2024) interviewed software practitioners and found that some principles are like transparency and accountability and get less attention than others. ([link](https://arxiv.org/abs/2412.07620))
- A 2023 UNESCO survey found that fewer than 10% of the 450+ schools and universities in it had formal guidance on generative AI. ([link](https://www.unesco.org/en/articles/unesco-governments-must-quickly-regulate-generative-ai-schools))

I may look for student-focused evidence later.

---

## Related Work / Existing Approaches

| Existing work | What it does | Limitation |
|---|---|---|
| [NIST AI RMF 1.0](https://doi.org/10.6028/NIST.AI.100-1) | General framework for managing AI risks | Too general. Students must work out what applies to them |
| [Malaysia AIGE (2024)](https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/) | National guidelines with seven AI principles | Voluntary and general. Not interactive |
| [Let's have a chat with the EU AI Act](https://arxiv.org/abs/2505.11946) | RAG chatbot for checking systems against the EU AI Act | Focuses on EU law, not students or Malaysia |
| [gov-rag](https://github.com/rogerramos4/gov-rag) | RAG question answering over NIST AI RMF and EU AI Act | Answers questions only. No project review or checklist |
| [AI Governance & Compliance Assistant](https://github.com/Zaid0205/-AI_Governance_Assistant) | Classifies a described AI use case under EU AI Act risk levels | Mainly EU AI Act. Focused on classification, not learning |

This is a first search only. I will check for more similar tools later.

---

## Engineering Gap

The tools I found mostly focus on EU or US documents and on compliance. ResponsibleAI Guide tries to fill the gap by:

- being made for **students**, with simple explanations
- adding **Malaysian and ASEAN** guidance next to NIST
- taking a **project description** and giving a **checklist**
- being **tested properly**. Including a comparison with a normal LLM without retrieval

---

## Initial Project Objectives

1. **O1:** Collect and organise public responsible AI documents (NIST, Malaysia, ASEAN) for the assistant.
2. **O2:** Build a RAG assistant that explains possible risks in a student project, with source references and a simple checklist.
3. **O3:** Test retrieval relevance, answer correctness, citation accuracy and handling of questions the documents cannot answer and compare with a plain LLM.

---

## Target Users

- University students building AI or machine learning projects (main users)
- Student project teams
- Lecturers who want a starting point to discuss responsible AI

It is not made for professional compliance work.

---

## Proposed Data Sources

1. [NIST AI Risk Management Framework (AI 100-1)](https://doi.org/10.6028/NIST.AI.100-1)
2. [NIST Generative AI Profile (AI 600-1)](https://doi.org/10.6028/NIST.AI.600-1)
3. [Malaysia National Guidelines on AI Governance and Ethics](https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/)
4. [ASEAN Guide on AI Governance and Ethics](https://asean.org/wp-content/uploads/2024/02/ASEAN-Guide-on-AI-Governance-and-Ethics_beautified_201223_v2.pdf)
5. [UNESCO Guidance for Generative AI in Education and Research](https://unesdoc.unesco.org/ark:/48223/pf0000386693)

I record the version and date of each document and check the terms of use before adding any file to this repo. The PDFs are kept in Google Drive and copied into Colab each session. I do not commit the raw PDFs. The Malaysia AIGE PDF has a copyright notice, so it is link only.

In Week 3, **NIST AI 100-1** is the main document used to test the pipeline. The other PDFs will be added later with the same steps. Details are in `docs/week3_data_inventory.md`.

---

## Planned Tools (will change)

- **Language:** Python
- **Approach:** RAG (document processing, embeddings, vector search, LLM)
- **Interface:** Streamlit
- **Testing:** Python scripts and a set of manually checked questions

The exact libraries and LLM will be chosen later based on cost and availability.

---

## What I Have Done So Far

### Week 2: Requirements and Architecture
- Wrote the requirements (FR, NFR and Responsible AI requirements) and how each one will be tested: `docs/week2_requirements.md`, `docs/week2_evaluation_criteria.md`
- Drew the first architecture and recorded four design decisions: `docs/week2_architecture_decisions.md`
- Listed the data sources and links: `data/data_sources.md`

### Week 3: First Data Pipeline
The pipeline takes a guideline PDF and prepares it for search later:

**Source PDF → Ingest → Inspect → Prepare → Validate → Record lineage**

| Step | What happens |
|---|---|
| Ingest | Pick the exact PDF from `data/raw` |
| Inspect | Count pages and characters, find near-empty pages and the repeated page header |
| Prepare | Split the text into pieces of about 500 characters, one page at a time, and keep the PDF page number |
| Validate | Check the output file exists, is not empty and every piece has text and a page number |
| Record lineage | Save which PDF made which output, with SHA-256 fingerprints, in a manifest file |

**Main results (NIST AI 100-1, 48 pages):**
- I compared three ways of splitting the text. Fixed-size gave 213 pieces, paragraph-aware gave 214, and page-aware gave 238.
- I chose **page-aware** because the lab's basic splitter does not keep page numbers (`"page": None`), and my project needs page numbers for source references (FR3 / RAI1).
- The repeated header "NIST AI 100-1 AI RMF 1.0" is removed, and wrapped lines are joined. Line-end hyphens are kept, so words like "red-teaming" stay correct.
- The pipeline is run twice to check that the sources, outputs and mappings stay the same (Cell 9 in the notebook).

**Main limitations:**
- Only one document (NIST AI 100-1) is processed so far.
- Citations only have the PDF page number, no section title yet.
- The checks only show the files exist and match their fingerprints. They do not prove the data quality. That comes in Topic 4.

Full details: `docs/week3_pipeline_decisions.md` and `docs/week3_pipeline_diagram.png`.

---

## Repository Structure

```
notebooks/
  week3_pipeline.ipynb          Colab notebook for the data pipeline
docs/
  week1_project_scope.md
  week2_requirements.md
  week2_evaluation_criteria.md
  week2_architecture_decisions.md
  week3_data_inventory.md
  week3_pipeline_diagram.png
  week3_pipeline_decisions.md
data/
  data_sources.md               links to the documents
  week3_manifest.json           Part B: source-to-output record
  processed/                    Part B prepared text
  pipeline_v1/documents/        Part C prepared text and run manifest
README.md
```

## How to Run the Week 3 Pipeline

1. Put the five PDFs in a Google Drive folder.
2. Open `notebooks/week3_pipeline.ipynb` in Google Colab.
3. Run the cells from top to bottom (Cell 1 to Cell 9). Cell 2 copies the PDFs from Drive, so change the folder path to your own.
4. The outputs appear in `week3_project/data/` in Colab. Download them before the session ends, because Colab files are temporary.

---

## Weekly Progress

| Week | What I did / plan | Output | Status |
|------|-------------------|--------|--------|
| Week 1 | Chose the topic, wrote the project scope, set up the repo | README, project scope | Done |
| Week 2 | Requirements and system design | Requirements, evaluation criteria, architecture, decisions | Done |
| Week 3 | Data sources and first data pipeline (PDF) | Data inventory, notebook, prepared text, manifests, diagram, decisions | Done |
| Week 4 onwards | Data quality, retrieval, build, test and evaluate | Code, experiments and results | Planned |

This plan is rough and will be updated each week.

---

## Limitations

ResponsibleAI Guide is for learning and a first review only. It cannot replace expert advice, it may miss risks, and it does not approve or certify any project.

---
