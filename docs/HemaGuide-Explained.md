# HemaGuide (Nature Medicine 2026) — explained

_Written 2026-08-28. Paper: Zoller et al. (senior author Mirco Julian Friedrich) — "Clinical
decision support in hematological malignancies using a case-grounded AI agent", **Nature Medicine,
2026**. Groups: DKFZ, HI-STEM, Heidelberg University Hospital.
Link: https://www.nature.com/articles/s41591-026-04494-4_

**Read this one carefully. It is the closest thing to "our project, done properly, in the top
journal in the field."** It is not HCC, but architecturally it is Samavet.

---

## 0. One paragraph

HemaGuide is a **locally deployable multi-agent LLM system** that emulates a **blood-cancer
tumour board**. You feed it the raw, messy physician notes for a patient; it turns them into a
**structured case**, decides on its own which kind of question this is (routine / complex /
genetics-driven), and answers by grounding itself in **guideline flowcharts** plus a **memory of
2,000+ real past tumour-board decisions** plus the literature. It also acts as a **digital
molecular tumour board** — reading the tumour's mutations against international standards and
finding matching targeted drugs in ~40 seconds instead of hours. Tested on **555 external** and
**64 prospective** real cases it agreed with the human board **~82%** of the time, hallucinated
in **0.3%** of cases, and lifted junior doctors to near-senior performance. Runs entirely on
hospital servers, no cloud.

This is the same recipe as Samavet: **unstructured docs → structured schema → route → ground in
guidelines + case memory → explainable recommendation, local model, human-in-the-loop.**

---

## 1. Medical background (short)

- **Hematological malignancies** = blood / bone-marrow / lymph-node cancers: leukaemias,
  lymphomas, myeloma, myelodysplastic syndromes (MDS), and many rare subtypes. The paper covers
  **47 subtypes**.
- These are decided by a **tumour board** (multidisciplinary meeting) just like HCC, but the
  decisive inputs are different: **bone-marrow findings, blood counts, flow cytometry, and above
  all the tumour's genetics** (mutations, fusions, karyotype).
- **Molecular tumour board (MTB)** = a second, specialised board that looks only at the genetic
  profile and asks "is there a targeted drug or trial for this mutation?" Normally slow and only
  available at big academic centres.
- Guidelines here = ELN, NCCN, ESMO, WHO classification — and, like BCLC for liver, a lot of the
  real logic lives in **flowcharts and risk tables**, not prose.

---

## 2. What they built (architecture)

A **modular agent pipeline**, local LLM:

| Stage | What it does |
|---|---|
| **Structuring** | Converts unstructured physician letters / reports into a **structured case representation** (the equivalent of our patient JSON schema). |
| **Routing** | The agent **decides by itself** which of 3 "decision modes" the case needs: <br>• **guideline** — standard case, follow the guideline flowchart <br>• **advanced** — complex/atypical, needs reasoning + literature + case memory <br>• **molecular** — genetics is the deciding factor, run the digital MTB |
| **Grounding** | Answers are anchored in **disease-specific guideline flowcharts** + a **clinical decision memory of >2,000 real past tumour-board cases** + current literature. |
| **Molecular mode** | Classifies each variant against international standards, searches for matched targeted therapies / trials. Median **39 seconds** on standard hardware. |
| **Output** | A treatment recommendation **with explicit written reasoning** and its sources. |

No fine-tuning of a bespoke model — it's an **orchestration layer** that works on top of general
foundation models (they tested **6** of them). Contrast with HCC-STAR, which fine-tuned one model.

---

## 3. THE INPUT

- **Raw unstructured clinical documents** for one patient — physician letters, path/genetics
  reports, history. No requirement that a human pre-structures it.
- Standing access to: guideline flowcharts for the relevant diseases, the 2,000-case decision
  memory, literature.
- Genetics report (for molecular mode).

## 4. THE OUTPUT

- A **treatment recommendation** for this patient.
- **Transparent reasoning** — which guideline branch, which past cases, which papers.
- In molecular mode: variant-by-variant classification + matched targeted therapy / trial options.

---

## 5. Evaluation & results

| Test | Cases | Result |
|---|---|---|
| Expert-blinded benchmark, high-complexity cases, vs 6 base foundation models | 45 | HemaGuide **substantially** beat every raw model |
| **External** validation (independent tumour-board cases, 47 subtypes) | **555** | **81.8%** concordance with the human board |
| **Prospective** (unselected consecutive cases) | **64** | **82.8%** concordance |
| Hallucinations across everything evaluated | 664 | **2 cases (0.3%)** |
| Variant classification speed | — | median **39 s** on standard hardware |
| Resident physicians **with** HemaGuide | — | reached **near-senior** concordance |
| **Ablation** across **11 components** | — | gains were **routing-type-dependent — no single component was enough on its own** |

The ablation is the important scientific bit: they stripped the system down layer by layer and
showed *which* piece matters *depends on the case type* (guideline flowcharts matter for routine
cases, case memory + literature for complex ones, the variant classifier for molecular ones).
That is a real finding, not just "our system works."

---

## 6. Limitations they state

- Not a replacement for clinicians; decision-support only.
- Concordance is measured against the board's decision, not against patient outcomes — an
  **outcomes trial is only "in preparation."**
- Single-institution memory (Heidelberg cases); external set still German.
- Blood cancers, not solid tumours — imaging plays almost no role, so it is **not really
  multimodal** in the image sense.

---

## 7. What this means for our BTP

### The bad news
Someone built the multi-agent, locally-deployed, guideline-grounded, case-memory tumour-board
agent — with a **proper** evaluation (555 external + 64 prospective real cases, blinded experts,
6-model benchmark, ablation, hallucination audit) — and put it in **Nature Medicine**. "We built
a multi-agent tumour board that runs locally" is now clearly **not a contribution by itself**.

### The good news / where the gap is
HemaGuide is **heme, text-driven, German guidelines, no survival prediction, no imaging**. Our
lane is still open:

1. **Solid tumour, genuinely multimodal.** HCC decisions hinge on **reading the CT/MRI and the
   histopathology slide** — LI-RADS, APHE/washout, tumour burden, vascular invasion. HemaGuide
   doesn't touch imaging. This is our hardest, most defensible differentiator.
2. **HCC-specific + Indian guidelines (INASL) + PGIMER clinicians.** A different guideline
   ecosystem and a different patient population (HBV-driven, later-stage, resource-varied). This
   is the "AI paradox in precision oncology" angle — does concordance-with-a-Western-guideline
   even help an Indian cohort?
3. **Survival module.** HemaGuide gives no prognosis. HCC-STAR does (C-index 0.74). A multimodal
   Cox / nomogram module on our structured schema (published HCC nomograms already hit 0.73–0.76,
   multimodal DL 0.82) is a headline metric neither competitor covers well.
4. **The route-first + parse-flowcharts pattern is now validated twice** (HemaGuide + the JCO CCI
   guideline system). We should just adopt it — self-routing to a decision mode, guideline
   *flowcharts* not prose, a case-memory retrieval layer.

### What to copy directly
- **Self-routing** into decision modes (guideline / advanced / molecular) — clean idea, maps
  straight onto HCC (early-stage-curative / intermediate-locoregional / advanced-systemic /
  genetics-if-available).
- **Clinical decision memory** of past board cases as a retrieval source — we have 4 cases now;
  PGIMER could grow this.
- **The evaluation design is the template**: retrospective external set + prospective consecutive
  set + blinded experts + multi-model benchmark + component ablation + explicit hallucination
  count. If our paper looks like this, it is publishable; if it looks like the old BTP (4 cases,
  90% agreement), it is not.
- **Report a hallucination rate.** They did (0.3%); reviewers now expect it.

### Positioning sentence for the paper
> "Prior case-grounded tumour-board agents have been validated in hematology on text-based
> records (HemaGuide, Nature Medicine 2026) or as fine-tuned single models for HCC staging on
> narrative EMRs (HCC-STAR). We extend the paradigm to **multimodal** hepatocellular carcinoma —
> integrating imaging and pathology interpretation — under **Indian (INASL) guidelines**, with an
> attached multimodal survival-estimation module, evaluated prospectively at PGIMER."

---

## Sources
- [Nature Medicine paper](https://www.nature.com/articles/s41591-026-04494-4)
- [PubMed record (PMID 42380678)](https://pubmed.ncbi.nlm.nih.gov/42380678/)
- [DKFZ press release](https://www.dkfz.de/en/news/press-releases/detail/new-ai-system-provides-treatment-recommendations-for-complex-blood-cancers)
- [medicalxpress coverage](https://medicalxpress.com/news/2026-07-ai-treatment-complex-blood-cancers.html)
- [DKFZ repository record](https://inrepo02.dkfz.de/record/313892)
