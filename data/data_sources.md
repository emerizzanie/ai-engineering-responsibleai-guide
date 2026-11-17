# Data Sources (ResponsibleAI Guide)

[Back to README](../README.md)

These are the public documents for the ResponsibleAI Guide knowledge base.

| ID | Document | Publisher | Year | Link | Rights (from the PDF) | Copy in this repo? |
|---|---|---|---|---|---|---|
| S01 | NIST AI Risk Management Framework (AI RMF 1.0), NIST AI 100-1 | NIST | 2023 | https://doi.org/10.6028/NIST.AI.100-1 | Free of charge | Yes: `raw/nist_ai_100-1_ai_rmf_1.0.pdf` |
| S02 | NIST Generative AI Profile, NIST AI 600-1 | NIST | 2024 | https://doi.org/10.6028/NIST.AI.600-1 | Free of charge | Yes: `raw/nist_ai_600-1_genai_profile.pdf` |
| S03 | National Guidelines on AI Governance and Ethics (AIGE) | MOSTI, Malaysia | 2024 | https://mastic.mosti.gov.my/publication/the-national-guidelines-on-ai-governance-ethics/ | All rights reserved, no reproduction without permission | **No, link only** |
| S04 | ASEAN Guide on AI Governance and Ethics | ASEAN | 2024 | https://asean.org/wp-content/uploads/2024/02/ASEAN-Guide-on-AI-Governance-and-Ethics_beautified_201223_v2.pdf | Copyright, no reproduction without written permission | **No, link only** |
| S05 | Guidance for Generative AI in Education and Research | UNESCO | 2023 | https://unesdoc.unesco.org/ark:/48223/pf0000386693 | Open Access (CC-BY-SA 3.0 IGO) | Yes: `raw/unesco_genai_guidance_2023.pdf` |

## Notes

- S01 to S04 are the main sources. S05 is optional.
- Only S01 is prepared into text chunks so far. See the [Week 3 data inventory](../docs/week3_data_inventory.md).
- **Rights:** I checked the rights statement inside each PDF in Week 4 ([section E of the audit](../docs/week4_data_quality_audit.md#e-responsible-data-practices)). NIST's website also says works by its staff are not under copyright in the United States, but foreign rights are reserved.
- **S03 and S04 (update, 17 Nov 2026):** both are free to download but not openly licensed. So they are no longer kept in this public repo. They are listed in [`.gitignore`](../.gitignore) and kept on my laptop and Google Drive, and their chunks will only be made locally. I will confirm this choice with my lecturer. Details: [proposal design, section 6](../docs/proposal_design.md#6-responsible-data-decision-for-s03-and-s04).
- **To run the notebooks with S03 and S04:** download them from the links above and save them in `data/raw/` with the file names `malaysia_aige_2024.pdf` and `asean_ai_governance_ethics_2024.pdf`. Git will ignore them.
- **Still to do:** record the version and the date I accessed each document in the manifest (planned for the next pipeline run).
