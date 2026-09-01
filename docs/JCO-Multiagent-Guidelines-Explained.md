# The JCO CCI multi-agent guideline system — explained

_Written 2026-08-28. Paper: Wang, Arora, Swoboda, Nazha — "Tumor Board–Inspired Multiagent
Artificial Intelligence System for Interpreting Oncology Guidelines", **JCO Clinical Cancer
Informatics, 2026**. Precursor: ASCO 2025 abstract "Virtual oncology collaborative tumor board
using multiple AI agents"._

Shorter than the HCC-STAR write-up because the paper is narrower.

---

## 0. One paragraph

This is **not** a patient tumour board. It's a **guideline question-answering system** dressed up
with a tumour-board metaphor. You ask it a question about what an oncology guideline says
("what's the first-line therapy for X?"), and a team of AI agents finds the right guideline,
pulls the answer out of the PDF (including from **tables and figures**, not just text), and
writes a grounded answer. On 100 test questions it hit **90% answer accuracy**, versus ~48–67%
for GPT-4o / Claude 3.7 / Gemini 2.5 / DeepSeek-R1 / ASCO's own official Guidelines Assistant.
For us it matters as the **best published example of doing the guideline-grounding layer properly**
— which is one component of Samavet, not the whole thing.

---

## 1. What they built (architecture)

A **3-role multi-agent pipeline** over a fixed library of **34 ASCO guidelines** (published
Jan 2021 – Dec 2024):

| Agent | Job |
|---|---|
| **Coordinator Agent** | Reads the question, picks **which** of the 34 guidelines is relevant (a routing step). |
| **PDF Viewer / "Tumour Board" Agents** | Open that guideline PDF and extract the answer from **text, tables, and figures** — i.e. they actually parse the decision flowcharts and dosing tables, not just prose. |
| **Reviewer Agent** | Synthesises the extracted pieces into one final answer. |

Built on top of general LLMs (they benchmark against GPT-4o, Claude 3.7, Gemini 2.5 Flash,
DeepSeek-R1 — the system itself is an orchestration layer, not a fine-tuned model). No training.

**The key idea:** route to the right document first, then use multimodal PDF parsing (tables +
figures) instead of dumping raw text into an LLM.

---

## 2. THE INPUT

- **A single natural-language question** about guideline content.
  Examples of the kind of thing: *"What is the recommended first-line systemic therapy for
  metastatic HER2-positive breast cancer?"*, *"When is adjuvant chemotherapy indicated after
  resection of stage II colon cancer?"*
- **That's it.** No patient data. No labs, no imaging, no history. Just the question.
- The system has standing access to the 34 ASCO guideline PDFs (with their tables and figures).

---

## 3. THE OUTPUT

- **A written answer to the guideline question**, grounded in the specific guideline it routed to
  (and, per the tumour-board framing, traceable to the source section/table/figure).
- No staging, no patient-specific treatment plan, no prognosis. It tells you *what the guideline
  says*, not *what to do for this patient*.

---

## 4. Results

| | This system | GPT-4o | Claude 3.7 | Gemini 2.5 | DeepSeek-R1 | ASCO Guidelines Assistant |
|---|---|---|---|---|---|---|
| Answer accuracy (100 Qs) | **90%** | 48% | 49% | 50% | 58% | 67% |
| Correct guideline selected | **94%** | — | — | — | — | — |

Evaluated by expert review against the guideline content. The margin over the base models — and
over ASCO's own official tool — is the headline.

**Stated scope limits:** ASCO guidelines only; guideline-QA only (not patient management); pilot
study.

---

## 5. How it compares to our project (Samavet AI)

| | JCO CCI system | Samavet AI (ours) |
|---|---|---|
| What it does | answers "what does the guideline say?" | takes a full patient case → stage + treatment plan + board report |
| Input | one text question, no patient data | structured multimodal patient record (labs, imaging, pathology, history) + PDFs |
| Output | a grounded guideline answer | consolidated tumour-board report with reasoning, scores, caveats |
| Guidelines | 34 ASCO (Western) | INASL (Indian), for HCC |
| Hard part it solves | retrieving + reading guidelines accurately (incl. tables/figures) | applying guidelines to a messy specific patient |
| Method | 3-agent orchestration, no training | multi-agent + KG + rules + RAG + tools |

**It's essentially a much better version of ONE layer of our system** — the
guideline-grounding / RAG layer. It doesn't do the reasoning-over-a-patient part at all, which is
the hard bit and where HCC-STAR competes.

### What's worth stealing

1. **Route to the right document first.** A Coordinator/router agent that picks the relevant
   guideline (or the relevant *section* of INASL) before retrieval beats blind vector search.
2. **Parse tables and figures, not just text.** Oncology guidelines are full of decision
   flowcharts and dosing tables — **BCLC staging is literally a flowchart**, and the INASL HCC
   algorithm is a decision tree. A naive RAG that only embeds prose will miss exactly the parts
   that matter. Using a vision-capable model to read the flowchart is a concrete upgrade for our
   grounding layer.
3. **A dedicated Reviewer/synthesiser agent** as the last step — we already have an Orchestrator,
   but making it explicitly a guideline-faithfulness checker is a small, cheap improvement.

### What it tells us strategically

- **Multi-agent + structured guideline handling beats raw LLMs by ~40 points** on guideline
  questions (90% vs ~50%). Strong evidence that investing in the grounding layer pays off — not
  just the reasoning layer.
- It also **beat ASCO's own official RAG tool (67%)** — so "official vendor RAG" is not a ceiling;
  a well-built agentic pipeline can do much better.
- Good citation for our related-work section as the **closest published tumour-board-inspired
  multi-agent system**, while making clear our scope (full multimodal patient case) is broader and
  harder.

---

## Sources
- [JCO CCI 2026 paper (abstract)](https://ascopubs.org/doi/10.1200/CCI-25-00286)
- [PubMed record](https://pubmed.ncbi.nlm.nih.gov/41499718/)
- [Precursor: ASCO 2025 abstract — virtual collaborative tumour board with multiple AI agents](https://ascopubs.org/doi/10.1200/JCO.2025.43.16_suppl.1563)
- [ASCO Guidelines Assistant (the official tool it beat)](https://www.asco.org/practice-patients/guidelines/assistant)
