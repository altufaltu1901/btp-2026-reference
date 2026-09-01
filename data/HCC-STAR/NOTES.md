# HCC-STAR — data

**No dataset released by the authors.**

- **Training:** ~30,000 HCC cases from **SEER** (US NCI cancer registry).
  Public, free: request access at https://seer.cancer.gov/data/access.html →
  use SEER*Stat or the incidence files. Structured variables only (age, tumour
  size, AFP, TNM, survival months) — the authors synthesised the free-text
  narratives from these with GPT-4o + the CNLC 2024 guideline.
- **External test:** 6,668 patients from 12 Chinese hospitals — private, not available.
- **`arXiv-source/`** — the paper's LaTeX bundle: every figure (`figs/`), and
  `baseline_final_external.tex` / `baseline_full_external.tex` with the exact
  reported baseline numbers (BCLC/CNLC/TNM C-index etc.).
