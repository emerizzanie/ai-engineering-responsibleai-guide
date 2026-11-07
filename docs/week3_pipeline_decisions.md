# Pipeline Decisions: ResponsibleAI Guide (Week 3)

This file records what I chose for the V1 data pipeline, why, and what evidence supports it. The pipeline is the data side of the Week 2 architecture: it turns the approved guideline PDFs into text pieces with source details, so the "Knowledge source" and "Knowledge access" parts have something to work with.

Notebook: `notebooks/week3_pipeline.ipynb` | Diagram: `docs/week3_pipeline_diagram.png`

---

## 1. Requirements This Pipeline Supports

| Requirement | What the pipeline must do |
|---|---|
| **FR2** | Turn the approved PDFs into readable text pieces that can be searched later. |
| **FR3 / RAI1** | Keep the document name and PDF page number for every text piece, so answers can show a real source. |
| **NFR5** | Keep a manifest per run so one document can be added or updated and the change can be traced. |

## 2. Selected Source

| Item | Value |
|---|---|
| Source | S01: NIST AI 100-1, AI RMF 1.0 (`nist_ai_100-1_ai_rmf_1.0.pdf`) |
| Size | 48 pages, 106,446 extracted characters, 0 near-empty pages |
| Why S01 | Clean readable text, no near-empty pages, and only 48 pages (the same as S05, and fewer than the others). It is a good first document to test the pipeline on. |
| Not in this run | S02, S04 and S05 use the same pathway and will be added later. S03 (Malaysia AIGE) is link only because of its copyright notice. |

The lab code handles one document per run, so V1 runs S01 only. Adding the other PDFs later means repeating the same steps with a different file name.

## 3. Part B Comparison and Evidence

I compared three ways of splitting the same S01 text into pieces of about 500 characters.

| Approach | Chunks | Page number kept? | What I observed |
|---|---|---|---|
| Fixed-size (lab) | 213 | No | Cuts words in the middle. Repeated header stays inside the text. |
| Paragraph-aware (lab) | 214 | No | Almost the same as fixed-size, because the extracted text has very few blank lines to split on. |
| **Page-aware (adapted, selected)** | **238** | **Yes, every chunk** | Never crosses a page. Header removed. Wrapped lines joined. |

**Why page-aware:** the lab splitter writes `"page": None`, so it cannot support FR3 and RAI1 (source references). Page-aware gives about 12% more chunks but every chunk can be cited.

**Trade-off accepted:** a chunk can go a few characters over 500 so a hyphenated word is not split across two chunks (for example "red-" and "teaming").

## 4. Chosen Preparation Configuration

| Setting | Value |
|---|---|
| Method | Page-aware, line-packed |
| Chunk size | 500 characters |
| Repeated header | Removed when the same first line appears on 10 or more pages (S01: "NIST AI 100-1 AI RMF 1.0", found on 43 of 48 pages) |
| Line-end hyphens | Kept. The next line is joined without a space, because some hyphens are real ("red-teaming"). S01 has 235 line-end hyphens. |
| Page number | PDF page number (page 1 = first page of the file), not the printed number |
| Record fields | `record_id`, `text`, `source`, `source_sha256`, `processing`, `page` |

## 5. Accepted Limitations

- **Page only, no section title.** Citations can say "page 23" but not the section name yet. Section information may be added later.
- **PDF page vs printed page.** The PDF page number can differ from the number printed on the page. The answer should say "PDF page".
- **Header rule is simple.** It only removes a first line that repeats on 10+ pages. Pages without that header are left as they are.
- **One document only** in this run (see section 2).
- **Not a full quality check.** The checks here only show that files exist, are not empty, and match their fingerprints. Deeper quality checks belong to Topic 4.

## 6. Pipeline Design Choices

| Question | Decision | Reason |
|---|---|---|
| ETL or ELT? | **ETL**: clean and chunk first, then save the prepared file. | The prepared JSONL is what later steps read. There is no database yet to transform inside. |
| Batch or streaming? | **Batch** | The guideline PDFs rarely change and there are only a few of them. Streaming adds complexity with no benefit. |
| Output format | JSONL, one chunk per line | Easy to read in Python, easy to load into a search index later. |

## 7. Output Locations

| Output | Location |
|---|---|
| Part B prepared text | `data/processed/prepared_text.jsonl` |
| Part B lineage manifest | `data/week3_manifest.json` |
| Part C prepared text | `data/pipeline_v1/documents/prepared_text.jsonl` |
| Part C run manifest | `data/pipeline_v1/documents/pipeline_manifest.json` |

Part C uses the same configuration as Part B, so the two prepared files should be identical. The last cell of the notebook compares them (record count, text and page numbers).

## 8. Stage Contracts

| Stage | Contract / evidence | Failure response (in the notebook) |
|---|---|---|
| **Ingest** | The chosen PDF exists in `data/raw` (`resolve_source`). | Stops with `FileNotFoundError`. |
| **Inspect** | The PDF has extractable text. Pages, characters, near-empty pages and the repeated header are recorded. | Stops with "No extractable text found. Scanned PDFs may require OCR." |
| **Prepare** | Chunks are produced, none are empty, and every chunk has a page number. | Stops if no chunks, an empty chunk, or a missing page. |
| **Validate** | The output file exists, is not empty, and its SHA-256 is saved. | Stops with "Prepared output missing / empty". |
| **Record lineage** | The source file is linked to the exact output file in `pipeline_manifest.json` and `week3_manifest.json`. | Stops if the source or output is missing, or the processing note is empty. |

## 9. Repeatability Check (Cell 9)

Cell 9 runs the pipeline a second time and compares the source fingerprints, output fingerprints and source-to-output mapping. Timestamps are allowed to differ.

Result: fill in after running Cell 9 in Colab (expected: `PASS: documents` and "All selected pipelines passed repeatability smoke checks.").

This is a smoke test. It does not prove full reproducibility on another computer.

## 10. Provenance and Access Caveats

- `source_version` and `access_rights` are recorded as **"Not verified"** in both manifests.
- NIST says the document is free of charge, but the reuse terms are not checked yet. I will check them before committing any PDF.
- Raw PDFs are not committed. Only the prepared text and the manifests are, and I will check the reuse terms before committing these too.
- S03 stays link only.

## 11. Traceability

| Requirement | Pipeline evidence |
|---|---|
| FR2 | S01 turned into 238 text chunks (`prepared_text.jsonl`) |
| FR3 / RAI1 | Every chunk has `source` and `page`; page-aware chosen over the lab splitter |
| NFR5 | One manifest per run with source and output fingerprints |
