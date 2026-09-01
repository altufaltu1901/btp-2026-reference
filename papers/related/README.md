# Related papers (the 2-3 most relevant beyond the core three)

Picked from `Related-Work-Scan.md` — not the whole landscape, just the ones that
most directly shape the BTP.

### MTBBench — `MTBBench_arXiv-2511.20490.pdf`
Vasilev et al., ETH/EPFL/HUG, arXiv:2511.20490 (2025).
*"A Multimodal Sequential Clinical Decision-Making Benchmark in Oncology."*
The benchmark **closest to our exact task** — multimodal + longitudinal + multi-agent
molecular-tumour-board decisions, physician-validated. Their own agentic framework
only gained +9-11% on multimodal/longitudinal reasoning → lots of headroom.
Code + data: https://github.com/bunnelab/MTBBench ·
https://huggingface.co/datasets/EeshaanJain/MTBBench

### Stanford Thoracic Tumor Board multi-agent — `Stanford-Thoracic-Tumor-Board-MultiAgent_arXiv-2604.12161.pdf`
Ellis-Caleo, Keyes, Shah, Neal et al., Stanford, arXiv:2604.12161 (2026).
*"Development, Evaluation, and Deployment of a Multi-Agent System for Thoracic Tumor Board."*
The **template for the deployment + evaluation half** of our work: physician
gold-standard summaries, fact-based scoring rubric, validated LLM-as-judge,
post-deployment monitoring. Actually deployed in a live tumour board.

### LLMs for HCC treatment recommendations (nationwide registry) — `LLMs-for-HCC-treatment-recs_nationwide-registry_PLOS-Medicine-2026.pdf`
Yang, Lee, Jang, Sung, Han. PLOS Medicine 23(1):e1004855 (2026). Open access.
*"Evaluating the clinical utility of LLMs for hepatocellular carcinoma treatment
recommendations: A nationwide retrospective registry study."*
**Directly our disease + task.** Large Korean registry cohort; finding: LLM–physician
concordance is **associated with better survival in early-stage HCC, not advanced**.
The key citation for "why concordance is a meaningful endpoint" in HCC specifically.

### EvoMDT — `EvoMDT_npj-Digital-Medicine-2026_s41746-025-02304-8.pdf`
Liu, Hu, Huang, Niu, Zhang et al. npj Digital Medicine 9:124 (2026).
doi:10.1038/s41746-025-02304-8. Open access (CC BY-NC-ND).
*"EvoMDT: a self-evolving multi-agent system for structured clinical decision-making in multi-cancer."*
**Very close to our architecture** — domain-specific agents over lesion-level clinical
data + structured guideline RAG + a consensus protocol producing traceable,
evidence-linked recs. Adds a **self-evolution loop** (updates prompts, consensus
weights, retrieval scope from expert feedback + outcome signals). Evaluated on 6
public oncology QA benchmarks + 4 real-world datasets — **one of which is HCC**
(also breast, lung adeno, lymphoma), plus single-blind physician rating
(ROUGE / BERTScore / safety checks + clinical-appropriateness Likert).
Data: private (corresponding-author request). Code: promised at
https://github.com/KesselZ/EvoMDT — **not public yet** (404 as of 2026-09-02).
Supplement + notes: `data/EvoMDT/`.

---
Other strong ones we didn't pull (all in Related-Work-Scan.md with links):
Frontiers 2026 review (LLMs → multi-agent), JCO CCI 2026 Nazha multiagent guideline
system, MDPI Cancers 2026 (50 HCC + 50 CCA concordance — MDPI blocks scripted
download), "AI paradox in precision oncology" ASCO 2026 (Indian MTB, abstract only).
