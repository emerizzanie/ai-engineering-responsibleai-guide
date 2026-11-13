# Week 3: Data Inventory (ResponsibleAI Guide)

[Back to README](../README.md) | Same week: [pipeline decisions](week3_pipeline_decisions.md) | Next: [Week 4 audit](week4_data_quality_audit.md)

This is real project data, not the synthetic practice dataset. All sources are public PDF documents about responsible AI. The data type is **unstructured text (PDF)**, so I will use **Pathway A (PDF/TXT)**.

The numbers below come from my Colab inspection cell (Cell A3) using `pypdf`. A page counts as "near-empty" if it has fewer than 50 characters of extracted text.

---

## 1. From Requirements to Data Needs (lab step A1)

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
|---|---|---|---|
| **FR2**: Answer questions about what the approved documents say | Readable text of the guideline content, split into pieces that can be searched later | The 5 PDFs below (NIST, Malaysia, ASEAN, UNESCO) | Official publishers and public documents, so they fit the project scope. They are general guidance, so they do not cover every responsible AI topic. |
| **FR3 / RAI1**: Give a real source reference for each answer | Document name, publisher, year, link, and page or section for each piece of text | Same PDFs, plus the details in `data/data_sources.md` | Needed for later citation checking. A basic chunking method may lose page numbers, so this must be handled or listed as a limitation. |
| **NFR5**: Add a new document without rebuilding unrelated parts | A clear file naming rule, an inventory and a manifest for each document | Same PDFs and the Week 3 manifest | Makes it possible to add or update one document and trace what changed. |

---

## 2. Data Inventory (lab step A4)

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
|---|---|---|---|---|---|
| S01 | `nist_ai_100-1_ai_rmf_1.0.pdf` / https://doi.org/10.6028/NIST.AI.100-1 | PDF text. General AI risk management guidance | NIST AI 100-1, January 2023, owner: NIST | The PDF says it is available free of charge. NIST's website says works by its staff are not under copyright in the United States, but foreign rights are reserved. | 48 pages, 106,446 characters, 0 near-empty pages. Text reads cleanly. The header "NIST AI 100-1 AI RMF 1.0" repeats at the top of pages, and a word split by a line-end hyphen was seen ("illus-" / "trated"). |
| S02 | `nist_ai_600-1_genai_profile.pdf` / https://doi.org/10.6028/NIST.AI.600-1 | PDF text. Extra risks for generative AI | NIST AI 600-1, 2024, owner: NIST | The PDF says it is available free of charge. NIST's website says works by its staff are not under copyright in the United States, but foreign rights are reserved. | 64 pages, 160,494 characters, 0 near-empty pages. The cover page has many blank spaces and newlines. A ligature character was seen in "aﬀected". Action IDs such as MS-1.1-006 appear in the text. |
| S03 | `malaysia_aige_2024.pdf` / https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/ | PDF text. Malaysian national guidance | AIGE, 2024, owner: MOSTI | The PDF says "All rights reserved" and no copying without prior permission. Permission is an open issue. | 140 pages, 258,335 extracted characters and 5 near-empty pages. Pages 3, 10 and 140 contain no extracted text, while pages 40 and 114 contain only page numbers. The document has substantial readable text, including sections intended for different audiences, such as policymakers. |
| S04 | `asean_ai_governance_ethics_2024.pdf` / https://asean.org/wp-content/uploads/2024/02/ASEAN-Guide-on-AI-Governance-and-Ethics_beautified_201223_v2.pdf | PDF text. Regional guidance and risk assessment template | ASEAN Guide, 2024, owner: ASEAN | The PDF says it is protected by copyright and may not be reproduced without written permission. Permission is an open issue. | 87 pages, 191,311 extracted characters and 9 near-empty pages. The identified pages are 1, 6, 10, 17, 46, 53, 58, 60 and 73. These pages contain short headings or section titles, such as Introduction, Guiding Principles, Conclusion and Annex titles. The extracted text also includes case studies, such as the GOJEK example. |
| S05 | `unesco_genai_guidance_2023.pdf` / https://unesdoc.unesco.org/ark:/48223/pf0000386693 | PDF text. Optional background on generative AI in education | UNESCO, 2023, owner: UNESCO | Open Access under the CC-BY-SA 3.0 IGO licence (stated in the PDF). | 48 pages, 168,712 characters, 0 near-empty pages. The first text page is a UNESCO Education 2030 boilerplate page, not the title page. A page number appears at the top of pages, and list bullets come out as "●". |

**Total:** 5 documents, 387 pages, about 885,000 extracted characters.

> The "Access / rights" column was updated in Week 4, after I checked the rights statement inside each PDF. Details are in [section E of the Week 4 audit](week4_data_quality_audit.md#e-responsible-data-practices).

---

## 3. Storage: Raw vs Prepared

- **Raw PDFs:** kept in `data/raw/` in this repo, with copies in Google Drive and on my own computer. The permission for S03 and S04 is an open issue (see the table above).
- **Prepared data:** saved under `data/processed/` and `data/pipeline_v1/documents/`, with the manifest in [`data/week3_manifest.json`](../data/week3_manifest.json).

## 4. Observations and Limitations

- **Text extraction:** All five PDFs gave readable text with `pypdf`, so OCR (reading text from page images) is not needed for now.
- **Near-empty pages:** S04 (ASEAN) has 9 and S03 (Malaysia AIGE) has 5. The ASEAN ones are mostly short headings or section titles. For Malaysia, some pages have no text and others have only a page number. They should be checked by eye if their content is needed later.
- **Formatting issues:** Repeated headers, page numbers, line-end hyphens and ligature characters appear in some of the extracted text. They may affect the search later if they are not handled.
  - S01 (NIST AI RMF 1.0): the header "NIST AI 100-1 AI RMF 1.0" appears at the top of 43 of 48 pages, and there are 235 line-end hyphens and 0 ligatures.
  - S02 (NIST AI 600-1): 402 ligature characters were found (for example "ﬀ" in "aﬀected"). They are not handled in V1 because S02 is not in this run.
- **Page references:** The lab's basic chunking code writes `"page": None`, so the PDF page number is lost. FR3 and RAI1 need source references, so the preparation step must keep the document name and page.
- **Document structure:** S03 (Malaysia AIGE) has different sections for different audiences, including policymakers. Section information should be kept, so guidance for different audiences is not mixed.
- **Main document:** S01 (NIST AI RMF 1.0) is the main document for the first pipeline. It has readable text, no near-empty pages and only 48 pages (the same as S05 and fewer than the others). Part B confirmed this choice, and S01 is the source used in the Part C pipeline.
