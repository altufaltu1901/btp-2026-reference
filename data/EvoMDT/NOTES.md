# EvoMDT — code & data situation

Paper: `../../papers/related/EvoMDT_npj-Digital-Medicine-2026_s41746-025-02304-8.pdf`
npj Digital Medicine 9:124 (2026), doi:10.1038/s41746-025-02304-8. Received 2025-10-20,
accepted 2025-12-18. Corresponding: Xianfu Sun, Zhitao Ying, Guangliang Qiang.

## What it is
Self-evolving multi-agent MDT system. Domain agents (Diagnostic — Bayesian
P(D|S); plus treatment/other agents) reason over **lesion-level** clinical data with
structured guideline RAG (semantic weight 0.8 + keyword 0.2). A conflict-detection +
hierarchical consensus protocol (S_conflict = alpha*D_semantic + beta*R_risk +
gamma*I_implementation; safety > efficacy) yields traceable, evidence-linked recs.
The **self-evolution loop** updates prompts, consensus weights, and retrieval scope
from expert feedback + outcome signals while keeping an audit log.

Evaluation: 6 public oncology QA benchmarks (incl. MedQA) + 4 real-world lesion-level
cohorts — **breast, HCC, lung adenocarcinoma, lymphoma** — then single-blind physician
rating. Metrics: ROUGE-1/L, BERTScore, cosine similarity of treatment plans,
automated safety/guideline-concordance checks, 6-dimension Likert clinician scores.

## Code — NOT available yet
"Code availability: Core modules and prompts of EvoMDT are available at GitHub:
https://github.com/KesselZ/EvoMDT" — but that URL returns **404 as of 2026-09-02**
(user KesselZ exists; the repo is not published). Nothing to clone. Re-check later.

## Data — private
"The data supporting this study's findings are not publicly available due to privacy
and confidentiality restrictions ... available from the corresponding author upon
reasonable request." The 4 real-world cohorts (incl. HCC lesion-level) are not released.
The KB guideline catalog IS listed (Supplementary Tables 1-2) but guidelines
themselves are used for extraction only, "not for redistribution".

## What we have (`supplementary/`)
- `S1_Supplementary-Information.pdf` (16 pp) — the only supplementary file on the
  article page. Contents:
  - **Supp. Table 1** — international clinical guidelines used to build the KB
    (HCC: AASLD, ACR LI-RADS, BCLC, EASL, ESMO 2025, NCCN Hepatobiliary 2025;
    plus NSCLC, breast, lymphoma catalogs).
  - **Supp. Table 2** — China-local guideline catalog.
  - **Supp. Figs 1-4** — lesion-level design-cohort descriptors (age/stage violin
    plots, gender bars, lesion-diameter ridgelines, UpSet plots of etiology/clinical
    context, stage/treatment Marimekko) for lung adeno, breast, **HCC (Fig 3: BCLC
    stages, viral etiology, tumor diameter)**, lymphoma.

## Anything else the user could get that scripts can't
Only S1 is posted; there is no separate tables xlsx or peer-review file on the npj
page for this article. Nothing further to fetch. The single outstanding item is the
**GitHub repo once KesselZ publishes it** — worth a periodic check.

## Why it matters for the BTP
- Architecturally the closest published system to Samavet (agents + guideline RAG +
  consensus + traceability) and it explicitly does **HCC** at lesion level.
- The **self-evolution loop** is a differentiator we don't currently have — cite as
  related work / possible extension.
- Uses ROUGE/BERTScore + LLM/automated safety checks + physician Likert — another
  data point for our eval rubric (alongside the Stanford thoracic-board paper).
- No shared benchmark with MTBBench / HemaGuide / HCC-STAR either.
