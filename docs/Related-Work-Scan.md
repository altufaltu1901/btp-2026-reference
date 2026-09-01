# Related Work Scan — who else is doing AI tumour boards (and what it means for us)

_Written 2026-08-28. A light scan of newer / stronger work than Ferber et al. (2025), kept to
what actually affects our project. Not a full literature review._

**Bottom line up front:**
- The field moved fast. "One LLM + tools" (Ferber, 2025) is now the *old* design. The 2026
  consensus is **multi-agent systems** that split the work into specialist roles that debate —
  which is exactly Samavet AI's design, so we're on the right track architecturally.
- **The real threat to our novelty is HCC-STAR** — a 2026 HCC-specific reasoning model trained on
  ~30k cases and validated on 6,668 patients across 12 hospitals. Much bigger and more rigorous
  than anything in our BTP report. We need to be aware of it and position against it.
- Everyone's weak spot is still the same as ours: **small, retrospective evaluation**. Nobody has
  a large prospective trial with patient-outcome endpoints. That's the gap to aim for.
- **Standard benchmarks now exist** (MTBBench, MedAgentBench, AgentClinic). We should run against
  at least one so our numbers are comparable to the field, not just to ourselves.

---

## 1. The most relevant systems

### HCC-STAR — HCC-specific clinical-reasoning LLM (arXiv 2607.08602, 2026) ⚠️ closest competitor
- Specialised LLM for **hepatocellular carcinoma**: risk stratification, treatment recommendation,
  survival prediction from EMR-style text.
- "Knowledge-aligned reasoning framework optimised with a step-verifiable composite reward" —
  i.e. trained to *reason*, not memorise guidelines, with rewards for each correct reasoning step.
- Data: ~**30,000 HCC cases from SEER**, expanded into EMR narratives via a clinician-validated
  augmentation workflow.
- Evaluation: multi-centre Chinese cohort, **6,668 patients across 12 hospitals**.
- Results: beat BCLC and CNLC staging systems, beat GPT-5 and Gemini-2.5 Pro, beat residents *and*
  attendings on treatment accuracy; hypothetical median survival 51 months under its
  recommendations vs 29–32 months for guideline-based; blinded hepatobiliary specialists rated its
  reasoning trustworthy.
- **What it means for us:** this is the strongest HCC decision-support result to date and it's
  *not* a tumour board — it's a single specialised reasoner. Our differentiation has to be the
  things it doesn't do: **multimodal** (it's text/EMR only — no direct imaging or pathology-slide
  input), **multidisciplinary/explainable board-style output**, **human-in-the-loop workflow**,
  **deployed product**, **Indian/INASL context**, **handles incomplete real-world data**.
  Consider using HCC-STAR-style step-verifiable rewards as an idea for our reasoning agent.

### EvoMDT — evolving multidisciplinary team agents (via Frontiers review, 2026)
- **4 agents**: diagnostic, therapeutic, safety-monitoring, coordinator.
- Evaluated on 6 benchmarks + real-world data across breast, lung adeno, **HCC**, lymphoma.
- **What it means for us:** near-identical philosophy to Samavet's agent roster. Confirms the
  "specialist agents + coordinator + safety checker" pattern is now standard. Our
  Evaluation Agent ≈ their safety-monitoring agent.

### Multi-Disciplinary Agent Team (MDAT) — gynecologic oncology (medRxiv 2025.10.30.25339199)
- Agents use **different prompting strategies to simulate different specialist viewpoints**, then
  **debate / appraise evidence** like a real MDT.
- Reported: higher MDT-grade concordance, more complete and auditable rationales than single-agent
  baselines, low contraindication-violation rate.
- **What it means for us:** the *debate between agents* is the value-add they measured. Samavet's
  orchestrator currently just merges outputs — adding a genuine cross-agent disagreement/resolution
  step (and measuring concordance + contraindication rate) is a concrete, publishable improvement.

### Virtual MDS Panel (VMP) — myelodysplastic syndromes (medRxiv, 2026)
- "Domain-specialised, **rule-bound** software agents" tied to WHO/ICC guidelines and IPSS-R/IPSS-M
  scoring, compared against general-purpose LLMs.
- **What it means for us:** same hybrid idea as Samavet's rule-based routing + INASL grounding.
  Validates combining hard scoring rules with LLM agents. Good methodological reference for how to
  write up "rule-bound agents vs plain LLM".

### Thoracic Tumor Board multi-agent system — Stanford (arXiv 2604.12161, 2026) — *deployed*
- AI workflow that generates **case summaries displayed live during the real tumour board**.
- Went from manual → automated summarisation; compared to physician gold-standard summaries with
  fact-based scoring rubrics; **validated "LLM-as-judge"** for scoring; did **post-deployment
  monitoring**.
- **What it means for us:** the best template for the *deployment + evaluation* half of our work.
  Copy their approach: physician gold-standard summaries, fact-based rubric, LLM-as-judge (with a
  human-validation check), post-deployment monitoring. This is what turns Samavet's "4 cases,
  ~90%" into a credible study.

### VISTA Architect — graph-database health AI for MDTs (arXiv 2606.22692, 2026)
- Health AI system built **around a graph database**, demonstrated in multidisciplinary tumour
  boards.
- **What it means for us:** directly relevant to Samavet's knowledge-graph reasoning engine. Worth
  reading for how they structure the clinical graph and query it. Potential citation / comparison
  point for our KG component.

### Others worth knowing (one line each)
- **LungNoduleAgent** — sequential modules + inter-agent deliberation for lung nodules, 86.7–89.1%.
- **GPT-Plan** — "dosimetrist" + "physicist" agents for radiotherapy planning.
- **OncoPainBot** — 4-agent cancer pain management, 0.841 accuracy.
- **PathFinder** — multi-agent navigation + diagnosis on melanoma whole-slide images.
- **Deliberative multi-agent LLMs in ophthalmology** (arXiv 2603.21447) — debate improves reasoning.
- **Multi-Agent open-source LLM for structured cancer reporting** (ACL BioNLP 2026) — open models,
  structured extraction focus.

---

## 2. Benchmarks we should actually use

Running against a shared benchmark makes our results comparable to the field. Priorities:

| Benchmark | What it tests | Why it matters to us |
|---|---|---|
| **MTBBench** (arXiv 2511.20490) | Molecular-tumor-board decisions: **multimodal + longitudinal + multi-agent**, physician-validated Qs | Closest to our exact task. Their own agentic framework only gained +9–11% on multimodal/longitudinal reasoning → lots of headroom. Good target. |
| **MedAgentBench** (NEJM AI, 2025; v2 at PSB 2026) | 300 EHR tasks, 10 categories, tool use in a virtual FHIR EHR | Standard for agent *tool-use* competence. Best model (Claude 3.5 Sonnet v2) only ~70% → shows agents are far from solved. Use to sanity-check our agents' tool use. |
| **AgentClinic** (npj Digit Med, 2026) | Multimodal, tool-using clinical agents in simulated encounters; MedQA + MIMIC-IV | Key finding: **static QA benchmarks massively overstate clinical skill.** Justifies why we need interactive/simulation eval, not just accuracy on cases. |
| **Multidimensional benchmarking framework for oncology LLMs** (Sci Rep, 2026) | Expert-rated accuracy + explainability + **cost + latency + efficiency** → Composite Performance Score | Template for evaluating our *local vs cloud model* trade-off with real operational metrics. |
| **Multi-center LLM benchmarking, lung cancer screening** (Cell Rep Med, 2025) | LLM CDS across multiple sites | Reference for how to structure a multi-site comparison. |

Also: **GPT for HCC BCLC staging** (Sci Rep, 2026) tested GPT-4 / o1 / o3 / GPT-5.4 on MELD, ALBI,
Child-Pugh, BCLC and treatment — all >85% on liver-function assessment, **MELD calculation the
most error-prone**. Useful: it shows even frontier models make arithmetic staging errors, which
justifies Samavet computing these scores **deterministically in code** rather than letting the LLM
do the maths. Cite this as motivation.

---

## 3. Findings that keep recurring (and what we should do about them)

1. **Multi-agent > single-agent for complex MDT decisions** — but with a catch: "compounded
   opacity" — one agent's error gets amplified through rounds of negotiation.
   → *For us:* keep the Evaluation/safety agent, log every agent's intermediate output, and
   measure whether the multi-agent version actually beats a single-agent baseline on our data.

2. **Evaluation is universally weak** — retrospective cases, small feasibility samples, no
   prospective trials with patient-outcome endpoints.
   → *For us:* this is the single biggest opportunity. Even 50–100 well-annotated cases with
   multiple blinded raters + a fact-based rubric would be competitive. A prospective co-pilot
   pilot at PGIMER with outcome tracking would be genuinely ahead of the field.

3. **Hallucination + overconfidence still unsolved**; RAG and guideline grounding reduce but don't
   remove it.
   → *For us:* report a harmful-response rate (Ferber's was 2.4%) and a contraindication-violation
   rate (MDAT-style). Low numbers here are our strongest safety claim.

4. **Regulation targets single-purpose tools, not autonomous agents.** Reviews recommend framing
   these as **"advanced consultation systems to assist experts, not automated patient-facing
   tools."**
   → *For us:* keep the human-in-the-loop framing front and centre — it's both safer and more
   defensible. Samavet already does this.

5. **"Task–architecture alignment"** — don't use a multi-agent system where a simple LLM call
   would do. Structured extraction → plain LLM; guideline recommendation → single agent;
   full multidisciplinary decision → multi-agent.
   → *For us:* good justification for Samavet's rule-based router — cheap tasks shouldn't wake the
   whole board. We can measure LLM-call savings as an efficiency contribution.

6. **The "translational paradox"** (Frontiers, 2026): lots of algorithmic over-engineering in HCC
   AI, little real-world clinical utility.
   → *For us:* lean into deployment, usability, incomplete-data handling, and clinician workflow
   fit — the things Samavet already emphasises — rather than chasing another 1% on a benchmark.

---

## 4. How this changes our plan

- **We're not first, and not the strongest on raw HCC accuracy** (HCC-STAR). Stop framing the
  contribution as "beating Ferber on accuracy." Frame it as:
  *the first deployed, multimodal, multidisciplinary, human-in-the-loop tumour-board emulator for
  HCC that is grounded in INASL guidelines, handles incomplete real-world data, and is validated
  prospectively with clinicians.*
- **Add a genuine agent-debate / disagreement-resolution step** and measure MDT-grade concordance
  + contraindication rate (MDAT-style) vs a single-agent baseline.
- **Run on MTBBench** (and optionally MedAgentBench for tool use) so our numbers are comparable.
- **Adopt the Stanford thoracic-board evaluation method**: physician gold-standard summaries,
  fact-based rubric, validated LLM-as-judge, post-deployment monitoring.
- **Keep deterministic score computation** and cite the GPT-HCC-staging paper as why.
- **Report safety metrics explicitly** (harmful %, contraindication %, hallucination %).
- **Local-model story**: use the multidimensional (accuracy + cost + latency) framework to show a
  local/open model is "good enough" for deployment where GPT-4/5 can't legally go.

---

## 5. Additional papers — the fuller landscape (added after a second pass)

The area is now **crowded**. "AI tumour board" as a concept is no longer novel. Grouping the rest
by type so you can see where the white space is.

### 5a. LLM-vs-tumour-board concordance studies (retrospective — the common, low-novelty type)
These all do the same thing: run an LLM on N past cases, compare its recommendation to what the
real board decided, report a concordance %. Easy to publish in mid-tier oncology journals, but the
field is saturated with them.
- **Primary liver tumours (HCC + cholangio), single-centre** (Cancers/MDPI 2026) — 50 HCC + 50 CCA
  cases. **ChatGPT 66% concordance with the board on HCC (κ=0.60); Claude only 38%.** ← directly
  our disease, directly our task. This is the closest "prior result" for HCC tumour-board LLMs.
- **HCC treatment recommendations, nationwide registry** (PLOS Medicine 2026) — large retrospective
  cohort; concordance with physician decisions **associated with better survival in early-stage
  HCC, not in advanced**. Good citation for "why concordance matters".
- **Colorectal cancer MTB: guideline-integrated general model vs domain-specialised model** (Curr
  Oncol 2026) — **the guideline-integrated general-purpose LLM beat the domain-specialised model
  that had no retrieval.** ← evidence *for* our RAG-heavy design over pure fine-tuning.
- **Breast cancer** (multiple: Senology/ChatGPT-3.5, "Evolution of LLMs for breast cancer") —
  concordance improves with model generation.
- **Colorectal liver metastases: AI vs MDT** (Sci Rep 2026).
- **ChatGPT-4 MTB recommendations, critical evaluation on real-world data** (The Oncologist 2025).

### 5b. Prospective / blinded validation (rarer, higher value — this is the white space)
- **CONCORDIA study** (urological oncology, *European Urology Oncology*-adjacent, 2026) —
  **blinded, prospective** LLM vs MDT. Claude 3.5 Sonnet **narrowly missed** the pre-set
  non-inferiority margin. First rigorous prospective head-to-head.
- **"The AI paradox in precision oncology"** (ASCO 2026 abstract) — **prospective blinded** LLM vs
  molecular tumour board, cases from a **Tamil Nadu / India national MTB**, Jul 2025–Jan 2026.
  Finding: LLM concordance is high exactly where guidelines are clear and low where support is
  most needed. ← Indian context, same as us.
- **Prospective multi-centre evaluation of guideline-based AI to streamline the MDT** (Frontiers in
  Oncology 2026) — closest to a "deployed co-pilot" study.
- **RCT protocol: decision algorithm vs MDT for liver cancer** (2023) — shows the RCT design is
  considered feasible for liver cancer specifically.

### 5c. Multi-agent tumour-board systems (our architectural peers)
- **Tumour Board–Inspired Multiagent AI for interpreting oncology guidelines** (JCO CCI 2026,
  Nazha group) — Coordinator agent picks the guideline, Tumour Board agents extract from
  text/tables/**figures**, Reviewer agent synthesises. Scope limited to **34 ASCO guidelines** —
  it's a guideline-QA system, **not** a full patient-data tumour board. Beat existing tools on
  guideline questions. Good "closest published multi-agent" citation; our scope (full multimodal
  patient case) is broader.
- **EvoMDT** — self-evolving 4-agent system, multi-cancer incl. HCC, "evidence-traceable,
  lesion-level".
- **CASCADE** (2024) — context-aware data-driven AI to streamline MDT recommendations (earlier,
  single-model-ish).
- **VISTA Architect** (arXiv 2026) — graph-DB-centred health AI, demoed in MDTs (relevant to our KG).
- **MDAT** (gynae onc), **thoracic board** (Stanford, deployed), **VMP** (MDS) — covered in §1.
- **iScience 2025 review** — "AI in multidisciplinary tumour boards" — good survey to cite for the
  intro / background.

### 5d. RAG-for-tumour-board specifically
- **Retrieval-Augmented Therapy Suggestion for Molecular Tumour Boards** (JMIR 2025) — algorithmic
  dev + validation of a RAG pipeline for MTB therapy suggestions. Direct precedent for our
  guideline-RAG component; note their point that "systematic preclinical validation of RAG
  pipelines for MTB is still lacking".

### 5e. Domain-specialised clinical LLMs (the HCC-STAR family)
- **HCC-STAR** — §1, the main one.
- **GastroGPT** — GI-specialised LLM (in our BTP report's related work already).
- **Expert-Guided LLMs for CDS in precision oncology** (Lammert et al., JCO PO 2024) — human-in-the-
  loop feedback improved molecular tumour board recommendations; cited by Ferber too.

---

## 6. Is there a paper in this? — honest take

**Short version:** You will get a solid BTP out of this with the engineering alone. A *paper*
needs one sharp research question executed with a real evaluation — the system by itself is not a
contribution any more (2024 it might have been; Ferber got Nature Cancer with 20 cases as a
first-mover, that window is closed).

### Why "we built a multimodal multi-agent tumour board" is not, by itself, a paper now
- EvoMDT, the JCO CCI multiagent system, VISTA, MDAT, the Stanford thoracic board — all published,
  all multi-agent, some deployed. The concept is done.
- ~10 LLM-vs-board concordance papers exist, including **two for HCC/liver specifically**
  (MDPI Cancers, PLOS Medicine).
- Reviewers now ask: *what did you show that we didn't already know?*

### The 4 real research angles (pick one, do it properly)

1. **Prospective co-pilot deployment at PGIMER.** _Highest value._ Almost nobody has this. Run the
   system live alongside a real HCC tumour board for a defined period; measure concordance, time
   saved, clinician trust, safety events, and whether it changed any decisions. CONCORDIA and the
   Tamil Nadu study are prospective but *LLM-vs-board*, not *deployed co-pilot in routine use*.
   Venue: JCO CCI, npj Digital Medicine, JAMA Network Open, Lancet Digital Health (if done well).

2. **Ablation — what in the pipeline actually helps?** The field *assumes* multi-agent + KG + rules
   beats a single frontier LLM + RAG. The colorectal-MTB paper found the opposite direction is
   possible. A clean ablation on the same HCC case set (single LLM → +RAG → +multi-agent →
   +KG/rules → +deterministic scoring) showing which components move which metric (staging
   accuracy, contraindication rate, completeness) is genuinely useful and underdone. Venue: JCO
   CCI, a MICCAI / ML4H / AMIA workshop, or a methods journal.

3. **Uncertainty via reasoner disagreement.** Run the two reasoners (frontier+RAG ‖ fine-tuned /
   HCC-STAR-style) in parallel; test whether *their disagreement predicts the cases the human
   board itself deliberated / split on / later revised*. "The system knows when it's uncertain"
   is a novel, clinically meaningful claim. Nobody's shown this for tumour boards.

4. **Incomplete-data robustness.** Everyone benchmarks on clean vignettes. Systematically ablate
   input fields and measure how tumour-board-AI degrades, and whether explicit `null` handling +
   completeness flagging (which we already have) mitigates it. Real-world-relevant, unoccupied.

### Weakest angles (don't lead with these)
- "Another HCC concordance study" — HCC is already covered twice; you'd be incremental.
- "Multimodal matters" — the field already accepts this; only worth it if you *demonstrate*
  specific cases where the image flips the recommendation and your pipeline gets them.
- "We beat Ferber's numbers" — different task, different cases, not a clean comparison.

### Realistic outcome
- **BTP thesis:** yes, easily — system + one of the evaluations above.
- **Workshop paper (ML4H, MICCAI, AMIA, EMNLP-clinical):** very achievable with angle 2, 3, or 4.
- **Mid-tier journal (JCO CCI, Cancers, Current Oncology, npj Digital Medicine):** achievable with
  a well-run version of angle 1 or 2.
- **Top-tier (Nature Cancer / Lancet Digital Health):** needs angle 1 done at real scale with a
  proper cohort and ideally outcome linkage. Possible but a bigger commitment than a BTP.

The engineering being strong *helps* — it's what makes angles 1–4 executable at all — but the
paper's spine has to be the evaluation question, not the architecture diagram.

---

## Sources

- [Ferber et al., Nature Cancer 2025 — the baseline](https://www.nature.com/articles/s43018-025-00991-6)
- [HCC-STAR: Clinical-Reasoning LLM for HCC (arXiv 2607.08602)](https://arxiv.org/abs/2607.08602)
- [Precision oncology: from LLMs to multi-agent systems — Frontiers in Oncology 2026 review](https://www.frontiersin.org/journals/oncology/articles/10.3389/fonc.2026.1828507/full)
- [MTBBench: Multimodal Sequential Clinical Decision-Making Benchmark in Oncology (arXiv 2511.20490)](https://arxiv.org/abs/2511.20490)
- [Multi-Disciplinary Agent Team for gynecologic oncology (medRxiv 2025.10.30.25339199)](https://www.medrxiv.org/content/10.1101/2025.10.30.25339199)
- [AI-driven virtual tumor board for myelodysplastic syndromes (medRxiv 2026)](https://www.medrxiv.org/content/10.64898/2026.03.26.26349088v1)
- [Multi-agent system for thoracic tumor board — Stanford (arXiv 2604.12161)](https://arxiv.org/abs/2604.12161)
- [VISTA Architect: graph-database health AI for MDTs (arXiv 2606.22692)](https://arxiv.org/pdf/2606.22692)
- [MedAgentBench: Virtual EHR environment for medical LLM agents (NEJM AI 2025)](https://ai.nejm.org/doi/full/10.1056/AIdbp2500144)
- [AgentClinic: multimodal benchmark for tool-using clinical AI agents](https://agentclinic.github.io/)
- [A multidimensional benchmarking framework for LLMs in oncologic decision making (Sci Rep 2026)](https://www.nature.com/articles/s41598-026-61195-1)
- [GPT-based LLMs in HCC stratification: liver function, BCLC staging, treatment (Sci Rep 2026)](https://www.nature.com/articles/s41598-026-56992-7)
- [Multi-center benchmarking of LLMs for lung cancer screening CDS (Cell Reports Medicine 2025)](https://www.cell.com/cell-reports-medicine/fulltext/S2666-3791(25)00538-5)
- [The translational paradox of AI in HCC (Frontiers in Oncology 2026)](https://www.frontiersin.org/journals/oncology/articles/10.3389/fonc.2026.1848190/full)
- [Deliberative multi-agent LLMs in ophthalmology (arXiv 2603.21447)](https://arxiv.org/pdf/2603.21447)
- [BCLC strategy 2026 update (Journal of Hepatology)](https://www.journal-of-hepatology.eu/article/S0168-8278(25)02571-1/fulltext)
- [LLMs for MDT decision-making in primary liver tumours — retrospective single-centre (Cancers/MDPI 2026)](https://www.mdpi.com/2072-6694/18/13/2175)
- [Clinical utility of LLMs for HCC treatment recommendations — nationwide registry (PLOS Medicine 2026)](https://journals.plos.org/plosmedicine/article?id=10.1371/journal.pmed.1004855)
- [LLMs in colorectal cancer MTB: guideline-integrated general vs domain-specialised (Current Oncology 2026)](https://doi.org/10.3390/curroncol33060309)
- [CONCORDIA: blinded prospective LLM vs MTB in urological oncology](https://www.sciencedirect.com/science/article/abs/pii/S2588931125003268)
- [The AI paradox in precision oncology: prospective blinded LLM vs MTB (ASCO 2026 abstract)](https://ascopubs.org/doi/10.1200/JCO.2026.44.16_suppl.11047)
- [Prospective multi-centre evaluation of guideline-based AI to streamline the MDT (Frontiers in Oncology 2026)](https://www.frontiersin.org/journals/oncology/articles/10.3389/fonc.2026.1785951/full)
- [Tumour Board–Inspired Multiagent AI for interpreting oncology guidelines (JCO CCI 2026)](https://ascopubs.org/doi/10.1200/CCI-25-00286)
- [EvoMDT: self-evolving multi-agent system for multi-cancer clinical decision-making](https://www.researchgate.net/publication/399544683)
- [CASCADE: context-aware data-driven AI for streamlined MDT recommendations (2024)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11171258/)
- [AI in multidisciplinary tumour boards — review (iScience 2025)](https://www.cell.com/iscience/fulltext/S2589-0042(25)02343-0)
- [Retrieval-Augmented Therapy Suggestion for Molecular Tumour Boards (JMIR 2025)](https://www.jmir.org/2025/1/e64364)
- [Expert-Guided LLMs for CDS in precision oncology — Lammert et al. (JCO PO 2024)](https://ascopubs.org/doi/10.1200/PO.24.00478)
- [Multi-timepoint SEER HCC survival nomogram, C-index 0.73–0.76 (Sci Rep 2026)](https://www.nature.com/articles/s41598-026-48480-9)
- [DeepSurv/NMTLR HCC survival models on SEER, C-index ~0.73 (Sci Rep 2024)](https://www.nature.com/articles/s41598-024-63531-9)
- [Multimodal deep learning for HCC survival, C-index 0.82 (npj Digital Medicine 2026)](https://www.nature.com/articles/s41746-026-03027-0)
