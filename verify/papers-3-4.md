# Verification of papers 3 and 4

Date checked: 2026-11-02. Raw downloads are in `verify/raw/`.

## Access summary

| Paper | Full text read? | What was read |
|---|---|---|
| 3. Ciulli et al., Ann Surg Oncol 2026 | **No. Paywalled** | Springer landing page (abstract, reference list, citation block), the online Appendix 1 DOCX (full), Europe PMC record. |
| 4. Grapa et al., Livers 2026 | **Yes, full text and supplement** | MDPI full text, all 5 tables, and the supplementary PDF. |

Routes tried for Paper 3:
- `link.springer.com/content/pdf/10.1245/s10434-026-20253-8.pdf` returned an HTML page, not a PDF. That page says "This is a preview of subscription content" and offers "Buy article PDF 39,95".
- Europe PMC has the record (PMID 42722940) with `isOpenAccess: N` and `inEPMC: N`, so there is no full text there.
- Methods, dataset detail, rater count, blinding, statistics and the data availability statement for Paper 3 **could not be accessed**. They are not reported below. Only the abstract and the supplement were read.

Routes tried for Paper 4:
- `curl` to `mdpi.com` returned "Access Denied" (HTTP 403). WebFetch also got 403.
- A Playwright browser session loaded the page. The full text, tables and supplementary zip came from that session.
- Crossref and Europe PMC gave only metadata.

---

## Paper 3: Ciulli et al., Annals of Surgical Oncology

### Citation
- Authors: Cristina Ciulli, Emanuele Scarpa (joint first authors), Pasquale Chiacchio, Alessandro Fogliati, Mauro Alessandro Scotti, Antonio Ciaccio, Antonio Rovere, Christian Cotsoglou, Rita Niespolo, Federica Elisei, Michele Pietro Aprigliano, Lorenzo Ripamonti, Mattia Garancini, Fabrizio Romano. Corresponding author: Mattia Garancini.
- Title: Concordance Between a Generative Artificial Intelligence Model and a Hepatobiliary Multidisciplinary Team in Hepatocellular Carcinoma Management: A Retrospective Study
- Journal: Ann Surg Oncol (2026). Published online 10 September 2026. No volume or pages assigned yet: Europe PMC `pageInfo` is null, and the cite block has no volume.
- DOI: 10.1245/s10434-026-20253-8. PMID 42722940.
- Centre: IRCCS San Gerardo dei Tintori Foundation, Monza, Italy (University of Milano-Bicocca).

### Task
Agreement between a generative AI recommendation and an MDT decision on HCC management (abstract).

### Dataset (from abstract and supplement only)
- 100 HCC patients discussed at a hepatobiliary MDT, January 2023 to February 2025. These are real patients, retrospective.
- Anonymised case summaries, MDT conclusion excluded.
- The prompt describes the case summary as including: age and sex, past medical history, comorbidity index and performance status, oncological history, Child-Pugh and MELD-Na, BCLC stage, textual imaging reports, prior treatments, AFP, and social context. So the input was text only, with no images.
- Inclusion and exclusion criteria: **could not access**.

### Ground truth
- MDT decision (abstract: "expert MDT decisions").
- Number of raters, blinding, and how concordance was scored: **could not access**.

### Systems and baselines
- A single model, "ChatGPT-4" (abstract). Exact version, tools and RAG: **could not access**.
- Guideline: the prompt says only "the latest version of European guidelines on HCC treatment". The reference list cites EASL 2025 (J Hepatol 2025;82:315-74) and BCLC 2022. The EASL 2024 and BCLC 2026 editions are not named in anything I could read.
- Prompt: the model simulates an eight-specialist MDT and gives a conclusion (see Evidence).
- Human comparator: the MDT is the reference, not a separate comparator.

### Metrics and results (abstract)
- Four-level progressive concordance scale. Levels 1-2 are low concordance (LC) and levels 3-4 are high concordance (HC).
- HC in 81% of cases, with 53% complete agreement on technique.
- LC in 19%, associated with older age (p = 0.001).
- No kappa or Likert score is reported in the abstract.

### Availability
- Prompt: released in the supplement (Appendix 1, DOCX, 15 KB). It is the only supplementary file.
- Cases: **not seen**. The abstract does not mention case release. The data availability statement sits in the paywalled full text and could not be accessed.
- No GitHub or Zenodo link was seen on the landing page. The licence is "exclusive rights ... under a publishing agreement", so it is not open access.
- Page note: "During the preparation of this work the authors used ChatGPT to synthesize literature and improve language and readability."

### Match to the professor's descriptions
- "100-patient MDT comparison": **matches**.
- "100 literature-based cases": **does not match**. The cases are real patients.
- "88-item Script Concordance Test": **no match**. No SCT is mentioned in anything I could read.

### Evidence
1. "A retrospective cohort of 100 HCC patients discussed at a hepatobiliary MDT between January 2023 and February 2025 was retrospectively included. Anonymized case summaries, excluding MDT conclusion, were submitted to a large language AI model (ChatGPT-4)." (Springer abstract, Methods)
2. "Recommendations were categorized using the following four-level progressive concordance scale: (1) treatment aim (curative vs palliative), (2) treatment extent (locoregional vs systemic), (3) treatment modality (e.g., resection, ablation, embolization), and (4) specific technique. Concordance levels 1 and 2 were classified as low concordance (LC), whereas levels 3 and 4 were classified as high concordance (HC)." (abstract)
3. "In 81 % of the cases, HC between AI-generated and MDT recommendations was observed, with 53 % showing complete agreement on treatment technique. In 19 % of the cases, LC was identified and associated with older patient age (p = 0.001). In most discordant cases, discrepancies reflected the MDT's preference for more aggressive, tailored interventions." (abstract, Results)
4. "This is a preview of subscription content, log in via an institution to check access." (Springer page)
5. "Supplementary file1 (DOCX 15 KB)" (Springer page, Supplementary Information)
6. "You will be given anonymized clinical data about a hepatocarcinoma (HCC) patient. This case summary will include demographic data such as age and sex, past medical history, comorbidity index and performance status, oncological history, liver function (Child-Pugh and MELD-Na classification), Barcelona Clinic Liver Cancer (BCLC) stage, imaging textual reports, prior treatments, alpha-fetoprotein levels and relevant social context. You need to simulate a multidisciplinary team (MDT) analysing this case. The team is made up of eight members: liver surgeons, interventional radiologist, radiotherapist, nuclear medicine specialist, oncologist, hepatologist and an anaesthesiologist. You will need to mimic the response each specialist would suggest and, at last, give the conclusion that include the therapeutic pathway your MDT decided. You must align every specialist response and conclusion to the latest version of European guidelines on HCC treatment." (Supplementary Appendix 1 DOCX, "We used the following prompt for each case")
7. "Received : 06 February 2026 Accepted : 06 July 2026 Published : 10 September 2026 ... Cite this article Ciulli, C., Scarpa, E., Chiacchio, P. et al. ... Ann Surg Oncol (2026). https://doi.org/10.1245/s10434-026-20253-8" (Springer page, About this article)
8. Reference list entries "European Association for the Study of the Liver. EASL clinical practice guidelines on the management of hepatocellular carcinoma. J Hepatol. 2025;82:315-74." and "Reig M, Forner A, Rimola J, et al. BCLC strategy for prognosis prediction and treatment recommendation: the 2022 update. J Hepatol. 2022;76:681-93." (Springer page, References)
9. Europe PMC record: `"isOpenAccess":"N"`, `"inEPMC":"N"`, `"pageInfo":null`. (REST API, DOI query)

---

## Paper 4: Grapa et al., Livers (MDPI)

### Which paper this is
`mdpi.com/2673-4389/6/5/104` is the journal **Livers** (ISSN 2673-4389), volume 6, issue 5, article 104.

### Citation
- Authors: Cristiana Grapa, Tudor Mocan, Daniel-Corneliu Leucuta, Lavinia-Patricia Mocan, Rares Craciun, Horia Stefanescu, Bogdan Procopet, Miroslaw Kornek, Marcin Krawczyk, Nadim Al Hajjar, Andrei Varciu, Teodora Mocan, Zeno Sparchez (Crossref author list; the first two carry a dagger on the page).
- Title: Concordance Between Large Language Models and Multidisciplinary Tumor Board Recommendations in Treatment Allocation for Hepatocellular Carcinoma
- Journal: Livers 2026, 6(5), 104. Published 8 October 2026 (received 29 July, revised 17 September, accepted 30 September). 19 pages.
- DOI: 10.3390/livers6050104
- Centre: Regional Institute of Gastroenterology and Hepatology "Prof. Dr. Octavian Fodor", Cluj-Napoca, Romania.

### Task
Concordance of LLM treatment recommendations for HCC with MDT decisions. Each model gave a first, second and third treatment option.

### Dataset
- 100 real consecutive HCC patients (retrospective). Period: methods and results text say 2020 to 2025. Single centre, Romania.
- Inclusion: age over 18 with an established HCC diagnosis. All 100 consecutive cases met the criteria.
- Exclusion: indefinite diagnoses, or multiple neoplasias besides HCC (excluded before MDT presentation).
- Information given: a Word document identical to the MDT presentation, de-identified, with demographics (sex, age), laboratory values, imaging results and prior treatments. No images: imaging was given as text summaries (LI-RADS category, lesion number, diameter, location, vascular invasion, extrahepatic disease).
- Cohort (Table 1): BCLC 0/A/B/C/D = 6/56/25/10/3%. Child-Pugh A/B/C = 73/23/4%. ECOG 0 in 77%. LI-RADS 5 in 98%.
- MDT decisions (Table 1): ablation 30, surgical resection 25, LT alone or with bridge 14, systemic 13, TACE 8, BSC 7, TARE 2, TACE and ablation 1.

### Ground truth
- The MDT consensus decision plus two or three alternatives. The MDT is described as two hepatologists, two hepatobiliary surgeons, one transplant surgeon, two oncologists, one pathologist, an interventional radiologist, one radiation radiologist and one specialized nurse.
- No independent raters. Concordance is a categorical comparison between the LLM and the MDT. Inter-rater kappa does not apply. The kappa reported is LLM versus MDT.
- The authors say the MDT is not an absolute gold standard.
- Blinding: the case documents were compiled "by a physician who did not attend the board meeting or have knowledge of the treatment decisions". Each case was evaluated in a new, independent chat.

### Systems and baselines
- Four LLMs: ChatGPT-5 (GPT-5 Thinking), a custom "TumorBoard ChatGPT" (Custom GPT with persistent instructions, no fine-tuning), Gemini 2.5 Flash, Gemini 2.5 Pro.
- Run through consumer web interfaces (ChatGPT Plus, Google AI Pro), 1 August to 1 September 2025. Each case was run once. Web browsing and memory were not disabled. No API, so no temperature or other settings.
- No RAG or tools beyond the web interface. Guidelines named in the prompt: **EASL 2018 and AASLD 2018**, plus CIRSE 2019 and 2021 for ablation and TACE cases. BCLC 2022, BCLC 2026 and EASL 2024 are **not** given to the models. The MDT itself used "contemporary BCLC, EASL, AASLD".
- Human comparators: none beyond the MDT reference.

### Metrics and results
- Concordance with 95% Wilson CI, Cochran's Q, Holm-adjusted exact McNemar tests, Cohen's kappa, GEE. R 4.5.2. No Likert scale.
- First-recommendation concordance: GPT-5 81% (72.2-87.5), Gemini 2.5 Pro 80% (71.1-86.7), TumorBoard ChatGPT 78% (68.9-85.0), Gemini 2.5 Flash 63% (53.2-71.8).
- Cumulative top-3: 95%, 92%, 95%, 86%. Top-2 (Table 2): 91, 93, 91, 75.
- Kappa, granular: GPT-5 0.754, Gemini Pro 0.743, TumorBoard ChatGPT 0.622, Flash 0.537.
- GEE, Gemini Flash vs TumorBoard ChatGPT: estimate -0.792, p = 0.006.
- TACE precision was low for all models (0.280 to 0.778), so TACE was over-recommended.
- Authors' caution: the study was not powered to rank models.

### Availability
- Data availability statement, verbatim: "The data presented in this study are available on request from the corresponding author. The data is not publicly available due to privacy and ethical restrictions."
- Cases: not released. Prompt: released. Standardized patient template: released.
- Supplementary file, one PDF (`livers-06-00104-s001.zip`, containing `livers-4499996-supplementary.pdf`): Tables S1 and S2, the LLM prompt, and the patient template form.
- No GitHub or Zenodo link was found on the page.
- Licence: CC BY (article).

### Match to the professor's descriptions
- "100-patient MDT comparison": **matches**.
- "100 literature-based cases": **does not match**. They are real, consecutive patients.
- "88-item Script Concordance Test": **no match**. No SCT is mentioned.

### Evidence
1. "We retrospectively analyzed 100 HCC cases discussed during MDT meetings in a tertiary-care hospital in Cluj-Napoca, Romania. Identical prompts and structured clinical information were offered to four different LLMs (ChatGPT-5, a customized Tumor Board ChatGPT, Gemini 2.5 Flash, and Gemini 2.5 Pro)" (abstract)
2. "This retrospective study included 100 patients with hepatocellular carcinoma who were previously discussed during MDT meetings, between 2020 and 2025, at the Regional Institute of Gastroenterology and Hepatology, 'Prof. Dr. Octavian Fodor', Cluj-Napoca, Romania." (2. Materials and Methods)
3. "A total of 100 case files of patients aged > 18 years with an established HCC diagnosis were included in the study. Patients with indefinite diagnoses, or multiple neoplasias in addition to HCC, were excluded before being presented in MDT meetings. All 100 consecutive HCC cases presented to the MDT met the eligibility criteria and were included in the final analysis, with no subsequent exclusions or missing clinical data requiring imputation." (2.1 Patient Cohort)
4. "The documents were compiled by a physician who did not attend the board meeting or have knowledge of the treatment decisions, and contained patient demographics (gender and age), laboratory values, imaging results, and information on prior treatments." (2.2)
5. "Radiological examinations were not provided as original CT, MRI, or CEUS images. Instead, standardized text summaries extracted from the radiology reports ... were incorporated into the clinical case description presented to each LLM." (2.2)
6. "the LLM evaluations were performed between 1 August 2025 and 1 September 2025. The models were accessed through ChatGPT Plus Subscription and Google AI Pro Subscription; the GPT-5 Thinking model was used" (2.2)
7. "Web browsing/search and ChatGPT memory were not disabled, each case being evaluated in an independent chat. Each patient case was submitted once to each LLM, and no repeated inferences were performed." (2.2)
8. "No additional model training, fine-tuning, modification of model weights, or external training datasets were used." (2.2, on TumorBoard ChatGPT)
9. "This exploratory analysis does not assume that MDT decisions represent an absolute gold standard but rather uses them as a pragmatic real-world comparator" (1. Introduction)
10. "Following the discussion, a consensus decision was reached, along with two or three more alternatives that could be suitable for the patient (when possible)" (2. Materials and Methods)
11. "Finally, unlike previous studies, which mostly employed independent clinical evaluators, our analysis relied on a direct categorical comparison between LLM and MDT decisions" (4. Discussion, limitations)
12. "3. Apply the relevant guidelines on HCC treatment: EASL 2018 and AASLD 2018. For scenarios involving tumor ablation or chemoembolization, also apply CIRSE guidelines (2019 and 2021)." (Supplementary PDF, LLM prompt)
13. "Liver transplantation unavailable before September 2024, available afterwards." and "TACE used with curative intent for tumors <3 cm in patients with contraindications for surgery and ablation." (Supplementary PDF, LLM prompt, institutional protocols)
14. "First-recommendation concordance was 81% (95% CI 72.2-87.5) for GPT-5, 80% (71.1-86.7) for Gemini 2.5 Pro, 78% (68.9-85.0) for TumorBoard ChatGPT, and 63% (53.2-71.8) for Gemini 2.5 Flash; cumulative top-3 concordance reached 95%, 92%, 95%, and 86%, respectively. GPT-5 (κ = 0.754) and Gemini 2.5 Pro (κ = 0.743)" (abstract)
15. Table 2 rows: "ChatGPT 5 81 (81.0) [72.2, 87.5] 91 (91.0) [83.8, 95.2] 95 (95.0) [88.8, 97.8]; Gemini Flash 63 (63.0) ... 75 (75.0) ... 86 (86.0)"; "Cochran's Q p = 0.0007 / p < 0.0001 / p = 0.048". (Table 2)
16. "ChatGPT (κ = 0.754) and Gemini 2.5 Pro (κ = 0.743) had substantial agreement with the tumor board, while TumorBoard ChatGPT (κ = 0.622) and Gemini Flash (κ = 0.537) had moderate agreement." (3.3)
17. "All models had low precision for TACE (positive predictive value) (from 0.280 to 0.778) ... This means that the models over-recommended TACE in comparison with tumor board decisions." (3.3)
18. "The data presented in this study are available on request from the corresponding author. The data is not publicly available due to privacy and ethical restrictions." (Data Availability Statement)
19. "The following supporting information can be downloaded at: https://www.mdpi.com/article/10.3390/livers6050104/s1, 1. Supplementary Tables S1 and S2, 2. LLM Prompt. 3. Standardized patient template." (Supplementary Materials)
20. "This is an open access article distributed under the terms and conditions of the Creative Commons Attribution (CC BY) license." (page footer)
21. "These concordance rates reflect numerical agreement with MDT decisions and should not be interpreted as a measure of pertinence to treatment, guideline conformity, or patient outcome benefits." (4. Discussion)

### Internal inconsistencies noticed in Paper 4
- Ethics approval is "No. 9653/11 August 2025" in Methods but "protocol code NCT02979951/11 August 2025" in the IRB statement.
- The prompt names EASL 2018 and AASLD 2018, while the introduction cites EASL 2025 and BCLC 2026. The models were therefore not given the newest guidelines.
- The supplementary file title reads "Large Language Models Acting as Multidisciplinary Tumor Boards in Treatment Allocation for Hepatocellular Carcinoma", which differs from the published title.

---

## Overall
- Both papers are real-patient, single-centre MDT-concordance studies with about 100 cases. Neither is literature-derived and neither uses a Script Concordance Test. The professor's "100-patient MDT comparison" fits both. "100 literature-based cases" and "88-item SCT" fit neither, so those descriptions probably refer to other papers.
- Neither releases its cases as far as I could read. Paper 4 states data are on request only. Paper 3's statement could not be read. Both release their prompt.
