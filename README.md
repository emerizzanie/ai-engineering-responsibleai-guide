# ResponsibleAI Guide

**A RAG-based assistant for responsible AI risk identification in student projects**

> A study assistant that reads public responsible AI guidelines and explains, with sources, which risks a student should think about in their own AI project.

This is my individual semester project for SAIA3113 AI Engineering at UTM. A student describes their AI project, and the system searches public responsible AI guidelines, explains possible risks, shows where each point comes from and gives a simple checklist.

RAG (Retrieval-Augmented Generation) means the system first finds the relevant parts of the documents, then gives them to a language model, so the answer is based on real sources.

**Status (proposal, Nov 2026):** The requirements, evaluation plan, architecture, first data pipeline and data quality audit are done (Topics 1 to 4). The audit ended with **GO WITH CONDITIONS**. The [proposal design](docs/proposal_design.md) picks the techniques for each part (V2). The assistant itself (search, answers, checklist) is not built yet.

---

## Weekly Progress

| Week | Topic | Main files | Status |
|---|---|---|---|
| 1 | Project scope and AI system observation (group lab) | [Project scope](docs/week1_project_scope.md), [observation sheet](docs/Group01_Topic1_Lab/week1_observation_sheet.md), [system map](docs/Group01_Topic1_Lab/week1_system_map.jpeg) | Done |
| 2 | Requirements, evaluation and architecture | [Requirements](docs/week2_requirements.md), [evaluation criteria](docs/week2_evaluation_criteria.md), [architecture decisions](docs/week2_architecture_decisions.md), [diagram](docs/week2_architecture_v1.png) | Done |
| 3 | Data sources and first data pipeline | [Data inventory](docs/week3_data_inventory.md), [pipeline decisions](docs/week3_pipeline_decisions.md), [diagram](docs/week3_pipeline_diagram.png), [notebook](notebooks/week3_pipeline.ipynb) | Done |
| 4 | Data quality audit | [Audit report](docs/week4_data_quality_audit.md), [notebook](notebooks/week4_data_quality_audit.ipynb) | Done, with conditions to fix |
| Proposal | V2 architecture, technique choices, test set examples, data rights decision | [Proposal design](docs/proposal_design.md), [V2 diagram](docs/figures/architecture_v2_proposed.png), [test set examples](evaluation/test_set_examples.md), [draft prompt](prompts/answer_prompt_v0.md) | Done (design only, nothing built) |
| Next | Clean the pipeline, add documents, build and test the assistant | Not started | Planned |

## How to Navigate

| Folder | What is inside |
|---|---|
| [`docs/`](docs/) | One or more files per week, named `weekN_...`, plus `proposal_design.md` and the proposal diagrams in `figures/`. Start here. |
| [`notebooks/`](notebooks/) | The Week 3 pipeline notebook and the Week 4 audit notebook, with saved outputs. `week4_audit/` holds the audit evidence files. |
| [`data/`](data/) | [`data_sources.md`](data/data_sources.md) (links), `raw/` (NIST and UNESCO PDFs; S03 and S04 are link only), `processed/` and `pipeline_v1/` (prepared text), and the manifest files. |
| [`evaluation/`](evaluation/) | Example test questions for each evaluation criterion. |
| [`prompts/`](prompts/) | The first draft of the answer prompt (proposed, not tested). |
| `src/` | Empty for now. The assistant code will go here. |

If you only read four files, read the [project scope](docs/week1_project_scope.md), the [requirements](docs/week2_requirements.md), the [data quality audit](docs/week4_data_quality_audit.md) and the [proposal design](docs/proposal_design.md).

---

## Problem Statement

When students build AI projects, they mostly focus on getting the model to work. Privacy, bias and trust in the output are often left until the end or forgotten.

Guidelines about these topics exist, but they are long, written in general terms and spread across different documents. It is hard for a student to know which parts apply to their own project. ResponsibleAI Guide tries to help by finding the relevant parts and explaining them in simple words, with the source shown.

More detail: [Week 1 project scope](docs/week1_project_scope.md).

## Evidence of the Problem

- A scoping review in *AI & Society* found that responsible AI guidelines often have little effect in practice, partly because the principles are too abstract. ([link](https://link.springer.com/article/10.1007/s00146-024-01880-9))
- Leça et al. (2024) interviewed software practitioners and found that some principles, like transparency and accountability, get less attention than others. ([link](https://arxiv.org/abs/2412.07620))
- A 2023 UNESCO survey found that fewer than 10% of the 450+ schools and universities in it had formal guidance on generative AI. ([link](https://www.unesco.org/en/articles/unesco-governments-must-quickly-regulate-generative-ai-schools))
- Garrett et al. (2020) found that most of the 20 technical AI/ML courses they studied covered ethics only in the last two classes. ([link](https://doi.org/10.1145/3375627.3375868))

The first three sources are about practitioners and institutions. The fourth is about university courses, but in the US.

## Initial Related Work / Existing Approaches

| Existing work | What it does | Limitation |
|---|---|---|
| [NIST AI RMF 1.0](https://doi.org/10.6028/NIST.AI.100-1) | General framework for managing AI risks | Too general. Students must work out what applies to them |
| [Malaysia AIGE (2024)](https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/) | National guidelines with seven AI principles | Voluntary and general. Not interactive |
| [Let's have a chat with the EU AI Act](https://arxiv.org/abs/2505.11946) | RAG chatbot for checking systems against the EU AI Act | Focuses on EU law, not students or Malaysia |
| [gov-rag](https://github.com/rogerramos4/gov-rag) | RAG question answering over NIST AI RMF and EU AI Act | Answers questions only. No project review or checklist |
| [AI Governance & Compliance Assistant](https://github.com/Zaid0205/-AI_Governance_Assistant) | Classifies a described AI use case under EU AI Act risk levels | Mainly EU AI Act. Focused on classification, not learning |

This is a first search only, so there may be other similar tools.

## Engineering Gap

The tools I found mostly focus on EU or US documents and on compliance. ResponsibleAI Guide tries to fill the gap by:

- being made for **students**, with simple explanations
- adding **Malaysian and ASEAN** guidance next to NIST
- taking a **project description** and giving a **checklist**
- being **tested**, including a comparison with a normal LLM without retrieval

## Project Objectives

- **O1:** Collect and organise public responsible AI documents (NIST, Malaysia, ASEAN) for the assistant.
- **O2:** Build a RAG assistant that explains possible risks in a student project, with source references and a simple checklist.
- **O3:** Test retrieval relevance, answer correctness, citation accuracy and handling of questions the documents cannot answer, and compare with a plain LLM.

## Target Users

- University students building AI or machine learning projects (main users)
- Student project teams
- Lecturers who want a starting point to discuss responsible AI

It is not made for professional compliance work.

## Data Sources

| ID | Document | Prepared? |
|---|---|---|
| S01 | [NIST AI Risk Management Framework (AI 100-1)](https://doi.org/10.6028/NIST.AI.100-1) | Yes, 238 text chunks |
| S02 | [NIST Generative AI Profile (AI 600-1)](https://doi.org/10.6028/NIST.AI.600-1) | Not yet |
| S03 | [Malaysia National Guidelines on AI Governance and Ethics](https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/) | Not yet |
| S04 | [ASEAN Guide on AI Governance and Ethics](https://asean.org/wp-content/uploads/2024/02/ASEAN-Guide-on-AI-Governance-and-Ethics_beautified_201223_v2.pdf) | Not yet |
| S05 | [UNESCO Guidance for Generative AI in Education and Research](https://unesdoc.unesco.org/ark:/48223/pf0000386693) (optional) | Not yet |

NIST (S01, S02) and UNESCO (S05) PDFs are in `data/raw/`. The Malaysia AIGE (S03) and ASEAN (S04) PDFs say they may not be reproduced without permission, so they are **link only** in this repo and kept on my own computer (see [`data/data_sources.md`](data/data_sources.md) and [proposal design, section 6](docs/proposal_design.md#6-responsible-data-decision-for-s03-and-s04)). I will confirm this with my lecturer.

---

## Results So Far

**Week 3, first data pipeline** ([details](docs/week3_pipeline_decisions.md))

- Steps: Source PDF → Ingest → Inspect → Prepare → Validate → Record lineage.
- NIST AI 100-1 (48 pages) is split into 238 text chunks of about 500 characters. Each chunk keeps the PDF page number, because the project needs source references (FR3 / RAI1).
- Running the pipeline a second time gave the same fingerprints and mappings.

**Week 4, data quality audit** ([details](docs/week4_data_quality_audit.md))

- 238 of 238 chunks have a valid source reference and were found on the PDF page they point to.
- Problems found: 4 chunks with no real content, a "Page N" footer inside 42 chunks, and about 195 words that look broken by a line-end hyphen (an estimate).
- Only 48 of 387 pages are prepared (about 12%), and there are no chunks yet for Malaysian or ASEAN guidance.
- Decision: **GO WITH CONDITIONS**. Next steps are to clean the pipeline and add the missing documents.

**Proposal design, V2** ([details](docs/proposal_design.md))

- Each V1 part now has a proposed technique, and a new **Embed + Index** step links the data pipeline to the search ([diagram](docs/figures/architecture_v2_proposed.png)).
- Two evaluation criteria were added so that every objective is measured: knowledge base coverage (O1) and checklist relevance (FR4).
- A mistake in the Week 2 traceability matrix (O1 instead of O3 for retrieval relevance) was found and fixed.

## How to Run

**Week 3 pipeline** ([`week3_pipeline.ipynb`](notebooks/week3_pipeline.ipynb), made for Google Colab)

1. Put the five PDFs in a Google Drive folder.
2. Open the notebook in Colab and run the cells from top to bottom (Cell 1 to Cell 9). Cell 2 copies the PDFs from Drive, so change the folder path to your own.
3. Download the outputs from `week3_project/data/` before the session ends, because Colab files are temporary.

**Week 4 audit** ([`week4_data_quality_audit.ipynb`](notebooks/week4_data_quality_audit.ipynb), works in VS Code or Colab)

1. Install `pandas` and `pypdf` if needed.
2. Run the cells from top to bottom (Cell 1 to Cell 7). The notebook reads the prepared data from this repo and does not change it.
3. Cell 7 (Part 3) also opens the S03 and S04 PDFs. They are not in the repo, so download them from the links in [`data/data_sources.md`](data/data_sources.md) into `data/raw/` first.

## Proposed Tools

These are design choices for the proposal, not built yet. The reasons are in [proposal design, section 2](docs/proposal_design.md#2-technique-choices).

| Part | Proposed tool |
|---|---|
| Language | Python (Google Colab and VS Code) |
| PDF text and chunks | `pypdf`, page-aware chunks of 500 characters (done for S01) |
| Embeddings | `bge-small-en-v1.5` (free, runs on a CPU) |
| Vector store | ChromaDB, top 5 chunks per question |
| Language model | Gemini Flash-Lite (Groq as backup) |
| Citations | The model cites chunk IDs, and an output check rejects IDs that were not retrieved |
| Interface | Streamlit, run locally |
| Testing | Python scripts with manually checked test sets and a plain-LLM baseline |

## Limitations

ResponsibleAI Guide is for learning and a first review only. It cannot replace expert advice, it may miss risks, and it does not approve or certify any project.

## References (APA 7th)

- Association of Southeast Asian Nations. (2024). *ASEAN guide on AI governance and ethics*. https://asean.org/wp-content/uploads/2024/02/ASEAN-Guide-on-AI-Governance-and-Ethics_beautified_201223_v2.pdf
- Garrett, N., Beard, N., & Fiesler, C. (2020). More than "if time allows": The role of ethics in AI education. In *Proceedings of the AAAI/ACM Conference on AI, Ethics, and Society* (pp. 272–278). ACM. https://doi.org/10.1145/3375627.3375868
- Kovari, A., Ghafourian, Y., Hegedus, C., Abu Naim, B., Mezei, K., Varga, P., & Tauber, M. (2025). *Let's have a chat with the EU AI Act* (arXiv:2505.11946). arXiv. https://doi.org/10.48550/arXiv.2505.11946
- Leça, M. de M., Bento, M., & de Souza Santos, R. (2024). *Responsible AI in the software industry: A practitioner-centered perspective* (arXiv:2412.07620). arXiv. https://doi.org/10.48550/arXiv.2412.07620
- Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., Küttler, H., Lewis, M., Yih, W., Rocktäschel, T., Riedel, S., & Kiela, D. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. *Advances in Neural Information Processing Systems, 33*, 9459–9474. https://doi.org/10.48550/arXiv.2005.11401
- Miao, F., & Holmes, W. (2023). *Guidance for generative AI in education and research*. UNESCO. https://doi.org/10.54675/EWZM9535
- Ministry of Science, Technology and Innovation. (2024). *The national guidelines on AI governance & ethics*. https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/
- National Institute of Standards and Technology. (2023). *Artificial intelligence risk management framework (AI RMF 1.0)* (NIST AI 100-1). https://doi.org/10.6028/NIST.AI.100-1
- National Institute of Standards and Technology. (2024). *Artificial intelligence risk management framework: Generative artificial intelligence profile* (NIST AI 600-1). https://doi.org/10.6028/NIST.AI.600-1
- rogerramos4. (n.d.). *gov-rag* [Computer software]. GitHub. https://github.com/rogerramos4/gov-rag
- Sadek, M., Kallina, E., Bohné, T., Mougenot, C., Calvo, R. A., & Cave, S. (2025). Challenges of responsible AI in practice: Scoping review and recommended actions. *AI & Society, 40*, 199–215. https://doi.org/10.1007/s00146-024-01880-9
- UNESCO. (2023, September 7). *UNESCO: Governments must quickly regulate generative AI in schools* [Press release]. https://www.unesco.org/en/articles/unesco-governments-must-quickly-regulate-generative-ai-schools
- Xiao, S., Liu, Z., Zhang, P., Muennighoff, N., Lian, D., & Nie, J.-Y. (2023). *C-Pack: Packed resources for general Chinese embeddings* (arXiv:2309.07597). arXiv. https://doi.org/10.48550/arXiv.2309.07597
- Zaid0205. (n.d.). *AI Governance & Compliance Assistant* [Computer software]. GitHub. https://github.com/Zaid0205/-AI_Governance_Assistant
