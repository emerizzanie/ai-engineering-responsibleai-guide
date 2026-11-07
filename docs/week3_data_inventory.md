# Data Inventory: ResponsibleAI Guide (Week 3)

This is real project data, not the synthetic practice dataset. All sources are public PDF documents about responsible AI. The data type is **unstructured text (PDF)**, so I will use **Pathway A (PDF/TXT)**.

The numbers below come from my Colab inspection cell (Cell A3) using `pypdf`. A page counts as "near-empty" if it has fewer than 50 characters of extracted text.

---

## A1: From Requirements to Data Needs

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
|---|---|---|---|
| **FR2**: Answer questions about what the approved documents say | Readable text of the guideline content, split into pieces that can be searched later | The 5 PDFs below (NIST, Malaysia, ASEAN, UNESCO) | Official publishers and public documents, so they fit the project scope. They are general guidance, so they do not cover every responsible AI topic. |
| **FR3 / RAI1**: Give a real source reference for each answer | Document name, publisher, year, link, and page or section for each piece of text | Same PDFs, plus the details in `data/data_sources.md` | Needed for later citation checking. A basic chunking method may lose page numbers, so this must be handled or listed as a limitation. |
| **NFR5**: Add a new document without rebuilding unrelated parts | A clear file naming rule, an inventory and a manifest for each document | Same PDFs and the Week 3 manifest | Makes it possible to add or update one document and trace what changed. |

---

## A4: Data Inventory

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
|---|---|---|---|---|---|
| S01 | `nist_ai_100-1_ai_rmf_1.0.pdf` / https://doi.org/10.6028/NIST.AI.100-1 | PDF text. General AI risk management guidance | NIST AI 100-1, January 2023, owner: NIST | Cover says it is available free of charge. Reuse terms still to check before committing the PDF | 48 pages, 106,446 characters, 0 near-empty pages. Text reads cleanly. The header "NIST AI 100-1 AI RMF 1.0" repeats at the top of pages, and a word split by a line-end hyphen was seen ("illus-" / "trated"). |
| S02 | `nist_ai_600-1_genai_profile.pdf` / https://doi.org/10.6028/NIST.AI.600-1 | PDF text. Extra risks for generative AI | NIST AI 600-1, 2024, owner: NIST | Cover says it is available free of charge. Reuse terms still to check before committing the PDF | 64 pages, 160,494 characters, 0 near-empty pages. The cover page has many blank spaces and newlines. A ligature character was seen in "aﬀected". Action IDs such as MS-1.1-006 appear in the text. |
| S03 | `malaysia_aige_2024.pdf` / https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/ | PDF text. Malaysian national guidance | AIGE, 2024, owner: MOSTI | The PDF has a publisher copyright notice ("All rights reserved..."). **Link only, do not commit the PDF** unless permission is clear | 140 pages, 258,335 extracted characters and 5 near-empty pages. Pages 3, 10 and 140 contain no extracted text, while pages 40 and 114 contain only page numbers. The document has substantial readable text, including sections intended for different audiences, such as policymakers. |
| S04 | `asean_ai_governance_ethics_2024.pdf` / https://asean.org/wp-content/uploads/2024/02/ASEAN-Guide-on-AI-Governance-and-Ethics_beautified_201223_v2.pdf | PDF text. Regional guidance and risk assessment template | ASEAN Guide, 2024, owner: ASEAN | Public download. Reuse terms still to check before committing the PDF | 87 pages, 191,311 extracted characters and 9 near-empty pages. The identified pages are 1, 6, 10, 17, 46, 53, 58, 60 and 73. These pages contain short headings or section titles, such as Introduction, Guiding Principles, Conclusion and Annex titles. The extracted text also includes case studies, such as the GOJEK example. |
| S05 | `unesco_genai_guidance_2023.pdf` / https://unesdoc.unesco.org/ark:/48223/pf0000386693 | PDF text. Optional background on generative AI in education | UNESCO, 2023, owner: UNESCO | Check the licence on the UNESCO page before committing the PDF | 48 pages, 168,712 characters, 0 near-empty pages. The first text page is a UNESCO Education 2030 boilerplate page, not the title page. A page number appears at the top of pages, and list bullets come out as "●". |

**Total:** 5 documents, 387 pages, about 885,000 extracted characters.

---

## Storage: Raw vs Prepared

- **Raw PDFs:** kept in Google Drive and on my own computer, and copied into Colab `data/raw` (temporary) each session. I will only commit a PDF to GitHub if its licence allows it. For S03 I will not commit the PDF.
- **Prepared data:** saved under `data/processed/` and `data/pipeline_v1/documents/`, with the manifest in `data/week3_manifest.json`.

## Observations and Limitations

- **Text extraction:** All five PDFs produced substantial extracted text using `pypdf`. The samples show that the main document content is readable. OCR is not currently required for the extracted text used in this initial pipeline.

- **Near-empty pages:** S04 (ASEAN) has 9 pages with fewer than 50 extracted characters, while S03 (Malaysia AIGE) has 5. Most identified ASEAN pages contain short headings or section titles. For Malaysia, some pages contain no text, while others contain only page numbers. These pages should be checked visually if their content is needed later.

- **Formatting issues:** Repeated headers, page numbers, line-end hyphens and ligature characters appear in some extracted text. These may affect text search and retrieval if they are not handled during preparation.
  - S01 (NIST AI RMF 1.0): the header "NIST AI 100-1 AI RMF 1.0" appears at the top of 43 of 48 pages, and there are 235 line-end hyphens and 0 ligatures.
  - S02 (NIST AI 600-1): 402 ligature characters were found (for example "ﬀ" in "aﬀected"). They are not handled in V1 because S02 is not in this run.

- **Page references:** The basic chunking code in the lab uses `"page": None`, which does not preserve the original PDF page number. This is a limitation because FR3 and RAI1 require source references. The preparation process should preserve document identity and page information for future citations.

- **Document structure:** S03 (Malaysia AIGE) contains different sections for different audiences, including policymakers. Section information should be preserved during text preparation to avoid mixing guidance intended for different audiences.

- **Main document selection:** S01 (NIST AI RMF 1.0) is selected as the proposed main document for the initial pipeline because it has readable extracted text, no near-empty pages and fewer pages than the other selected documents. This decision will be reviewed during Part B. Part B confirmed it, and it is the source used in the Part C pipeline.
