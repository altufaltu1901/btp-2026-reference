# Project Overview — AI Tumour Board (plain-English guide)

_Written 2026-08-28. This is a reading guide, not code. It explains the two PDFs now in this
folder, what the project is, how it works, what has already been built, and what "beating the
previous work" actually means._

Files in this folder:

| File | What it is |
|---|---|
| `BTP_report.pdf` | **Our side.** The B.Tech Project report by Aayush Mishra (IIIT Delhi, roll 2022011, advisor Dr. Jainendra Shukla). Describes a system called **Samavet AI**. This is the "previous work" we are continuing. |
| `s43018-025-00991-6.pdf` | **The paper to beat.** Ferber et al., "Development and validation of an autonomous artificial intelligence agent for clinical decision-making in oncology", *Nature Cancer*, 2025. A well-known result that our system is compared against. |

---

## 1. The one-paragraph summary

Cancer treatment decisions are normally made in a **tumour board** — a meeting where an
oncologist, radiologist, pathologist, surgeon and liver specialist look at one patient's full
record together and agree on a treatment plan. These meetings are slow, need many senior
doctors in one room, and often don't happen in smaller hospitals. The project builds an **AI
system that imitates a tumour board**: you feed in a patient's data (lab reports, scans,
biopsy, treatment history), and the system produces a structured, explained summary plus a
suggested treatment plan, which a real doctor then reviews. The focus disease is **liver
cancer (HCC — hepatocellular carcinoma)**. The Ferber paper did something similar for
gastrointestinal cancers and got strong numbers; our goal is to build a more complete,
deployable, better-evaluated version and show it does at least as well.

---

## 2. Background: what is a tumour board and why automate it

- A **tumour board** (a.k.a. multidisciplinary team / MDT meeting) is where specialists jointly
  review a cancer case and decide staging + treatment.
- Proven to improve survival, staging accuracy and guideline-compliant care.
- Problems: needs many specialists synchronously, lots of prep, doesn't scale, rare in
  low-resource hospitals, and modern cancer data is huge and multimodal (labs over time, CT/MRI,
  pathology, molecular markers, treatment timeline).
- So there is a real need for software that can **aggregate multimodal patient data and produce
  tumour-board-style reasoning** that a clinician can check.

Key medical terms you'll keep seeing:

| Term | Meaning |
|---|---|
| HCC | Hepatocellular carcinoma = the most common primary liver cancer |
| BCLC | Barcelona Clinic Liver Cancer staging — the main staging system for HCC; decides treatment |
| Child-Pugh, MELD, MELD-Na, ALBI | Scores for how well the liver is working; computed from lab values |
| ECOG | Performance status 0–4: how well the patient can function day-to-day |
| LI-RADS | Standardised way radiologists score how likely a liver lesion is cancer |
| PVTT | Portal vein tumour thrombosis — tumour growing into the main liver vein; changes treatment |
| RECIST / mRECIST | Rules for measuring whether a tumour grew or shrank between two scans |
| MSI / MSS | Microsatellite instability status — a genetic property that predicts immunotherapy response |
| KRAS / BRAF | Genes whose mutations change which drugs will work |
| INASL | Indian National Association for the Study of the Liver — the guideline set our system follows |
| OncoKB | A database that maps a genetic mutation → approved/experimental drugs |
| RAG | Retrieval-Augmented Generation: before answering, the AI looks up relevant guideline text and is forced to use it, which reduces made-up answers |
| Agent | An LLM that can decide to call "tools" (search, calculator, a vision model) and use the results |
| ReAct | A prompting style where the model alternates "think" and "act (use a tool)" steps |

---

## 3. The paper we are trying to beat — Ferber et al., Nature Cancer 2025

### What they built
A **single AI agent** = GPT-4 plus a toolbox. GPT-4 decides on its own which tools to call for a
given patient, in what order, then writes the final answer.

Tools available to it:
- **Pathology AI**: vision-transformer models that read histopathology slides and predict MSI vs
  MSS, and KRAS / BRAF mutation status.
- **Radiology AI**: MedSAM (segments/measures a tumour on a CT/MRI slice) and GPT-4 Vision
  (writes a radiology report from an image).
- **Calculator** (Python) — e.g. to compute % tumour size change between two scans.
- **Web search**: Google and PubMed.
- **OncoKB** database lookup for mutation → drug.
- **Knowledge base / RAG**: ~6,800 curated documents from 6 official sources (MDCalc, UpToDate,
  MEDITRON, ASCO, ESMO, Onkopedia guidelines). Built with `text-embedding-3-large`, a Chroma
  vector DB, Cohere reranking, orchestrated with DSPy.

### How they tested it
- **20 fictional but realistic multimodal patient cases** (colorectal, pancreatic,
  cholangiocellular, hepatocellular cancers), each with imaging + genetics + history and a
  multi-part question.
- **4 oncologists** scored the outputs, blinded, majority vote, worst-case on ties.

### Their headline results

| Metric | Result |
|---|---|
| Picked the right tools | **87.5%** (56/64 required tool calls) |
| Statements factually correct | **91.0%** (223/245) |
| Statements harmful | 2.4% (6/245) |
| Citations actually supporting the claim | **75.5%** (194/257) |
| Completeness (covered what experts expected) | **87.2%** (95/109) |
| Completeness — GPT-4 *without* tools/RAG | only **30.3%** (33/109) |
| Helpfulness (sub-questions answered) | 94.0% (63/67) |

They also tried open models (Llama-3 70B, Mixtral 8x7B) — both were bad at reliably calling tools.

### What they admit is missing (this is our opportunity)
1. **Tiny evaluation** — only 20 cases, 1 run each. They call it a "proof of concept".
2. **Single-turn only** — no back-and-forth, no human-in-the-loop refinement.
3. **GPT-4 in the cloud** — sending patient data to OpenAI is a legal blocker (GDPR/HIPAA). They
   want local/open models but didn't deliver that.
4. **One generic agent**, not a real multidisciplinary board with separate specialist roles.
5. Only single 2-D image slices; no 3-D scans; no patient history fed to the imaging step.
6. Not deployed, not integrated into any hospital system.
7. Code: `github.com/Dyke-F/LLM_RAG_Agent`.

---

## 4. The previous work on our side — "Samavet AI" (the BTP report)

Same idea, but pushed toward a **real, deployable product for liver cancer**, and structured as
a **multi-agent tumour board** rather than one agent.

### What was built (concrete)

**Frontend** — React + Tailwind dashboard:
- Patient Entity Manager: create / edit / delete cases (UUID per case).
- Upload DICOM images, pathology PDFs, JSON.
- **PDF → structured JSON pipeline**: upload a hospital report, system extracts a schema-compliant
  JSON, shows a validation report (missing / uncertain fields), user edits and approves before save.
- Dynamic "completeness checklist": tells the clinician which fields are still needed for BCLC /
  Child-Pugh staging.
- Charts (Recharts) for lab trends (AFP, bilirubin, INR…).
- Chat panel to ask the agents follow-up questions (human-in-the-loop).

**Backend** — Python (FastAPI) + PostgreSQL + object storage (S3):
- Per-domain JSON schema validators (labs, radiology, notes…).
- DICOM preprocessing (modality/phase detection).
- One endpoint per agent (e.g. `/invoke/radiology-agent`).
- **Security**: Google OAuth 2.0 login, JWT sessions, role-based access control (normal user vs
  master admin), audit logs, encryption at rest. Deployed on institutional servers with secure
  external access for clinicians.
- **Caching**: once a case is saved, agent outputs are generated once and stored, so re-opening a
  patient doesn't re-run every LLM call.

**The patient schema** (the backbone of the whole system):
- Sections: Demographics, Clinical presentation, Lab data (baseline + longitudinal + derived
  scores), Radiology (lesion-level: size, LI-RADS, PVTT, metastasis), Pathology (grade, vascular
  invasion, molecular markers), Treatment history, Tumour board notes, Ground-truth annotations.
- Key rule: **`null` = "not assessed"** vs an explicit value like `"none"` = "checked, absent".
  This distinction matters for staging ("absence of evidence ≠ evidence of absence").
- Designed with real clinicians (Dr. Rohit, Dr. Nipun, Dr. Uwais at PGIMER Chandigarh); aligned
  loosely with FHIR / OMOP / mCODE standards.

**The agents** (modular, plug-and-play):
| Agent | Job |
|---|---|
| Strategy Planner | Decides which modalities/agents to run and in what order |
| Lab Summarizer | Reads lab time-series, flags abnormal values, interprets trends |
| Radiology Interpreter | Summarises scans in LI-RADS / mRECIST terms (uses GPT-V / MedSAM-type models) |
| Pathology Agent | Extracts biopsy details — differentiation, vascular invasion, markers |
| Specialist Synthesizers | Oncology / hepatology "voices" that propose recommendations |
| Orchestration Agent | Merges all agent outputs into one coherent tumour-board report |
| Evaluation Agent | Checks coherence, guideline adherence, flags hallucinations |

**Knowledge-graph reasoning engine** (the "extended phase" upgrade):
- Turns the structured patient data into a graph (nodes = symptoms/labs/findings, edges =
  relationships / staging criteria).
- Uses ReAct-style planning + **rule-based decision routing** (e.g. hard-coded BCLC logic) to
  decide which agents actually need to run → fewer LLM calls, more explainable.
- Hybrid: symbolic rules + LLM reasoning.
- Recommendations are **grounded in INASL guidelines** to cut hallucination.

**Derived calculations the system does deterministically**: Child-Pugh, MELD, MELD-Na, ALBI,
Tumour Burden Score, LI-RADS overall.

### End-to-end pipeline
1. Data in (manual entry or PDF parsing) → 2. Schema validation → 3. Agents run on relevant
modalities → 4. Outputs stored in a standard intermediate format → 5. Knowledge-graph engine
routes + aggregates → 6. Tumour Board Composer writes the final report (Clinical Summary /
Diagnostic Findings / Proposed Treatment Strategy / Caveats) → 7. Clinician reviews and edits.
Every step is logged.

### How it was tested
- **Only 4 de-identified real HCC cases** so far.
- **~90% agreement** with the real clinicians' decisions.
- The ~10% disagreements were mostly **non-medical** reasons the system can't see: money /
  affordability, patient preference, hospital resource limits.
- Strengths observed: correct score computation, guideline-consistent plans, good multimodal
  synthesis, explainable structured output.
- A full **evaluation framework was designed** (calculation accuracy, hallucination detection,
  guideline adherence, clinical agreement, completeness, clarity, safety) but **not yet run at
  scale**.

### What the BTP report says is still to do
- Scale evaluation to **50–100 annotated cases**, benchmark across institutions.
- Deploy as a live **co-pilot in real tumour board meetings** (PGIMER Chandigarh).
- Add **non-clinical factors** (cost, access) to the schema and reasoning.
- Move to **local / open models** (Gemma, Sarvam AI) to remove cloud-privacy problems.
- Extend to other cancers (colorectal, breast, lung).

---

## 5. How the two relate

| | Ferber et al. (to beat) | Samavet AI (our previous work) |
|---|---|---|
| Structure | One GPT-4 agent + tools | Multi-agent "tumour board" + orchestrator |
| Disease focus | GI cancers (mixed) | Liver cancer (HCC) specifically |
| Guidelines | ASCO / ESMO / Onkopedia (Western) | INASL (Indian) |
| Reasoning | LLM + RAG | LLM + RAG + knowledge graph + hard rules (e.g. BCLC) |
| Interaction | Single-turn, autonomous | Human-in-the-loop chat, clinician review step |
| Model | GPT-4 (cloud) | LLM-based, plan to localise |
| Deployment | Experimental notebook | Deployed web app, auth, RBAC, audit, encryption |
| Data entry | Hand-crafted vignettes | PDF→schema pipeline + dashboard forms |
| Evaluation size | 20 cases, 4 expert raters, blinded | 4 cases so far; framework designed for 50–100 |
| Evaluation rigour | **Strong, published, quantified** | **Weak so far — this is the gap** |

Samavet AI already **cites Ferber et al.** as its main inspiration ("GPT for Oncology" section).
So Ferber is simultaneously the design template and the scoreboard.

---

## 6. What "beating the previous work" concretely means

Ferber's evaluation is the bar. To claim we beat it, we need a **comparable or stronger
evaluation** with numbers at least as good, ideally on a harder setting. Concrete targets:

1. **Bigger, rigorous benchmark.** Move from 4 cases to at least the 50–100 planned, with
   multiple blinded clinician raters and majority voting (copy Ferber's methodology so the
   comparison is fair).
2. **Match or beat the headline metrics** on that benchmark:
   - Clinical correctness / agreement ≥ ~91%
   - Completeness ≥ ~87%
   - Citation / guideline-grounding accuracy > ~75%
   - Harmful-response rate ≤ ~2.4% (ideally lower — safety is the strongest selling point)
3. **Win on the axes Ferber explicitly couldn't deliver:**
   - Runs on a **local / open model** (no patient data leaves the hospital).
   - **Multi-turn, human-in-the-loop** refinement (already partly built).
   - **True multidisciplinary** decomposition, not one generic agent.
   - **Actually deployed** and usable in a real workflow.
   - Handles **incomplete real-world data** gracefully (Ferber used clean vignettes).
4. **Show the knowledge-graph + rule routing** measurably helps — e.g. fewer LLM calls, higher
   staging accuracy, better explainability — vs a plain agent baseline.
5. Ideally run **Ferber's own 20 cases (their GitHub repo is public)** through Samavet AI as a
   head-to-head, plus our own HCC benchmark.

---

## 7. Likely focus for the next phase of work (where code will plug in)

Based on both documents, the open work is:
- Build / expand the **annotated HCC case dataset** and the **automated evaluation harness**
  (the framework is designed but not implemented at scale).
- Swap in and evaluate **local models**; measure quality drop vs GPT-4.
- Strengthen the **radiology and pathology tool** components (Ferber notes these need independent
  validation; single-slice imaging is a known weakness).
- Add **non-clinical / cost factors** to schema + reasoning to close the ~10% disagreement gap.
- Run the **head-to-head benchmark** and write it up.

When the code arrives, map it against Section 4 ("What was built") to see which parts are the
dashboard, the backend, the agents, the knowledge-graph engine, and the evaluation harness.

---

## 8. Quick glossary of system/AI terms

- **LLM** — large language model (e.g. GPT-4).
- **Agent** — an LLM that can call tools and act on the results, in a loop.
- **Tool** — a function the agent can invoke: a calculator, a web search, a segmentation model.
- **RAG** — retrieve relevant guideline text first, force the model to answer from it.
- **Vector database / embeddings** — guideline text stored as numbers so the system can find the
  passages most similar to a query.
- **Reranking** — a second, smarter pass that re-orders retrieved passages by real relevance.
- **Knowledge graph** — patient facts stored as nodes + relationships, so rules can run over them.
- **Orchestration** — the layer that decides which agent runs when and merges their outputs.
- **Human-in-the-loop (HITL)** — the clinician stays in control and reviews/edits every output.
- **Schema** — the fixed JSON shape all patient data is forced into, so every component reads it
  the same way.
