# The Pipeline That Said No

Reproducible-methods companion to the essay **"The Pipeline That Said No"** and its X thread / long-form article.

**Thesis.** One fixed 9-stage computational drug-discovery pipeline — curation → noise-floor estimation → random-forest QSAR on ECFP4 → scaffold-split cross-validation → conformal prediction → applicability domain → freedom-to-operate → design → synthesis triage — run **unchanged** across four protein targets (ENPP1, NLRP3, TYK2, IRAK4) spanning a 23× range in public data. Its most valuable outputs are the two refusals: **ENPP1** (data starvation — "not yet") and **IRAK4** (IP saturation — "not here").

## What's here

| Path | Contents |
|---|---|
| `docs/` | The Substack essay, the X thread, and the X long-form article (markdown). |
| `figures/` | The four publication figures (reusability scorecard, conformal-bounds hero, IRAK4 confidence–freedom frontier, QC verification table). |
| `qc/` | Full QC/QA reconciliation report — every headline number re-derived with an orthogonal method. |
| `realworld/` | Live-verified clinical-trial records (ClinicalTrials.gov) and disease-burden + patient-advocacy grounding. |
| `references/` | All 28 references (CrossRef-verified) as markdown and BibTeX. |
| `provenance/` | Open-source tool + data attribution ledger. |
| `DELIVERABLES_INDEX.md` | Map of the full package. |

## Key verified numbers

- Curated compounds: **445 / 765 / 5,888 / 10,162** (ENPP1/NLRP3/TYK2/IRAK4) — exact.
- Cross-validated rank correlation ρ: **0.69 → 0.72 → 0.82 → 0.84**, climbing with data at fixed hyperparameters.
- Model error ≈ **1.1×** the experimental noise floor.
- ENPP1: **0 of 8** de novo designs cleared the pAct 6.0 floor on the honest conformal lower bound.
- IRAK4: **0 of 36** designs both buildable (interior applicability domain) and ownable (FTO Tanimoto ≤ 0.40).
- NLRP3: **14 of 24** cleared the floor; best lower bound 6.85.

## Disclosure boundary

The **engine** is disclosed in full — methods, counts, correlations, the empty quadrant. The **outputs are not**: no SMILES, no compound structures, no ChEMBL IDs, no patent numbers. *Publish the engine, patent the output.*

## Data & license

All bioactivity numbers derive from public **ChEMBL** data (CC BY-SA 3.0) by reference, not redistribution. Text and figures in this repository: CC BY 4.0 (see `LICENSE`). Nothing here constitutes medical advice or a proprietary structure disclosure.
