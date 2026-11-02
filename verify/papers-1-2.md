# Verification of papers 1 and 2 (checked 2026-11-02)

## Bottom line
- The "88-item Script Concordance Test" matches Paper 2 (ASCO 2026 abstract e16015). It is quoted in the abstract.
- "100 literature-based cases": not confirmed for either paper. Paper 2's abstract does not mention cases or literature. Paper 1 could not be read at all.
- **Paper 1 could not be accessed, so none of the requested facts for it are reported.**
- For Paper 2, only the abstract was readable. Many requested fields (dataset origin, country, language, SCT construction, availability) are not in the abstract. They are listed as "not stated in the abstract".

## Paper 1: ScienceDirect PII S1590865826001714 (Digestive and Liver Disease)
**Status: could not access. No content was read.**

What was tried, all failed:
- WebFetch of the ScienceDirect URL: HTTP 403 Forbidden.
- Playwright browser, two attempts: Cloudflare challenge page ("Just a moment...", HTTP 403).
- Crossref: journal ISSN 1590-8658 listing, 1111 works indexed from 2025-06. No record has alternative-id S1590865826001714.
- Crossref DOI lookup of 10.1016/S1590-8658(26)00171-4: "Resource not found". doi.org returned 404 for the same DOI.
- PubMed E-utilities (journal plus LLM, hepatocellular, BCLC, script concordance): 0 hits.
- Europe PMC searches (script concordance + LLM; Dig Liver Dis + language model; hepatocellular + BCLC + literature-based): no matching article.
- Web search on the PII and on topic keywords: no match.

Conclusion: the article is not in Crossref, PubMed or Europe PMC as of today, and the publisher page is bot-blocked. The full citation, DOI, task, dataset, ground truth, systems, metrics, results, data availability statement, BCLC version and supplement list are all **could not access**. A human needs to open the page in a normal browser, or give me the PDF or title.

Unverified, do not rely on it: a Livers (MDPI) paper, "Concordance Between Large Language Models and Multidisciplinary Tumor Board Recommendations in Treatment Allocation for Hepatocellular Carcinoma" (DOI 10.3390/livers6050104), turned up in searches. It is not in Digestive and Liver Disease, and I did not read it. I have no evidence that it is Paper 1.

Whether it matches the "100 literature-based cases" description: unknown.

## Paper 2: ASCO 2026 abstract e16015
**Source accessed:** Crossref API record for 10.1200/JCO.2026.44.16_suppl.e16015 (the ascopubs.org page itself returned HTTP 403). Only the abstract text and metadata were available. The full poster was not accessed.

### Citation (from Crossref metadata)
- Authors in order: Ernest Saenz, Santiago Rodriguez-Mora, Jimmy Daza, Santiago Arenas, Maria Fernanda Saavedra-Chacon, Yeinis Paola Espinoza-Herrera, Juan Turnes, Andres Gomez-Aldana, Andreas Teufel.
- Title: "Tumor board-based multi-agent LLMs with guideline retrieval and consensus deliberation: Implications for hepatocellular carcinoma clinical reasoning." (the hyphen character was garbled in the Crossref response)
- Journal: Journal of Clinical Oncology, vol 44, issue 16_suppl, published print 2026-06-01, article number e16015.
- DOI: 10.1200/JCO.2026.44.16_suppl.e16015.
- Funder field: "None."
- Affiliations: Heidelberg University Mannheim (Germany), University of Cincinnati (USA), CES Clinic and Antioquia University (Medellin, Colombia), Pontevedra (Spain).

### Task
Answer items of an HCC Script Concordance Test, comparing single-call LLM inference with a multi-agent consensus system plus guideline RAG ("HCC clinical reasoning tasks").

### Dataset
- Stated in the abstract: "a validated 88-item HCC Script Concordance Test (SCT) validated by 3 hepatologists with more than 10 years of experience".
- Not stated in the abstract: how the items were built, whether cases are literature-derived, real or synthetic, country, language, inclusion criteria, and whether the panel made the reference answer distributions. The 3 hepatologists are described only as validators.

### Ground truth
Not explicitly stated. The abstract scores "accuracy" against the SCT and says the SCT was validated by 3 hepatologists. I cannot say whether the key is panel-modal, panel-aggregate or other.

### Systems and baselines
- Multi-agent system: three specialist agents (hepatology, oncology, radiology personas) plus a supervisor agent.
- RAG over a curated guideline corpus (AASLD, EASL, ESMO, BSG) in a vector store, top-5 passages per case.
- Baseline: single-call inference.
- Models: a top large model (gpt-5.2) and a top small model (qwen3_vl_30b_thinking), "selected from an initial benchmarking of 10 LLMs".
- Human comparators (residents, specialists): none mentioned.
- Exact model versions and settings, and the prompts: not stated.

### Metrics and results
- Accuracy, run-to-run consistency, Friedman test, Wilcoxon test, McNemar test, 95% CI.
- Baseline single-call accuracy 61.8% to 74.9% (Friedman p = 0.44).
- Large vs small model classes: 69.9% vs 67.4% (Wilcoxon p = 0.26).
- gpt-5.2: 74.2% to 80.3% (+6.1 pp; 95% CI 75.1-85.4%; p = 0.020). Its table McNemar p = 0.19.
- qwen3-vl-30b: 55.4% to 64.8% (+9.4; 95% CI 57.0-72.5%; p = 0.029). McNemar p = 0.004: 17 items converted to correct, 3 errors introduced.
- Consistency: gpt-5.2 95.8% solo and 93.0% consensus. Qwen 54.9% solo and 73.2% consensus.
- Table baseline 95% CIs: gpt-5.2 68.1-79.8%, qwen 48.8-62.0%.
- Note: the text says gpt-5.2 consistency "93%", which matches the table consensus value. The solo value 95.8% means consistency fell slightly for gpt-5.2.

### Availability
The abstract has no data availability statement. No GitHub or Zenodo link appears in the Crossref record. The abstract does not say whether the SCT items, panel answer distributions, prompts or code are released. The full ASCO page and any supplement could not be opened, so availability is unknown. It is not shown to be released.

### BCLC
BCLC is not mentioned in the abstract. Guideline corpus listed: AASLD, EASL, ESMO, BSG. No BCLC version is stated.

### Match to the professor's description
- "88-item Script Concordance Test": matches, verbatim.
- "100 literature-based cases": not found in this abstract.

## Evidence (quote, then source)
1. "Performance was benchmarked against baseline single-call inference on a validated 88-item HCC Script Concordance Test (SCT) validated by 3 hepatologists with more than 10 years of experience." (Crossref abstract, Methods, DOI 10.1200/JCO.2026.44.16_suppl.e16015)
2. "We developed a multi-agent system comprising three specialist agents (hepatology, oncology, radiology personas) and a supervisor agent." (same, Methods)
3. "The system used retrieval-augmented generation (RAG) over a curated guideline corpus (AASLD, EASL, ESMO, BSG) implemented in a vector store, retrieving the top-5 passages per case." (same, Methods)
4. "We evaluated a top large model (gpt-5.2) and a top small model (qwen3_vl_30b_thinking) selected from an initial benchmarking of 10 LLMs." (same, Methods)
5. "Baseline single-call accuracy ranged from 61.8% to 74.9% with no significant heterogeneity (Friedman p = 0.44). Large versus small model classes showed no significant difference (69.9% vs 67.4%; Wilcoxon p = 0.26)." (same, Results)
6. "For the large model (gpt-5.2), accuracy improved from 74.2% to 80.3% (+6.1 percentage points; 95% CI, 75.1-85.4%; p = 0.020). For the small model (qwen3-vl-30b), accuracy improved from 55.4% to 64.8% (+9.4; 95% CI, 57.0-72.5%; p = 0.029)." (same, Results)
7. "item-level analysis showed 17 previously incorrect items converted to correct versus only 3 errors introduced (McNemar p = 0.004). Run-to-run consistency was maintained or improved (gpt-5.2: 93%; qwen3-vl-30b: 55% to 73%)." (same, Results)
8. Table row "Consistency: 95.8% | 93.0% | 54.9% | 73.2%" for GPT-5.2 Solo, GPT-5.2 Consensus, Qwen Solo, Qwen Consensus. (same, "Summary comparison table")
9. "Prospective validation should assess impact on tumor board efficiency, guideline adherence rates, and patient outcomes." (same, Conclusions)
10. Funder name: "None." (Crossref metadata, `funder`)
11. Access failures: "The server returned HTTP 403 Forbidden." (WebFetch result for both URLs), and browser title "Just a moment..." with HTTP 403 (Playwright, ScienceDirect).
