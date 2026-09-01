# MTBBench — how it works, scores, and relevance

Paper: `../../papers/related/MTBBench_arXiv-2511.20490.pdf` (arXiv:2511.20490,
NeurIPS 2025). Vasilev, Misrahi, Jain et al. — ETH Zürich / EPFL / Geneva Univ. Hospital.
Code: `../../code/MTBBench/` (cloned) · Data: HF `EeshaanJain/MTBBench` (public, ungated).

---

## 1. What it is

A benchmark + agent framework that makes an LLM act like a participant in a
**molecular tumor board (MTB)**: it must gather evidence across modalities and
across time, then answer a clinical question. Not static Q&A — the model has to
*decide which files to open* and reason over a multi-turn, evolving case.

Two tracks:

| Track | Source dataset | Cases | Questions | Modalities |
|---|---|---|---|---|
| **MTBBench-Multimodal** | HANCOCK (head & neck cancer, CC BY 4.0) | 26 | **390** (15/patient, GPT-4o-generated, expert-spot-checked) | H&E slides, IHC (CD3/CD8/CD68/CD163/CD56/MHC1/PD-L1…), hematology reports, clinical/path/surgical data |
| **MTBBench-Longitudinal** | MSK-CHORD (pan-cancer, via cBioPortal) | 40 | **183** | timeline events, somatic mutations, CNAs, structural variants, specimen reports |

Questions are multiple-choice / true-false. Ground truth validated by clinicians
through a purpose-built web app.

## 2. How the agent loop works

- At turn *t* the agent gets a **query** + a **list of available files** `F_t`
  (only data plausibly available at that clinical stage).
- It issues **retrieval requests** `R_t ⊆ F_t` — must actively choose what to read.
- It manages **memory across turns** as the case evolves (new timepoint, new
  genomics, surgical pathology…).
- Two conditions are benchmarked:
  1. **no tools** — model gets only metadata + files it requests
  2. **with tools** — can call:
     - **CONCH** (vision-language pathology FM) for H&E image↔text similarity
     - a **custom IHC tool**: UNI2 embeddings → ABMIL regression → % positive-stained
       cells (`code/MTBBench/data/ABMIL_checkpoint_regression.pt` is that trained head)
     - **PubMed**, **DrugBank** (drug indications / MoA / interactions on the timeline)
- Full agent transcripts for every model are in `code/MTBBench/agent_logs_hancock/`
  and `agent_logs_msk/`.

Key finding: **accuracy tracks how many files/modalities the agent chooses to
access, not model size** — information-gathering skill > raw scale.

## 3. Scores — the current SOTA on MTBBench (Table 2, no-tools baseline)

Only open + mid-tier closed models were run (gpt-4o, o4-mini, gemma-3, InternVL3,
Llama, Mistral, Qwen2.5/3). **No GPT-5 / Claude / Gemini-2.5 in the paper.**
Mean accuracy, bootstrap 95% CI.

### Multimodal track
| Model | Digital Pathology | Hematology | Outcome & Recurrence | **Overall** |
|---|---|---|---|---|
| **internvl3-78b** | 62.0 | 79.7 | 65.6 | **69.1** ← best |
| gpt-4o | 63.2 | 76.9 | 59.9 | 66.7 |
| o4-mini | 59.5 | 77.8 | 55.7 | 64.3 |
| internvl3-38b | 54.7 | 79.7 | 55.9 | 63.5 |
| mistral-small | 62.4 | 75.8 | 51.7 | 63.3 |
| qwen2.5-32b | 53.3 | 73.0 | 63.6 | 63.3 |
| llama-90b | 54.6 | 82.8 | 51.7 | 63.0 |
| gemma-3-12b | 55.9 | 74.9 | 53.6 | 61.5 |
| gemma-3-27b | 51.8 | 76.9 | 42.1 | 56.9 |
| qwen2.5-7b | 42.3 | 61.1 | 53.9 | 52.4 |

### Longitudinal track
| Model | Outcome | Progression | Recurrence | **Overall** |
|---|---|---|---|---|
| **qwen3-32b** | 83.0 | 63.3 | 54.6 | **67.0** ← best |
| llama-3.3-70b | 73.2 | 68.2 | 56.7 | 66.0 |
| gpt-4o | 72.9 | 64.8 | 54.8 | 64.2 |
| o4-mini | 66.0 | 63.1 | 51.1 | 60.0 |
| qwen3-8b | 63.1 | 57.6 | 47.4 | 56.0 |
| gemma-3-12b | 63.3 | 55.9 | 54.6 | 58.0 |
| gemma-3-27b | 57.7 | 50.7 | 47.4 | 51.9 |
| llama-3.1-8b | 60.4 | 49.0 | 45.5 | 51.6 |

**Takeaways:**
- Best overall ≈ **67–69%** — nowhere near solved.
- **Hematology** (structured, interpretable) is the easy task (up to ~83%).
- **Outcome / recurrence / progression** prediction hovers near chance (50%) even
  for the best models — this is the hard, unsolved part, and the one closest to a
  *survival* module.
- Adding tools raises task-level accuracy by up to **+9.0% (multimodal)** and
  **+11.2% (longitudinal)** but longitudinal gains are "modest" — no good FM exists
  for longitudinal clinical reasoning yet.

## 4. Did HemaGuide run on it? — NO

HemaGuide (Nature Medicine 2026, DKFZ) does **not** use MTBBench and MTBBench does
not test HemaGuide — different teams, and MTBBench is head&neck + pan-cancer, not
hematology. HemaGuide reports on its own benchmarks: 45 high-complexity cases
(expert-blinded, 6 foundation models), 555 external + 64 prospective real
tumour-board cases, and a molecular-mode variant set (ClinGen/CGC/VICC). So the
two are not directly comparable. Likewise HCC-STAR and the Nature Cancer agent
were not run on MTBBench.

## 5. Why it matters for our BTP

- Closest published benchmark to Samavet's task (multimodal + longitudinal +
  agentic + clinician-validated). Running on it makes our numbers comparable.
- The **outcome/recurrence near-chance result** is the strongest motivation for
  adding a proper survival/prognosis module.
- The **"more files accessed → higher accuracy"** finding supports our
  retrieval-heavy, structured-schema design over a single prompt.
- Gaps we could target: no HCC, no INASL, images are downsampled tiles not full
  WSIs, no GPT-5/Claude/Gemini-2.5 baselines, and it's offline (no incomplete-data
  / clarification handling — which our `null` model addresses).

## 6. What's in this folder vs. what needs downloading

- `questions_hancock_bench.json` (390 Q) + `questions_msk_bench.json` (183 Q) —
  the full benchmark, with per-question context, file lists, and gold answers.
- `data/hancock/cases/<id>/` + `data/msk_bench/cases/<id>/` — per-case **text/JSON**
  (history, clinical/blood/pathology data, ICD/OPS codes, MSK timelines, mutation/
  CNA/SV CSVs). Fetched from HF.
- **Images NOT included** (HF has downsampled H&E/IHC tiles; full WSIs need
  HANCOCK registration + MSK-CHORD from cBioPortal). To get the HF images:
  `git clone https://huggingface.co/datasets/EeshaanJain/MTBBench` (~1.3 GB) or
  `huggingface-cli download EeshaanJain/MTBBench --repo-type dataset`.
