# HemaGuide — data & code

## Code — RELEASED, complete
https://github.com/Friedrich-Lab/HemaGuide → copied to `code/HemaGuide/`
Full runnable app: FastAPI backend, pre-built React frontend, agent pipeline
(`agent.py`, `src/`), all prompts (`prompts/*.yaml`), `build_kb.py`, launcher scripts.
Runs locally on Ollama (or OpenAI). Needs a `.env` (API keys) + a KB build step.

## Data

### Released (have it — `supplementary/`)
Paper's Data Availability statement: *"All constructed clinical cases used for
benchmarking, along with the corresponding agent outputs, are available in the
Supplementary Information."*
- `S1_Supplementary-Information.pdf` (393 pp) — **the constructed benchmark cases +
  HemaGuide's outputs for each**, plus all supplementary methods/figures
- `S2_Reporting-Summary.pdf` — Nature reporting checklist
- `S3_Peer-Review-File.pdf` — reviewer reports + author rebuttals
- `S4_Supplementary-Tables-1-12.xlsx` — cohort composition (47 entities), ablation
  results, model comparisons, etc.

### Knowledge base (buildable)
`code/HemaGuide/data/` ships onkopedia.json, gene_classification.json, entities.txt,
entity_slugs.json. `build_kb.py` constructs the vector DB from **Onkopedia**
guidelines (public). Optional: COSMIC (free registration) for variant hotspots,
OncoKB API key for molecular classification.

### NOT released
- The clinical decision memory of **>2,000 real tumour-board cases** (Heidelberg).
- The **555 external + 64 prospective** real validation cases (Heidelberg / partner sites).
Only the *constructed* (synthetic-from-real-templates) benchmark cases are public.
