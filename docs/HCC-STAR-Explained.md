# HCC-STAR explained in detail (+ how it compares to our project)

_Written 2026-08-28. Paper: "Towards Precision Therapy in Hepatocellular Carcinoma: A
Clinical-Reasoning LLM for Risk Stratification and Treatment Guidance", arXiv 2607.08602 (2026)._

This is the paper that worried me most in the related-work scan. This doc explains exactly what
they built, what goes in, what comes out, the medical background you need, and a side-by-side with
our system (Samavet AI).

---

## 0. One paragraph

They **trained their own liver-cancer LLM**. You give it a patient's medical record written as
plain English text (history, lab numbers, what the CT/MRI report said, biopsy findings). It reads
that, reasons step by step, and outputs three things: **(1) a cancer stage, (2) a ranked list of
treatments with the guideline evidence for each, (3) a predicted survival time in months.** They
built it by fine-tuning an open model (Qwen3-32B) on ~30,000 US cancer-registry cases turned into
fake-but-realistic patient notes, then further training it with reinforcement learning that
rewards each correct reasoning step. They tested it on **6,668 real patients from 12 Chinese
hospitals** and it beat the standard staging systems, beat GPT-5 and Gemini-2.5 Pro, and beat both
junior and senior doctors on picking the right treatment.

---

## 1. Medical background you need (skip if you know it)

### The disease
- **HCC = hepatocellular carcinoma** = the most common primary liver cancer (cancer that starts in
  liver cells, not cancer that spread there from elsewhere).
- It almost always happens in a liver that is **already damaged** — usually by chronic hepatitis B
  (HBV), hepatitis C (HCV), or alcohol. So there are always **two problems at once**: the tumour,
  and a sick liver. Treatment has to respect both.

### The three questions a liver-cancer doctor must answer
Every HCC decision boils down to these, and HCC-STAR outputs all three:

1. **How bad is it? (Staging.)**
2. **What treatment? (And in what priority order.)**
3. **How long does the patient have? (Prognosis / survival.)**

### Staging systems (the "how bad is it" scales)
- **BCLC (Barcelona Clinic Liver Cancer)** — the main Western system. Stages **0, A, B, C, D**
  (very early → early → intermediate → advanced → terminal). Each stage maps to a recommended
  first-line treatment. This is what our system (Samavet) uses.
- **CNLC (China Liver Cancer staging)** — the Chinese equivalent. Stages **Ia, Ib, IIa, IIb, IIIa,
  IIIb, IV**. HCC-STAR outputs this because it was tested in China.
- **TNM** — a generic cancer system: **T** = tumour size/extent, **N** = lymph nodes, **M** =
  distant spread (metastasis). Used across all cancers.

To assign a stage you need to know:
- **Tumour burden**: how many tumours, how big, where.
- **Vascular invasion / "cancer thrombus" / PVTT** = the tumour has grown into a blood vessel
  (especially the portal vein). This is bad and pushes the stage up.
- **Extrahepatic metastasis** = the cancer has spread outside the liver (lungs, bone, lymph
  nodes). Also pushes stage up.
- **Liver function** (see next).
- **Performance status** (see next).

### Liver-function scores (how sick is the liver itself)
Computed from blood tests. You'll see these everywhere:
- **Child-Pugh** — class **A / B / C** (good / moderate / poor). Built from 5 things: bilirubin,
  albumin, INR (clotting), ascites (fluid in the belly), and hepatic encephalopathy (confusion
  from liver failure). Child-Pugh A is roughly the cutoff for aggressive treatment.
- **MELD / MELD-Na** — a number (roughly 6–40) from bilirubin, INR, creatinine (+ sodium for
  MELD-Na). Higher = sicker. Used mainly for transplant priority.
- **ALBI (albumin–bilirubin)** — a newer, simpler liver score, grade 1–3.

### Performance status
- **ECOG performance status** — 0 to 4. 0 = fully active; 1 = can't do heavy work; 2 = up and
  about >50% of the day; 3 = mostly in bed; 4 = completely bedbound. Determines whether the
  patient can tolerate treatment at all.

### Blood tumour markers
- **AFP (alpha-fetoprotein)** — a protein often high in HCC. Rising AFP suggests the tumour is
  growing.
- **PIVKA-II / DCP** — another HCC marker, same idea.

### Imaging clues (what the radiology report says)
Liver tumours are scored with **LI-RADS** (LR-1 definitely benign → LR-5 definitely HCC). The
features that make a lesion "LR-5" (definitely cancer):
- **APHE (arterial phase hyperenhancement)** — lights up brightly right after contrast dye.
- **Washout** — then goes darker than surrounding liver on later images.
- **Capsule** — a rim around it.
- Plus **threshold growth** over time.

### The treatment ladder (roughly, early → advanced)
| Treatment | What it is | Roughly for |
|---|---|---|
| **Resection** | Surgically cut out the tumour | Single tumour, good liver, no vascular invasion |
| **Ablation (RFA / MWA)** | Burn the tumour with a needle probe | Small tumours (<3 cm), can't/won't do surgery |
| **Liver transplant** | Replace the whole liver | Small tumour burden + poor liver function (fixes both problems) |
| **TACE (transarterial chemoembolization)** | Inject chemo + block the tumour's artery, via a catheter | Multiple tumours, still confined to liver (intermediate stage) |
| **TARE / radioembolization** | Same idea with radioactive beads | Similar to TACE |
| **Systemic therapy** | Whole-body drugs: immunotherapy combos (atezolizumab + bevacizumab, "atezo-bev"; durvalumab + tremelimumab), or targeted pills (sorafenib, lenvatinib) | Advanced disease: vascular invasion or spread outside liver |
| **Best supportive care** | Comfort care only | Terminal stage / very poor liver |

- **Treatment intent** is either **curative** (resection, ablation, transplant — trying to
  eliminate the cancer) or **palliative** (TACE, systemic therapy — trying to control it and
  extend life).

### Prognosis metric
- **Harrell's C-index (concordance index)** — measures how well a model's predicted risk ordering
  matches who actually died sooner. **0.5 = random guessing, 1.0 = perfect.** Around 0.66–0.68 is
  what the standard staging systems get; HCC-STAR got ~0.71–0.74.

### Datasets / registries
- **SEER** — a large US public cancer registry (National Cancer Institute). Has structured
  variables (age, tumour size, AFP, TNM stage, months survived) for hundreds of thousands of
  cancer patients, but **no free-text notes**. HCC-STAR used ~30,000 HCC cases from it.

---

## 2. What they actually built (the model)

### Base model
**Qwen3-32B** (a 32-billion-parameter open-weight model from Alibaba). They compared Qwen3-8B,
Qwen3-32B, QWQ-32B, and DeepSeek-R1 and picked Qwen3-32B.

### Two-stage training

**Stage 1 — Supervised fine-tuning (SFT), "Clinical-Knowledge Familiarization".**
- ~20,000 instruction → response pairs.
- Standard next-token training (cross-entropy loss).
- Purpose: teach it the vocabulary, the guideline logic, and the required output format.

**Stage 2 — Reinforcement learning (RL), "Experience-Accrual".**
- Algorithm: **GRPO (Group Relative Policy Optimization)** — the same family of RL used to train
  reasoning models like DeepSeek-R1. The model generates several answers per case, they get
  scored, and the model is nudged toward the higher-scoring ones.
- The scoring function is the interesting bit ↓

### The "step-verifiable composite reward"
Instead of only rewarding the final answer, they reward **each checkable intermediate step**. The
reward is a sum of:

1. **Process reward** — the model must emit 9 intermediate clinical facts as tags and get them
   right: `<ps>` (performance status), `<child_pugh>`, `<metastasis>`, `<cancer_thrombus>` (PVTT),
   `<num_tumor>`, `<tumor_size>`, `<cnlc>`, `<bclc>`, `<tnm>`. Score = fraction of the 9 that match
   the ground truth.
2. **Format reward** — 17 required structural tags must all be present exactly once (keeps the
   output parseable).
3. **Treatment-ranking reward** — how well its ranked treatment list matches the guideline's
   reference ranking.
4. **Survival-estimation reward** — accuracy of the predicted survival time, handling "censored"
   data (patients still alive at last follow-up).
5. **Brevity + chain-of-thought quality rewards** — don't ramble, reason properly.

They deliberately **kept the treatment-ranking and survival updates separate** ("decoupled") so
the two objectives don't fight each other during training.

**Why this matters for us:** this is a clean idea — force the model to show its staging work as
structured tags, and grade each tag. Our reasoning agent could do the same thing (emit
`<child_pugh>`, `<bclc>`, etc.) and we could grade it step-by-step in our evaluation harness even
without doing RL.

---

## 3. THE INPUT (what you feed it)

**Format:** one blob of **free-text EMR narrative** — "heterogeneous clinical narratives
end-to-end: diagnostic notes, imaging reports, operative summaries, discharge documentation." Not
a structured form. You paste in the patient's written record.

**Content it expects to find in that text:**

| Category | Specific items |
|---|---|
| Demographics | age, sex, marital status |
| Clinical status | ECOG performance status, Child-Pugh class |
| Labs | complete blood count, liver function tests (bilirubin, albumin, AST/ALT, INR…), tumour markers (AFP) |
| Imaging | findings from abdominal CT, contrast-enhanced MRI, ultrasound — **the report text, not the images** |
| Tumour details | size, number, vascular invasion, extrahepatic metastasis |
| Pathology | biopsy morphology **when available** (often it isn't — HCC is frequently diagnosed on imaging alone) |
| Viral status | HBsAg (hepatitis B) status |

**Key limitation of their input:** it is **text only**. HCC-STAR does **not** look at a CT scan,
an MRI, or a pathology slide. It reads what a human already wrote about them. If nobody wrote a
radiology report, HCC-STAR has nothing to work with.

---

## 4. THE OUTPUT (what comes back)

A single generated response containing a **chain-of-thought** plus three structured deliverables:

1. **Risk-stratified stage** — an A–D risk tier (their own quantile-based version), plus the
   standard CNLC / BCLC / TNM stages, derived in the reasoning.

2. **Ranked treatment list with rationale** — e.g. *"Resection >> Ablation >> Transplant (not
   prioritized)"*, each option annotated with its guideline evidence level (e.g. "Level 1,
   Recommendation A").

3. **Individualised survival estimate** — median overall survival in months, plus a risk tier.

**Worked example from the paper (Figure 4):**
- Input: 44-year-old man, ECOG 0, Child-Pugh A, one 1.2 × 2.0 cm tumour in liver segment 3.
- Reasoning: "CNLC Ia / BCLC A / TNM T1a … Surgical resection → Level 1, Recommendation A …
  Ablation → Level 1, Recommendation A … Transplant → Level 2 …"
- Output: **Resection >> Ablation >> Transplant.**

---

## 5. Training data construction (the clever/risky part)

SEER gives structured numbers but no prose. So:
1. Take a SEER case's variables (age, tumour size, AFP, TNM, survival months).
2. Use **GPT-4o** + templates + the CNLC 2024 guideline + some real de-identified EMRs as style
   references to **write a synthetic patient narrative** with "embedded guideline logic and brief
   evidence notes."
3. Keep both versions: the structured table **and** the synthetic narrative.
4. **Fidelity check:** 3 clinicians blind-scored 140 synthetic records on 6 dimensions →
   average 4.81 / 5, 96% rated "clinically acceptable", inter-rater agreement ICC 0.73.

**Risk they admit:** the synthetic notes may not match how real hospital notes are actually
written ("distributional shift").

---

## 6. Evaluation (why it's a strong paper)

**Cohorts:**
- Internal SEER test set: ~2,000 patients.
- External: **6,668 patients across 12 Chinese hospitals** (4,190 usable for the survival
  analysis after exclusions).
- A 60-case curated set for the physician-comparison and physician-assistance studies.

**Results:**

| Task | HCC-STAR | Comparators |
|---|---|---|
| Treatment top-1 accuracy | **79.2%** | resident 63.3%, attending 66.7%, also beat GPT-4o / DeepSeek-R1 on quality + safety |
| Survival C-index (external) | **0.737** (0.723–0.751) | BCLC / CNLC ≈ 0.66–0.68 |
| Survival C-index (SEER internal) | **0.708** | TNM / BCLC / CNLC 0.66–0.68 |
| 1/3/5-year AUROC | highest of all methods | — |
| KM curves for its A–D tiers | cleanly separated, log-rank P < 10⁻⁴ | — |
| Hypothetical median OS if you follow its advice | **51 months** | BCLC 29, CNLC 32 |
| Physician assistance | residents 63.3% → **73.0%** top-1; decision time down | attendings roughly unchanged top-1, +8.3% top-2 |

**Stated limitations:**
- Retrospective only — no prospective trial.
- The "51 vs 29 months" number is imputed/hypothetical and vulnerable to confounding (sicker
  patients get gentler treatment anyway).
- Synthetic training narratives ≠ real EMRs.
- Fairness across subgroups (HBV vs non-HBV) not fully checked.
- Baselines (GPT-5, Gemini-2.5) go stale fast.
- Needs continual retraining as guidelines change.

---

## 7. HCC-STAR vs our project (Samavet AI)

### 7a. Input — what each system takes in

| | HCC-STAR | Samavet AI (ours) |
|---|---|---|
| Form of input | One free-text EMR narrative you paste in | **Structured JSON schema** filled via a dashboard **or auto-extracted from uploaded PDFs** |
| Demographics | age, sex, marital status | age, sex, BMI, region |
| Clinical | ECOG, Child-Pugh (as text) | etiology (HBV/HCV/alcohol), symptoms, comorbidities, ECOG, ascites, encephalopathy, free-text notes |
| Labs | CBC, LFTs, AFP — as written in the note | **Full structured panel** (Hb, WBC, platelets, total/direct bilirubin, AST, ALT, ALP, albumin, INR, PT, sodium, creatinine, AFP, PIVKA-II, CRP), **baseline + longitudinal time series** |
| Imaging | the **text** of CT/MRI/US reports | radiology **report text + structured lesion-level annotations** (size, segment, LI-RADS, APHE/washout/capsule, PVTT, extrahepatic mets, mRECIST) **+ the actual DICOM images** (radiology agent can run GPT-V / MedSAM-type models) |
| Pathology | biopsy morphology if mentioned | structured: grade/differentiation, vascular invasion, margin status, IHC/molecular markers **+ uploaded pathology PDF** |
| Treatment history | whatever the note says | **structured array**: each prior therapy (TACE, RFA, resection, sorafenib…) with date and response |
| Tumour board notes | — | free-text prior MDT discussion (used as ground truth) |
| Missing-data handling | implicit (if it's not in the text, it's not there) | **explicit**: `null` = "not assessed" vs `"none"` = "checked, absent"; dashboard flags which fields are still needed for staging |
| Non-clinical context | — | treatment-plan context can include constraints (e.g. financial) — partly |

**Takeaway on input:** we take in **more, and more structured** — real images, lab trends over
time, an explicit missing-data model, and a PDF ingestion pipeline. HCC-STAR needs someone to
have already written a good narrative; we can start from raw hospital documents. Their advantage
is simplicity: one text box, no schema to fill.

### 7b. Output — what each system produces

| | HCC-STAR | Samavet AI (ours) |
|---|---|---|
| Staging | risk tier A–D + CNLC + BCLC + TNM, with reasoning | BCLC stage (+ Child-Pugh, MELD, MELD-Na, ALBI **computed deterministically in code**), LI-RADS, Tumour Burden Score |
| Treatment | **ranked list** with per-option guideline evidence level | "Proposed Treatment Strategy" section + caveats, grounded in **INASL** guidelines; consolidated multi-specialist reasoning |
| Survival / prognosis | **yes — median OS in months + risk tier, C-index ~0.74** | **no — we don't predict survival at all** ← clear gap |
| Format | one CoT response with structured tags | modular per-agent outputs (clinical summary, lab interpretation, radiology summary + interpretation, pathology summary) → merged tumour-board report (Clinical Summary / Diagnostic Findings / Proposed Treatment Strategy / Caveats) |
| Explainability | chain-of-thought text + step tags | per-agent traceable outputs + confidence/provenance metadata + knowledge-graph view + editable by clinician |
| Interaction | single response, no follow-up | **human-in-the-loop chat**, clinician reviews/edits every section |

**Takeaway on output:** HCC-STAR does one thing we don't — **survival prediction with a real
validated C-index**. We produce a richer, more auditable, board-style report with a human in the
loop. Adding a survival-estimate module (even a simple Cox model on our structured fields) would
close their biggest lead.

### 7c. Architecture / method

| | HCC-STAR | Samavet AI (ours) |
|---|---|---|
| Approach | **one fine-tuned model** (Qwen3-32B), SFT + GRPO RL | **multi-agent pipeline** of off-the-shelf LLMs + RAG + knowledge graph + hard-coded rules + orchestrator |
| Training | yes — needs GPUs, SEER data, RL infra | none — prompting + retrieval + rules |
| Guidelines | CNLC 2024 + BCLC, baked into weights | INASL, via retrieval/grounding (swappable without retraining) |
| Deployment | research model | deployed web app: OAuth, RBAC, audit log, encryption, PDF ingestion, charts |
| Multimodal | text only | text + structured + real images |
| Evaluation | 6,668 patients / 12 hospitals / SEER 30k | 4 cases so far (framework designed for 50–100) |

### 7d. What this means for our BTP

1. **Don't compete on HCC staging/treatment accuracy alone** — they have a fine-tuned model and
   6,668-patient validation; we won't beat that with prompting on a handful of cases.
2. **Our differentiators are real and defensible:** multimodal (actual images), structured +
   longitudinal data, PDF ingestion, explicit missing-data handling, multi-agent auditable
   board-style output, human-in-the-loop, deployed, Indian/INASL context.
3. **Close the obvious gap:** add a **survival / prognosis estimate**. We already collect every
   variable a standard prognostic model needs. Even a classical statistical model (Cox regression
   / a published HCC nomogram) bolted onto our schema would let us report a C-index and compare
   directly.
4. **Borrow their step-verifiable idea for our evaluation:** have our reasoning agent emit
   structured staging tags (`<child_pugh>`, `<bclc>`, `<pvtt>`, `<mets>`…) and grade each one
   against ground truth in our harness. Cheap, and it makes our numbers granular and comparable.
5. **Borrow their data trick if we're short on cases:** SEER + guideline-guided synthetic
   narrative generation, with a small clinician fidelity check, is a legitimate way to expand a
   thin dataset — they validated it at 4.81/5.
6. **Position statement:** *HCC-STAR is the strongest single-model HCC reasoner; Samavet AI is the
   first deployed, multimodal, multidisciplinary, human-in-the-loop tumour-board system that also
   works from raw hospital documents and incomplete data.*

---

## Sources
- [HCC-STAR paper (arXiv 2607.08602)](https://arxiv.org/abs/2607.08602)
- [HCC-STAR full text (arXiv HTML)](https://arxiv.org/html/2607.08602v1)
- [BCLC 2026 update — Journal of Hepatology](https://www.journal-of-hepatology.eu/article/S0168-8278(25)02571-1/fulltext)
- Our BTP report: `BTP_report.pdf` (schema in Chapter 4, agents in Chapter 5)
