# BTP 2026 — Reference material

Papers, code, and data for the three works the BTP ("Samavet AI" — multimodal HCC
tumour board) builds on. Collected 2026-09-02.

## Layout

```
papers/   the three paper PDFs
code/     source code released with each paper (upstream .git removed; see licenses inside)
data/     released / obtainable datasets + notes on what is private
docs/     our own write-ups (paper explainers, BTP report, project overview)
```

## The three papers

### 1. HCC-STAR — `papers/HCC-STAR_arXiv-2607.08602.pdf`
"Towards Precision Therapy in Hepatocellular Carcinoma: A Clinical-Reasoning LLM for
Risk Stratification and Treatment Guidance", Peng Cui et al., arXiv:2607.08602v1 (2026).
- **Code:** none released.
- **Data:** trained on ~30k SEER HCC cases (SEER is public, free sign-up). External
  test = 6,668 patients / 12 Chinese hospitals — private.
- Have: PDF + full arXiv LaTeX source (`data/HCC-STAR/arXiv-source/`) — all figures,
  plus `baseline_*_external.tex` with the exact reported numbers.

### 2. Nature Cancer AI Agent ("the original") — `papers/NatureCancer-AI-Agent_s43018-025-00991-6.pdf`
"Development and validation of an autonomous artificial intelligence agent for clinical
decision-making in oncology", Ferber, Kather et al., Nature Cancer (2025).
doi:10.1038/s43018-025-00991-6 · open text: https://pmc.ncbi.nlm.nih.gov/articles/PMC12380607/
- **Code:** `code/LLM_RAG_Agent/` — from https://github.com/Dyke-F/LLM_RAG_Agent
- **Data:** radiology image URLs listed in the paper's Supplementary Information;
  histopathology from TCGA (https://www.cancer.gov/tcga); a "source data" file is
  published with the paper. See `data/NatureCancer-AI-Agent/NOTES.md`.

### 3. HemaGuide — `papers/HemaGuide_NatureMedicine_s41591-026-04494-4.pdf`
"Clinical decision support in hematological malignancies using a case-grounded AI agent",
Zoller et al. (senior author M. J. Friedrich, DKFZ/HI-STEM/Heidelberg), Nature Medicine (2026).
doi:10.1038/s41591-026-04494-4
- **Code:** `code/HemaGuide/` — from https://github.com/Friedrich-Lab/HemaGuide
  (full app: FastAPI backend, React frontend, agent pipeline, `prompts/*.yaml`,
  `data/` knowledge base, `build_kb.py`).
- **Data:** KB built from Onkopedia guidelines (public). The >2,000 tumour-board
  cases and the 555 external / 64 prospective validation cases are Heidelberg
  patient data — not released.

## Our own project code
Not in this repo — lives at https://github.com/hmi-iiitd/AITumorBoard

## TODO / still missing
- HemaGuide + Nature Cancer supplementary files & "source data" (need institutional access)
- SEER extraction for a HCC-STAR replication (optional)
