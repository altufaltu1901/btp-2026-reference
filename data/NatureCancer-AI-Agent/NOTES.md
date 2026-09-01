# Nature Cancer AI Agent ("the original") — data & code

## Code — released, multimodal tools STUBBED
https://github.com/Dyke-F/LLM_RAG_Agent → copied to `code/LLM_RAG_Agent/`
Real: LLM-agent orchestration, RAG, ChromaDB retriever, citation checking,
embedding pipeline, `patient_cases.py` (the case vignettes).
**Stubbed:** image segmentation (MedSAM) and genetic-modelling tools are dummy
functions (`agent_tools_dummy.py`) — the real ones need external repos the authors
call hard to set up. README says "under construction".

## Data

### Released (have it — `supplementary/`)
Data-availability statement: radiology image URLs are in the Supplementary
Information; histopathology is from TCGA; source data provided with the paper.
- `S1_Supplementary-Information.pdf` (98 pp) — full supplementary methods, the
  radiology image source URLs, prompts, case details
- `S2_Reporting-Summary.pdf`
- `S3_Supplementary-Files-Description.pdf`
- `S4_Supplementary-Tables-1-7.xlsx` — tool config, agent capability matrix, case grading
- `S5_Figure-Source-Data.xlsx` — **the actual agent outputs per case** (Fig 2B, Fig 4
  correctness/helpfulness/citations/completeness/tool-use, Fig 5B, Extended Data)
- `S6_Case-Vignettes-and-Images.pdf` (37 pp) — case vignettes with the imaging figures

### External (public, referenced)
- Histopathology: TCGA — https://www.cancer.gov/tcga
- Radiology images: download from the URLs listed in S1
- Guideline corpus: Meditron guidelines (`scrape_meditron.py` in the repo)

### Not released
No raw patient EHRs — the cases are curated synthetic vignettes (in the repo +
S6). That's the complete dataset; there is no hidden private cohort for this paper.
