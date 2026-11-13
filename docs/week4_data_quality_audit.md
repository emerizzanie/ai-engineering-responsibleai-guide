# Week 4: Data Quality Audit (ResponsibleAI Guide)

[Back to README](../README.md) | Previous: [Week 3 pipeline decisions](week3_pipeline_decisions.md)

SAIA3113 AI Engineering, Topic 4. Individual data quality audit and responsible data assessment.

- **Evidence:** [`week4_data_quality_audit.ipynb`](../notebooks/week4_data_quality_audit.ipynb) (saved outputs) and [`week4_audit/`](../notebooks/week4_audit/). Extra tables are in the appendix.
- **Data audited:** [`prepared_text.jsonl`](../data/processed/prepared_text.jsonl), the 238 text chunks made in Week 3. A chunk is a piece of text of about 500 characters with its source file and page number.
- **Reading the numbers:** "sample" means the 19 chunks I inspected. "All" means all 238 chunks.

---

## A. Project Context

| Item | Details |
|---|---|
| **Project title** | ResponsibleAI Guide: A RAG-Based Assistant for Responsible AI Risk Identification in Student Projects |
| **Purpose** | A student describes their AI project. The system searches approved public responsible AI guidelines, explains possible risks, shows the source and gives a simple checklist. For learning and a first review only. |
| **Two requirements** | **FR2:** answer questions about what the approved guideline documents say. **FR3 (with RAI1):** give a real source reference for each risk or answer. ([Requirements](week2_requirements.md)) |
| **Data sources and types** | Five public PDF guidelines (unstructured text). Only S01 (NIST AI 100-1) is prepared, as 238 chunks. S02 to S05 are inspected but not prepared. ([Inventory](week3_data_inventory.md)) |
| **Sample inspected** | 19 of 238 chunks (about 8%), all from S01: 12 normal chunks spread through the document and 7 unusual ones. |

**Why this data is needed.** For FR2, the assistant can only answer from the text of the approved documents, and the chunks are what it will search. For FR3 / RAI1, each chunk keeps the document name and PDF page number, so an answer can show a source the student can check.

**If data is missing.** A missing document affects FR2, because the assistant cannot answer about that guidance and should say so (FR5 / RAI3). A missing page number or source name affects FR3 and RAI1, because the answer cannot be cited.

## B. Data Quality Assessment

Evidence: notebook Cells 3 and 4.

| Dimension | Sample (19 chunks) | All 238 chunks | Impact |
|---|---|---|---|
| **Completeness** | No empty field, but 2 chunks contain only "Page 7" and "Page 9". | 0 empty fields. 3 chunks under 50 characters. All 48 PDF pages have a chunk. | A chunk with no real content can still be returned by the search. |
| **Consistency** | Same source name, fingerprint and processing label. The fingerprint matches the manifest. | Same. But the Part B and Part C files use a different `processing` label, and the character count is 106,446 in my inventory and 106,493 in the manifest. | Small. The 47-character gap is line breaks between pages (Cell 7). Different labels make the data harder to trace. |
| **Accuracy** | All 19 found on the PDF page they point to. 4 have a "Page N" footer in the text. About 14 of 28 hyphenated words look broken ("sys-tem"). | 238 of 238 found on their page. 42 have a footer. About 195 of 406 hyphenated words look broken. | A search for "system" may miss "sys-tem". The footer shows the printed page ("Page 7") but the chunk stores PDF page 12, which can confuse a citation. |
| **Timeliness** | S01 is from January 2023. The NIST page (checked 13 November 2026) says AI RMF 1.0 is being updated and a revision is in progress. | Same document. | Usable for now. S01 must be prepared again when the revision is released. |
| **Uniqueness** | No repeated text or record ID. The cover title in chunk 0001 appears again inside chunk 0002. | 0 exact duplicates, 0 repeated IDs. 3 cases of a short chunk repeated inside another. | Low. These are cover lines, but they could take a place in the search results. |

The broken-word count is an estimate, and accuracy was checked against the text that `pypdf` extracts. Only S01 could be checked. More limits are listed in the appendix.

## C. Two Validation Rules

Evidence: notebook Cell 5.

| | Rule 1: valid source reference | Rule 2: real content |
|---|---|---|
| **Problem it detects** | A chunk that cannot be traced to its document, so no source can be shown (FR3 / RAI1). | A chunk with text and a page number but nothing useful inside, such as only a footer. |
| **Condition checked** | `source` is not empty, `source_sha256` matches the manifest, and `page` is a whole number from 1 to 48. | After removing any "Page N" footer, at least 50 characters are left. |
| **Result** | Sample: 19 pass, 0 fail. All: 238 pass, 0 fail. | Sample: 17 pass, 2 fail. All: 234 pass, 4 fail. |
| **Action if it fails** | Keep the chunk out of the search (quarantine) and prepare it again from the PDF. | Put it on a [review list](../notebooks/week4_audit/week4_review_list.csv), check it by hand, then fix the cause in the pipeline. |

Rule 2 found something my Week 3 check missed, because Week 3 only checked that a chunk "has text", and "Page 7" counts as text.

**Why human review and not automatic deletion?**

- **A rule cannot tell rubbish from real text.** Three of the four failed chunks are only footers or table headings. The fourth is "in particular. Page 42", which is the end of a real sentence. Deleting it automatically would cut that sentence.
- **A failed rule points to a cause.** My pipeline removes the page header but not the footer. Deleting the four chunks would hide this, and the footer would still be in 42 other chunks.
- **The rule itself can be wrong.** I chose 50 characters as the limit, and a short heading or definition could fail it too.

## D. Coverage and Potential Bias

Evidence: notebook Cell 6.

| User need | Example question | Should be covered by | Prepared now? |
|---|---|---|---|
| **1. General AI risks** | "What privacy and bias risks should I think about?" | S01 NIST AI 100-1 | Yes, 238 chunks |
| **2. Malaysian and ASEAN guidance** | "What do the Malaysian AI guidelines say about transparency?" | S03 Malaysia AIGE, S04 ASEAN Guide | No, 0 chunks |

**Coverage findings.** Only 48 of the 387 pages in my five documents are prepared (about 12%), all from one document. Need 1 has data to start with: privacy, bias, transparency, safety, security and accountability each appear in 17 to 26 chunks. Need 2 has no data: "Malaysia" and "ASEAN" appear in 0 chunks. This matters because adding Malaysian and ASEAN guidance is part of the gap my project wants to fill. This is a keyword count, so it shows a topic is present, not how well it is explained.

**Potential bias: representation bias.** All prepared data comes from one publisher (NIST) in one country (the United States). The Malaysian and ASEAN views are in my source list but not in the prepared data.

**How it could affect the output.** A local question would get "not enough information" (FR5), which is honest but not helpful. Or the search returns NIST chunks on the same topic, and the answer sounds complete even though it is the US view. Every checklist would cite only NIST. So the application would perform better for general AI risk questions than for Malaysian or ASEAN questions, only because of which document was prepared first.

**Can I confirm it now?** The data gap is confirmed. The effect on answers is not, because the assistant is not built yet. To investigate, I will prepare S03 and S04, label each question in my 30-question test set as "general" or "local", and compare the retrieval hit rate and correct answer rate of the two groups.

## E. Responsible Data Practices

Evidence: notebook Cell 7 and the two manifests.

**1. Source and tracing.** All five documents are public guidelines from official publishers ([links](../data/data_sources.md)). Each chunk stores the source file name, the SHA-256 fingerprint of the PDF and the PDF page number, and the fingerprints matched the manifest in this audit. Still missing: the manifest says "Not verified" for version and access rights, and I did not record the download dates.

**2. Personal or sensitive information.** I did not find private personal data. A simple search of the 238 chunks found one public NIST contact email and no phone numbers. The search only looks for emails and phone numbers, so it is a basic check. The bigger privacy risk comes later, when students type their own project descriptions (RAI4).

**3. Permission.** NIST is free of charge and UNESCO is Open Access. The Malaysia AIGE (S03) and ASEAN (S04) PDFs both say they may not be reproduced without permission. All five PDFs are in `data/raw/` of my public repo, and I do not have permission for S03 and S04, so this is an open issue. I am not a legal expert.

**4. Safeguards and actions.** Already in place: SHA-256 fingerprints, the Week 3 repeatability check (passed), and the two validation rules with a review list. Necessary actions:

1. Ask my lecturer if keeping the S03 and S04 PDFs in the repo is acceptable. If not, remove them and keep links only.
2. Until that is settled, do not commit prepared chunks from S03 or S04.
3. Replace "Not verified" in the manifest with the real version, access date and rights.

**Data governance statement.** My project uses public guideline documents, and every prepared chunk can be traced to its source file, fingerprint and page. I did not find private personal data in the chunks. The main gap is permission: S03 and S04 do not allow copying without permission, and copies are in my public repo. I will confirm this with my lecturer, remove the files if needed, and record the version, access date and rights of every source before more documents are prepared.

## F. Engineering Decision

**Decision: GO WITH CONDITIONS** (not GO, not HOLD)

**Why not HOLD.** The prepared data is traceable and matches its source in the checks I ran. 238 of 238 chunks pass Rule 1 and were found on the PDF page they point to. There are no empty fields, no exact duplicates and no repeated IDs, and no newer version of NIST AI 100-1 has been released. This is enough to start building and testing the search on S01.

**Why not a full GO.**

- **FR2:** only 1 of 5 documents is prepared (about 12% of pages), with 0 chunks for Malaysian or ASEAN guidance. About 195 hyphenated words look broken (estimate).
- **FR3 / RAI1:** 42 chunks contain a "Page N" footer, and 4 chunks failed Rule 2.
- **Responsible data:** permission for S03 and S04 is not settled, and the manifest says "Not verified".

These are cleaning, coverage and permission problems that can be fixed. I will continue with S01 only and treat the assistant as limited to general AI risk questions until the conditions below are met.

| # | Priority improvement | When | Done when |
|---|---|---|---|
| 1 | **Clean the pipeline and run it again.** Remove the page footer like the header, join words split at a line end, and join a short leftover piece to the chunk before it. | Before the next stage | Rule 2 has 0 fails, no chunk has a "Page N" footer, and Rule 1 still passes for every chunk. |
| 2 | **Add the missing documents.** Settle the permission for S03 and S04, then prepare S02 and the Malaysian and ASEAN guidance with the same pipeline, and fill in the manifest. | During the next stage | Need 2 has prepared chunks that pass both rules, and the manifest has no "Not verified". |

---

## Appendix: Evidence and Extra Notes

**Where each result comes from**

| Section | Evidence |
|---|---|
| A. Sample | Cell 2 and [`week4_audit_sample.csv`](../notebooks/week4_audit/week4_audit_sample.csv) |
| B. Quality dimensions | Cell 3 (sample and all), Cell 4 (page check against the PDF), Cell 7 (character count) |
| B. Timeliness | [NIST AI Resource Center, AI RMF 1.0 page](https://airc.nist.gov/AI_RMF_Knowledge_Base/AI_RMF/Foundational_Information/0-ai-rmf-1-0) |
| C. Validation rules | Cell 5 and [`week4_review_list.csv`](../notebooks/week4_audit/week4_review_list.csv) |
| D. Coverage and bias | Cell 6, [`week2_evaluation_criteria.md`](week2_evaluation_criteria.md) for the test set |
| E. Responsible data | Cell 7, [`week3_manifest.json`](../data/week3_manifest.json), and the [NIST copyright statement](https://www.nist.gov/open/copyright-fair-use-and-licensing-statements-srd-data-software-and-technical-series-publications) |

**How the sample was chosen (section A).** The notebook picks it by fixed rules, so it is the same on every run. The 12 normal chunks are every 20th chunk, from page 5 to page 46. The 7 unusual chunks are the two shortest, the longest, the first two, the last, and the one with the most hyphenated words.

**Limits of the quality assessment (section B)**

- A hyphenated word counts as "likely broken" if the same word without the hyphen also appears in the document. My Week 3 files say 235 line-end hyphens. That is a different count, made on the raw PDF text.
- Accuracy was checked against the text that `pypdf` extracts, not against figures or tables.
- Timeliness and chunk quality were checked for S01 only, because it is the only prepared document. The manifest still says `source_version: Not verified`.

**The four chunks that failed Rule 2 (section C)**

| Chunk | PDF page | Text | After looking by hand |
|---|---|---|---|
| 0052 | 12 | "Page 7" | Only a footer. Can be removed. |
| 0066 | 14 | "Page 9" | Only a footer. Can be removed. |
| 0151 | 31 | "Categories Subcategories Continued on next page Page 26" | Table heading and footer. Can be removed. |
| 0237 | 47 | "in particular. Page 42" | End of a real sentence. Join it to the chunk before it. |

Rule 1 only checks that the page is a valid number. Whether the text is really on that page was checked separately in Cell 4.

**Chunks that mention each topic, of 238 (section D)**

| Need 1 | Chunks | Need 2 and extra | Chunks |
|---|---|---|---|
| privacy | 18 | Malaysia | 0 |
| bias | 21 | ASEAN | 0 |
| fair | 11 | generative | 2 |
| transparen | 26 | student | 0 |
| safe | 18 | education | 0 |
| secur | 20 | | |
| accountab | 17 | | |

"generative" appears in only 2 chunks, so S02 will also be needed for student projects that use generative AI.

**A smaller second bias risk (section D).** "trustworthiness" appears 36 times correctly and 7 times as a broken word, while "privacy" is never broken. Long words that land at a line end are a little harder to find. This is pipeline-induced bias, because it comes from my preparation step and not from the NIST document. To check the main bias later, I will also count which document each citation comes from.

**Rights statement inside each PDF (section E)**

| ID | PDF page | What it says |
|---|---|---|
| S01, S02 (NIST) | 2 | "Available free of charge". NIST's website says works by its staff are not under copyright in the United States but foreign rights are reserved, and it asks for a citation. I am not sure how the foreign rights apply to me. |
| S03 (Malaysia AIGE) | 2 | "All rights reserved." No reproduction without prior permission. |
| S04 (ASEAN Guide) | 87 | Protected by copyright. No reproduction without written permission. |
| S05 (UNESCO) | 2 | Open Access under CC-BY-SA 3.0 IGO |

The cover page of S01 also names two US officials. One more action from section E: check the NIST version again before the final evaluation.
