# Verification of five LLM-for-liver/HPB tumour-board studies

Verified 2026-11-02. Method: full-text XML from Europe PMC (Palzer PMC13359904, Masthoff PMC13263330, Jo PMC13585308, Tsinghua PMC12745982) and the PLOS Medicine manuscript XML (journals.plos.org, `type=manuscript`). The publisher pages could not be read directly: MDPI returned HTTP 403, nature.com redirected to a login/cookie handshake, jmir.org returned an empty body. The Europe PMC text is the publisher's deposited full text. Supplementary files (docx/xlsx/pdf) were **not opened** for any study; only their titles in the article are reported.

Corrections to the brief, in short:
- Tsinghua paper is **Patterns 2025**, not 2026. The "six agents" are **six separate LLMs**, not a six-agent team.
- Jo et al. covers **hepatopancreatobiliary** cancers (biliary tract 71, pancreas 33, ampulla 3). It has no HCC cohort.
- Masthoff: the abstract's "84.0% (o3)" BCLC figure does not match the Results (see study 2).
- Yang: the Results text says BCLC-A is 43.2%. Table 2 says 29.9%.

---

## 1. Palzer et al., Cancers 2026

**Citation.** Palzer J, Genchev A, Belger E, Weigle CA, Wiemann BA, Tessmer P, Murad M, Berres ML, Meister FA, Amygdalos I, Bruners P, Truhn D, Mohamed AA, Vondran FWR, Oldhafer F, Beetz O. Large Language Models for Multidisciplinary Tumor Board Decision-Making in Primary Liver Tumors: A Retrospective Single-Center Study. *Cancers (Basel)* 2026;18(13):2175. doi:10.3390/cancers18132175. PMC13359904, PMID 42449717. Published 7 July 2026. CC BY.

**Task.** Concordance between the multidisciplinary tumour board (MTB) decision and LLM treatment recommendations for newly diagnosed cholangiocellular carcinoma (CCA) and HCC. The authors state concordance "was not interpreted as a measure of clinical correctness".

**Cases.** 100 (50 CCA + 50 HCC). Source: MTB protocols, University Hospital RWTH Aachen, Germany, 2022-2023. 1456 protocols were screened, 754 were primary liver tumours. Exclusions were recurrent disease and extensive prior multimodal therapy. HCC BCLC mix: A 19, B 15, C 11, D 5, 0 in stage 0 (Table 2).

**Input to model.** Pseudonymised MTB registration form content, with the MTB decision removed. It was limited to "patient age, ECOG-PS, and disease-specific clinical data, including tumor characteristics, staging, relevant diagnostic findings, as well as laboratory parameters". Prompts were in German. Each case was run once per model, sequentially in one chat session, in default mode with no reasoning feature.

**Ground truth and raters.** The real MTB recommendation is the comparator, "rather than an absolute gold standard". Mapping of LLM output to categories: "reviewed by study authors for categorization but not subjected to an independent qualitative assessment". The number of raters is not stated. No inter-rater statistic was reported.

**Models, baselines, guideline version.** ChatGPT GPT-5.4 (release 5 March 2026) and Claude Sonnet 4.6 (release 17 February 2026). No other baseline. No guideline was supplied in the prompt as far as the text says, and no BCLC year is stated. BCLC is cited to ref 12, "BCLC 2022 update: Important advances, but missing external beam radiotherapy". EASL 2018 and AASLD 2023 appear as refs 13-14.

**Metrics and headline results.** Exact concordance, Cohen's kappa, Spearman rho.
| | ChatGPT | Claude |
|---|---|---|
| CCA exact agreement | 40/50 (80%), kappa 0.688 (SE 0.083), rho 0.725 (95% CI 0.553-0.837) | 28/50 (56%), kappa 0.425, rho 0.417 (p=0.003) |
| HCC exact agreement | 33/50 (66%), kappa 0.604 (SE 0.076), rho 0.484 (CI 0.230-0.677) | 19/50 (38%), kappa 0.314 (SE 0.069), rho 0.086 (CI -0.205 to 0.363, p=0.551) |

HCC pattern: ChatGPT assigned local therapy 17 times against 6 by the MTB. Claude recommended supplementary diagnostics 17 times in HCC and 22 times in CCA.

**Availability.**
- Data Availability Statement, verbatim: "All data generated or analyzed during this study are included in this article."
- Supplementary Materials list, verbatim: "File S1: Complete prompt template; Table S1. Cross-table of MTB and ChatGPT recommendations for CCA; Table S2. ... Claude ... CCA; Table S3. ... ChatGPT ... HCC; Table S4. ... Claude ... HCC." Download link given: https://www.mdpi.com/article/10.3390/cancers18132175/s1 (not opened).
- No code. Licence: article CC BY. No separate licence is stated for the supplement.

**Evidence.**
- "After exclusions, 50 CCA and 50 HCC cases were analyzed." (Abstract)
- "From 1456 MTB protocols, 754 discussed such tumor entities." (2.2 Data Set Acquisition)
- "MTB protocols discussed within the MTB at the University Hospital RWTH Aachen between 2022 and 2023" (2.2)
- "The LLM input included all pseudonymized clinical information documented in the registration forms, while the final therapeutic decision of the MTB was deliberately excluded to prevent potential bias." (2.1)
- "All prompts were administered in German. The exact prompt is provided in the Supplementary Material (File S1). Each patient case was evaluated once per model" (2.1)
- "ChatGPT GPT-5.4 (Release date: 5 March 2026 ...) and Claude Sonnet 4.6 (Release date: 17 February 2026 ...)" (2.1)
- "All LLMs were used in default mode, e.g., features such as "reasoning" were not employed." (2.1)
- "LLM-generated recommendations were reviewed by study authors for categorization but not subjected to an independent qualitative assessment" (2.1)
- "Concordance was not interpreted as a measure of clinical correctness." (2.1)
- "Concordance with Claude was only 38%, with fair agreement (k = 0.314, p < 0.001) and weak correlation (r = 0.086, p = 0.551)." (Abstract)
- "All data generated or analyzed during this study are included in this article." (Data Availability Statement)

---

## 2. Masthoff et al., Scientific Reports 2026

**Citation.** Masthoff M, Zipser A, Praktiknjo M, Trebicka J, Morgul H, Pascher A, Pohler G, Kohler M, Schindler P. Performance of GPT-based large language models in hepatocellular carcinoma stratification: liver function assessment, BCLC staging, and treatment recommendations. *Sci Rep* 2026;16:18342 (Europe PMC: volume 16, elocation 18342). doi:10.1038/s41598-026-56992-7. PMC13263330. Epub 12 June 2026. CC BY 4.0. (Author list is from Europe PMC's flattened XML, with diacritics lost, so check the initials.)

**Task.** Three GPT tasks per patient: (1) calculate MELD, ALBI and Child-Pugh; (2) assign BCLC stage; (3) recommend treatment. A time and cost comparison against clinical staff is also reported.

**Cases.** 106 HCC patients, consecutive, newly referred to one tertiary liver centre, University Hospital Münster, Germany, January 2021 to October 2024. 87 men (82%), median age 65 (22-86). BCLC: 0 = 4, A = 43, B = 24, C = 22, D = 13. Initial diagnosis 96, relapse 1, prior treatment more than 3 months ago 9.

**Input to model.** "the complete original clinical, radiological, and laboratory reports ... compiled into a single anonymized PDF file in German language". No structured imaging metadata, no pre-processing scripts, zero-shot. The BCLC class and the MELD/ALBI/Child-Pugh scores were not included in the report.

**Ground truth and raters.** BCLC stage was assigned by "an interdisciplinary expert panel (radiology, hepatology, surgery with each member having at least 5 years of experience in HCC management) in consensus, blinded for GPT outputs". That consensus is "the gold standard". Liver scores were computed with calculators and formulas. Treatment suggestions were graded "true, false or as acceptable alternative based on comparison to the gold standard and BCLC classification". Who graded them is not stated. Outputs were also compared retrospectively with the real tumour-board decision.

**Models, guideline version.** GPT-4, o1, o3 (run December 2024) and GPT-5.4 "thinking mode; thinking effort: standard" (April 2026). Each was run with a short prompt (SP) and a long prompt (LP). The LP says: "Follow the 2022 BCLC guidelines strictly". The SP says "Based on the newest BCLC system". The main text says "latest BCLC classification system", and ref 2 is Reig et al., "BCLC strategy ... The 2022 update". No other baseline apart from the human-staff time and cost comparison.

**Metrics and headline results.** Error rate and accuracy (chi-square with Bonferroni-corrected pairwise tests), error taxonomy, time, cost.
- Child-Pugh: all versions above 85% accuracy, mean 93.2 +/- 4.1%.
- ALBI: mean 88.2 +/- 18.7%. GPT-4 SP accuracy 40.6% (error 59.4%).
- MELD: mean 74.2 +/- 27.1%. GPT-4 SP error 88.7%. GPT-5.4 SP error 3.8%, LP 14.2%.
- BCLC staging (error SP/LP): GPT-4 53.8%/43.4%, o1 27.4%/15.1%, o3 14.2%/20.8%, 5.4 13.2%/25.5%. Mean accuracy 73.3 +/- 13.8%.
- Treatment, correct: GPT-4 SP 45.3% (+18.9% acceptable alternatives, 35.8% false). o3 LP 84.0%. 5.4 SP 69.8%, LP 72.6%. Correct plus acceptable: o1 LP 90.6%, o3 LP 89.6%, 5.4 LP 86.8%, 5.4 SP 83.0%, o3 SP 85.8%, GPT-4 SP 64.2%, GPT-4 LP 69.8%.
- GPT gave a true or acceptable answer where the tumour board was retrospectively wrong in 9.4-14.2% of cases.
- Time: staff 567.8 +/- 59.5 s. GPT-o3 SP 53.2 +/- 24.2 s, the fastest. GPT-5.4 LP 129.1 +/- 47.3 s. Cost per case $2.44 (resident) and $3.92 (attending). GPT $0.003-$0.007.

**Discrepancy to flag.** The abstract says "BCLC staging accuracy ranged from 46.2% (version 4) to 84.0% (o3)". 46.2% matches GPT-4 SP (100 - 53.8). But 84.0% is the o3 LP *treatment* correct rate. From the Results text, the best BCLC accuracy is 86.8% (5.4 SP), then 85.8% (o3 SP), then 84.9% (o1 LP). Cite the Results figures, not the abstract number.

**Availability.**
- Data availability, verbatim: "The datasets supporting the conclusions of this article are included within the article and its additional files."
- Supplementary: one file, "Supplementary Material 1", `41598_2026_56992_MOESM1_ESM.docx`, 26,271 bytes (from the XML suppdata tag). The text says supplemental tables 1-6 hold the detailed statistics. Not opened.
- Both prompts are quoted in full in the Methods. No code and no case-level data are mentioned.
- Licence: article CC BY 4.0. No separate licence is stated for the supplement.

**Evidence.**
- "Data from 106 HCC patients (82% male, median age 65 [22-86]) were compiled into anonymized integrated reports." (Abstract)
- "all consecutive patients with HCC who were newly referred to a tertiary care liver center ... between January 2021 and October 2024." (Methods, Study design)
- "compiled into a single anonymized PDF file in German language, with all patient- and physician-identifying information removed. No structured imaging metadata was included in the integrated reports." (Methods, Data collection)
- "performed by an interdisciplinary expert panel ... in consensus, blinded for GPT outputs ... Consensus data served as the gold standard" (Methods, Data collection)
- "versions 4, o1 and o3, performed in December 2024 as a well as version 5.4 (thinking mode; thinking effort: standard) performed in April 2026." (Methods, GPT data analysis)
- "Follow the 2022 BCLC guidelines strictly, incorporating key updates such as classifying single tumors as BCLC A regardless of size" (Methods, Long prompt)
- "Version o3 with LP achieved the highest proportion of correct suggestions (84.0%)" (Results, GPT for therapeutic guidance)
- "version o1 (90.6%) and o3 with LP (89.6%) with LP reached the highest agreement" (same section)
- "The datasets supporting the conclusions of this article are included within the article and its additional files." (Data availability)

---

## 3. Yang et al., PLOS Medicine 2026

**Citation.** Yang K, Lee J, Jang JW, Sung PS, Han JW. Evaluating the clinical utility of large language models for hepatocellular carcinoma treatment recommendations: A nationwide retrospective registry study. *PLoS Med* 2026;23(1):e1004855. doi:10.1371/journal.pmed.1004855. PMC12799000, PMID 41528959. CC BY 4.0. Affiliation: College of Medicine, The Catholic University of Korea, Seoul.

**Task.** Compare LLM treatment recommendations with the treatment actually given, and test whether concordance is associated with overall survival (OS), stratified by BCLC stage. Decision-tree analysis of what drives each side.

**Cases.** 13,614 treatment-naive HCC patients, Korean Primary Liver Cancer Registry, diagnosed 2008-2020. The registry is "a nationwide, hospital-based cohort". The flow is 19,774 minus 2,640 (follow-up), 2,842 (missing labs), 26 (imaging), 168 (ECOG) and 484 (treatment history), which gives 13,614.

**Input to model.** An attached CSV of "structured, guideline-level clinical variables (e.g., tumor size and number, vascular invasion, extrahepatic spread, Child-Pugh class, and ECOG performance status)", plus a data dictionary and the guideline PDF. No imaging: "Detailed radiologic parameters ... were not available".

**Ground truth and raters.** The treatment actually received (registry). Two hepatologists "independently assessed whether the LLM-recommended treatment corresponded to the actual therapy". "Matched" is defined by "clinically equivalent treatment categories rather than exact textual agreement". No agreement statistic is reported.

**Models, guideline version.** ChatGPT 4o, Gemini 2.0, Claude 3.5, accessed 1-5 March 2025, standard configuration. "Each model inference was conducted twice to confirm reproducibility ... fixed decoding parameters." Prompts reference "the AASLD and EASL guidelines that were current at the time of each patient's diagnosis", so no single BCLC year applies. The paper's refs include AASLD 2023 (ref 4). Prompts were drafted by two hepatologists and one researcher.

**Metrics and headline results.** Concordance (match rate), Cox HR (multivariable, also IPTW and doubly robust, 1-year landmark, era-stratified), decision trees.
- Match rates: ChatGPT 4o 31.1%, Gemini 2.0 32.7%, Claude 3.5 26.8%.
- BCLC-A matched, multivariable HR: ChatGPT 0.743 (0.665-0.831), Gemini 0.902 (0.819-0.992), Claude 0.849 (0.767-0.940).
- BCLC-B matched, multivariable HR: ChatGPT 1.008 (0.943-1.078, p=0.810), Gemini 1.150 (1.075-1.229), Claude 1.058 (0.978-1.104, p=0.053).
- BCLC-C matched, multivariable HR: ChatGPT 1.650 (1.523-1.787), Gemini 1.586 (1.470-1.711), Claude 1.483 (1.366-1.610), all p<0.001.
- Whole cohort, ChatGPT matched, unadjusted HR 0.946 (p=0.011).

**Discrepancy to flag.** The Results text says "43.2% of patients were classified as BCLC-A and 31.5% as BCLC-C", but Table 2 shows A 4,064 (29.9%), B 5,265 (38.7%), C 4,285 (31.5%). Use the table.

**Availability.**
- Data Availability Statement, verbatim: "De-identified data from the Korean Primary Liver Cancer Registry are available from the registry's data access committee for researchers who meet the criteria for access to confidential data (https://livercancer.or.kr/eng/main.php). The registry data cannot be publicly shared due to institutional and national privacy regulations. No new analytical code was generated for this study and the standardized prompts used for the large language model inferences are provided in Table 1. All relevant data are available within the manuscript and Supporting information files."
- Prompts: all seven are printed in Table 1.
- Supporting information (titles only, not opened): S1-S14 Figs (DOCX), S1-S19 Tables (DOCX; S1 Table is the CHART checklist), **S1 Data "Raw outputs of LLM-generated treatment recommendations" (XLSX)**. The raw outputs are said to be provided there.
- Licence: article CC BY 4.0. No separate licence is stated for the supplement.

**Evidence.**
- "We analyzed 13,614 treatment-naive HCC patients diagnosed between 2008 and 2020 in the Korean Primary Liver Cancer Registry." (Abstract)
- "A total of 19,774 treatment-naive patients diagnosed between 2008 and 2020 were included in the present analysis, and those with insufficient follow-up (n = 2,640), missing laboratory data (n = 2,842), unavailable imaging (n = 26), unreported ECOG status (n = 168), or undocumented treatment history (n = 484) were excluded." (Methods, Study patients)
- "standardized prompts explicitly referencing the AASLD and EASL guidelines that were current at the time of each patient's diagnosis" (Methods, Generation and evaluation)
- "Two hepatologists independently assessed whether the LLM-recommended treatment corresponded to the actual therapy administered in clinical practice." (same)
- "Each model inference was conducted twice to confirm reproducibility, using identical prompts and fixed decoding parameters. The ChatGPT 4o, Gemini 2.0, and Claude 3.5 models were accessed between March 1-5, 2025" (same)
- "Concordance rates between LLM recommendations and physician decisions were 31.1% (ChatGPT 4o), 32.7% (Gemini 2.0), and 26.8% (Claude 3.5)." (Abstract)
- "the raw outputs of LLM recommendations are provided in S1 Data." (Methods)
- Table 1, row 4: "Study the attached hepatocellular carcinoma treatment guidelines (PDF file)." Row 7: "Do not list multiple options; provide a single best choice."
- Data Availability: see the verbatim statement above.

---

## 4. Jo et al., JMIR 2026

**Citation.** Jo SJ, Chong EH, Kang I, Yang SJ, Kang B, Kim JS, Chon HJ, Kwon CI, Sung MJ, Shin SP, An C, Lim HY, Yu JS, Jang S, Im JH, Ko KH, Lee SH. Large Language Models in Multidisciplinary Decision-Making for Hepatopancreatobiliary Oncology: Retrospective Comparative Feasibility Study. *J Med Internet Res* 2026;28:e101137. doi:10.2196/101137. PMC13585308, PMID 42753242. Published 17 September 2026. CC BY 4.0.

**Task.** Two outcomes: (1) stability of LLM treatment recommendations when the same case is queried 4 times; (2) concordance with the MDT decision. The treatment options are surgery, chemotherapy, radiation therapy, active surveillance, or further evaluation. This is **not an HCC study**: tumour location is biliary tract (intrahepatic, perihilar, distal, gallbladder), pancreas, or ampulla of Vater (Table 1).

**Cases.** 107 of 113 screened. Single-centre HPB MDT, CHA University Bundang Medical Center (Bundang CHA), Seongnam, Republic of Korea, 1 September 2024 to 31 August 2025. 77 primary and 30 recurrent or on-treatment. Location: biliary 71, pancreas 33, ampulla 3. MDT decision: chemotherapy 77, surgery 25, radiation 1, surveillance 2, further evaluation 2.

**Input to model.** Standardised textual case summary from pre-conference documentation: age, sex, CT/MRI abdomen and chest CT reports, biopsy histopathology if available, PET findings, labs (bilirubin, AST, ALT, CA 19-9, CEA). Radiology and pathology were "summarized in text format based on formal clinical reports rather than raw imaging". The MDT conclusion was excluded. The exact prompt wording is **not given in the main text**. The text only says the models were "asked to recommend the most appropriate treatment strategy among predefined options" and "instructed to provide a clear recommendation with a brief explanation".

**Ground truth and raters.** Real MDT decision. Resectability was graded "by 3 hepatobiliary and pancreatic surgeons reading together in a single consensus session ... blinded to both the MDT decision and all model output"; "interrater agreement could not be calculated". Sequential or combined plans were mapped to the first modality initiated.

**Models, guideline version.** GPT-4o, GPT-5.2, Gemini 3 Pro, Claude Sonnet 4.5, via consumer web interfaces, between November 2025 and May 2026. GPT-4o ran Nov-Dec 2025, GPT-5.2 Dec 2025-Jan 2026, Gemini 3 Pro to Feb 2026, then Claude about 1 month. 4 repeated queries, each in a fresh session. Browsing and memory off. GPT-5.2 used the default router (Instant or Thinking, not user-selectable). No guideline version or BCLC year is stated, and BCLC is not mentioned anywhere in the text. Institutional protocols are in Multimedia Appendix 1 (not opened).

**Metrics and headline results.** Discordance versus the first response, pairwise agreement, Fleiss kappa, concordance, Cohen kappa, class-wise F1 (surgery n=25, chemotherapy n=77 only).
| Model | Mean discordance (SD) | Fleiss kappa | Concordance, first response | Modal-response concordance | F1 surgery / chemo |
|---|---|---|---|---|---|
| GPT-4o | 30.2% (6.5) | 0.430 | 78/107 (72.9%) | 70/101 (69.3%) | 0.520 / 0.836 |
| GPT-5.2 | 22.7% (5.8) | 0.535 | 73/107 (68.2%) | 66/97 (68.0%) | 0.510 / 0.803 |
| Gemini 3 Pro | 12.8% (2.3) | 0.737 | 72/107 (67.3%) | 76/102 (74.5%) | 0.444 / 0.770 |
| Claude Sonnet 4.5 | 28.0% (1.5) | 0.572 | 52/107 (48.6%) | 65/98 (66.3%) | 0.400 / 0.621 |

Stability differed across models (P=.01). Cohen kappa (first response): GPT-4o 0.434, GPT-5.2 0.369, Gemini 0.286, Claude 0.115. Complete discordance (all four models wrong) 17/107 (15.9%), and 0 of 31 unresectable cases (Fisher P=.003). Independent predictors of complete discordance: recurrent or on-treatment disease aOR 5.40 (1.65-17.68), pancreatic location aOR 7.37 (2.03-26.78), low MDT agreement aOR 10.33 (1.54-69.38).

**Availability.**
- Data Availability, verbatim: "Data cannot be shared publicly for legal reasons. Data are available from the Bundang CHA Medical Center Institutional Data Access/Ethics Committee (contact via sungjun88@chamc.co.kr) for researchers who meet the criteria for access to confidential data."
- Supplementary: Multimedia Appendix 1 "Treatment category mapping rules and institutional protocols" (`jmir-v28-e101137-s001.docx`); Multimedia Appendix 2 "Annotated model rationales for 4 representative cases" (`jmir-v28-e101137-s002.docx`). Not opened.
- No code, no prompt text in the main article. Licence: article CC BY 4.0. No separate licence is stated for the appendices.
- Disclosure: Claude (Anthropic) was used for figures, preliminary verification of statistics and English editing.

**Evidence.**
- "A total of 107 MDT cases were analyzed." (Abstract, Results)
- "All consecutive cases discussed at the HPB MDT conference at our institution between September 1, 2024, and August 31, 2025, were screened for eligibility." (Methods, Study Design and Population)
- "a total of 113 cases were discussed ... 107 cases were included in the final analysis." (Results, Patient Characteristics)
- "Standardized clinical case summaries ... were provided to 4 LLMs (GPT-4o, GPT-5.2, Gemini 3 Pro, and Claude Sonnet 4.5) through their consumer web interfaces ... identical queries were repeated 4 times in separate sessions." (Abstract, Methods)
- "The documented MDT conclusion was not included in the model input" (Methods, Clinical Information Used as Model Input)
- "Radiologic and pathologic information was summarized in text format based on formal clinical reports rather than raw imaging or laboratory datasets." (same)
- "Because classification was reached by consensus rather than by independent reading, interrater agreement could not be calculated." (Methods, Study Design)
- "Concordance with MDT decisions ranged from 48.6% to 72.9% using the initial response and from 66.3% to 74.5% using the modal response" (Abstract)
- "Class-wise F1-score was consistently lower for surgery (0.400-0.520) than for chemotherapy (0.621-0.836)." (Abstract)
- "Data cannot be shared publicly for legal reasons." (Footnotes, Data Availability)

---

## 5. Tsinghua multimodal LLM-agent framework for HCC (Wang et al., Patterns 2025)

**Citation.** Wang L, Tian F, Li C, Wang J, Dong J, Cai J, Yang S, Feng X. A multimodal LLM-agent framework for personalized clinical decision-making in hepatocellular carcinoma. *Patterns (N Y)* 2025;6(12):101364. doi:10.1016/j.patter.2025.101364. PMC12745982, PMID 41472823. Published online 8 September 2025 (issue date 12 December 2025). Licence CC BY-NC-ND.

**Task.** A three-part pipeline: (a) predict pathological markers from MRI with a modified GhostNet plus radiomics; (b) turn clinical, imaging and pathology data into text; (c) six LLMs, each used as a decision agent grounded in a guideline knowledge base, give an HCC treatment recommendation. Surgeons then rate the recommendations.

**Cases.** Two cohorts, N = 435.
- Development: 330 resected HCC patients, Zhongshan Hospital, Fudan University (Shanghai, China), January 2020 to December 2023. This cohort trains the pathology model only.
- Agent evaluation: **105** patients "with a broader range of treatment modalities (including surgical resection, TACE, ablation, and systemic therapy)", Beijing Tsinghua Changgung Hospital (China), "during the same period". The paper calls this the "Tsinghua cohort", but one place calls it the "Chang Gung cohort".

**Input to the agents.** Structured text generated from the multimodal data: clinical indicators, imaging representation (radiomics and deep learning features from MRI) and pathological markers. Discussion: "Six LLM-based decision-making agents were evaluated using structured text inputs generated from multimodal data."

**Ground truth and raters.** There is no tumour-board ground truth and no concordance metric. The outcome is a blinded rubric: "four senior hepatobiliary surgeons, each with over 15 years of clinical experience ... independently assessed 105 patient cases" on four dimensions, "clinical consistency, personalization, feasibility, and alignment with actual treatment decisions". Responses were anonymised, randomly ordered per patient and scored over several sessions. No inter-rater statistic was found in the text read.

**Models, baselines, guideline version.** "six state-of-the-art LLMs: GPT-4, DeepSeek, BioGPT, Med-PaLM 2, LLaMA 2, and ChatDoctor". So the "six agents" are six different LLMs, not six cooperating agents. Each is "fine-tuned and integrated with a comprehensive HCC clinical knowledge base" (BCLC, CNLC, AASLD, EASL guidelines, literature, LI-RADS, WHO classification). Retrieval uses Neo4j and FAISS. **No BCLC year is stated.** Refs 6-7 are BCLC and CNLC. AASLD and EASL are refs 22-23. Image-model baselines were GhostNet, DenseNet121, ResNet50 and FBNet.

**Metrics and headline results.**
- Rubric, out of 20: DeepSeek-R1 17.79-18.58; GPT-4 and Med-PaLM 2 13.96-16.22; BioGPT and ChatDoctor 12.00-14.84; LLaMA 2 7.78-12.00. The ranges span the four surgeons.
- Robustness: 40 prompt variants, 16,800 responses; self-consistency rate 91.4%, Fleiss kappa 0.82, delta-AUC within +/-0.012, Jaccard@3 0.71 +/- 0.05.
- Pathology prediction (not LLM): the abstract reports MVI accuracy 0.8902 and capsule 0.8765, while the Results text attributes those two numbers to "MVI and tumor grade". Treat the mapping as inconsistent.

**Availability.**
- Verbatim, Resource availability / Data and code availability: "The data supporting this study's findings are not publicly available due to privacy and confidentiality restrictions. However, the datasets can be obtained from the corresponding author upon reasonable request, subject to approval and compliance with relevant data-protection regulations. Our source code is publicly available on GitHub (https://github.com/wangliyang123/HCC-decision-making-Agent) and has been archived on Zenodo with the following DOI: https://doi.org/10.5281/zenodo.15877157."
- Supplementary: "Document S1. Figures S1-S4, Tables S1 and S2, and supplemental methods S1-S7"; "Document S2. Article plus supplemental information". Prompt and robustness details are in supplemental methods (S6 is cited for the robustness setup). Not opened.
- The GitHub and Zenodo links were not opened. Whether prompts or the knowledge base are in the repo is unverified.
- Licence: "This is an open access article under the CC BY-NC-ND license". No separate licence is stated for the supplement. NC-ND restricts commercial use and derivatives of the article.

**Evidence.**
- "A total of 330 patients who underwent surgical resection at Zhongshan Hospital, Fudan University, between January 2020 and December 2023 were used to develop the pathological prediction model" (Methods, Patient enrollment)
- "An independent cohort of 105 patients with a broader range of treatment modalities (including surgical resection, TACE, ablation, and systemic therapy) was collected from Beijing Tsinghua Changgung Hospital during the same period" (same)
- "We developed a clinical decision-making framework based on six state-of-the-art LLMs: GPT-4, DeepSeek, BioGPT, Med-PaLM 2, LLaMA 2, and ChatDoctor. Each model was fine-tuned and integrated with a comprehensive HCC clinical knowledge base" (Methods, Clinical decision-making AI agents)
- "A blinded expert review was conducted involving four senior hepatobiliary surgeons, each with over 15 years of clinical experience. Each expert independently assessed 105 patient cases using a standardized evaluation rubric (Table 1)" (Methods, Evaluation)
- "yielding 16,800 LLM responses" (Results, Prompt robustness)
- "Among the six agents, DeepSeek-R1 demonstrated the highest overall score, with an average rating of 17.79-18.58 out of 20" (Results, Surgeon evaluation)
- "Six LLM-based decision-making agents were evaluated using structured text inputs generated from multimodal data." (Discussion)
- Data and code availability: see the verbatim statement above.
