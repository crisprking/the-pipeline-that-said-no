# The Pipeline That Said No — Deliverables Index

*Everything produced in this QC + real-world-grounding pass, with what each file is and how it was verified. The scientific thesis: one fixed 9-stage pipeline, run unchanged across four targets (ENPP1, NLRP3, TYK2, IRAK4), and its two "no" verdicts (ENPP1 = not yet, IRAK4 = not here) are its most valuable output.*

## Publication pieces

| File | What it is |
|---|---|
| `docs/substack_the_pipeline_that_said_no.md` | The definitive long-form essay ("The Pipeline That Said No"), ~4,000 words, 3 acts, embedded figures. Real-world clinical layer folded into Act III; all QC number-fixes applied (ENPP1 6.2–6.7; deucravacitinib "roughly a hundred" trials; "four ENPP1-inhibitor trials"; MW detail cut). |
| `docs/x_article_the_value_of_a_no.md` | X long-form article ("The Value of a 'No'"), distinct framing, all four targets, embedded figures, inline + full references. |
| `docs/x_thread.md` | 13-post X thread, complete 4-target arc with the IRAK4 empty-quadrant climax, live NCTs, hero + reusability figure callouts, full sources post. ENPP1 range corrected to 6.2–6.7. |

## QC / QA

| File | What it is |
|---|---|
| `qc/qc_report.md` | Full reconciliation. Every headline number re-derived from source data with an orthogonal method: counts & potent fractions exact; CV ρ reproduced within 0.03 via an independent RDKit/scikit-learn refit; IRAK4 empty quadrant 0/36 recomputed; noise-floor method pinned. |
| `figures/qc_verification_table.png` | One-glance verification table: stored vs. recomputed vs. status for all headline claims. |

## Figures

| File | What it is |
|---|---|
| `figures/reusability_across_4_targets.png` | The n=4 reusability scorecard: model quality (CV ρ) scaling with data at fixed hyperparameters, plus per-target curation/noise/warhead-policy table. |
| `figures/conformal_bounds_hero.png` | 40 designs across 3 targets: point predictions all clear the 6.0 floor; honest 80% conformal lower bounds tell the real story (ENPP1 0/8, TYK2 2/8, NLRP3 14/24). |
| `figures/irak4_confidence_freedom_frontier.png` | IRAK4's empty buildable-AND-ownable quadrant — the confidence–freedom frontier that defines the "not here" verdict. |

## Real-world evidence

| File | What it is |
|---|---|
| `realworld/clinical_trials.csv` | Five clinical trials verified live on ClinicalTrials.gov (status, phase, sponsor, indication, enrollment, dates, URL). |
| `realworld/disease_burden.md` | Disease burden + patient-advocacy grounding for all four targets, with CrossRef-verified epidemiology citations and real advocacy organizations (no fabricated patient quotes). |
| `provenance/opensource_provenance.md` | Honest attribution ledger: exact QC tool versions, canonical method/data citations, and the ChEMBL CC BY-SA 3.0 share-alike handling. |

## Citations

| File | What it is |
|---|---|
| `references/references_master.md` | All 28 references (13 methods/biology + 5 additional thread method/biology + 10 epidemiology) + 5 trial records, all CrossRef-verified live, formatted. |
| `references/references_master.bib` | The same 28 references as BibTeX. |

## Key verified facts

**QC (all reconciled):**
- Curated counts 445 / 765 / 5,888 / 10,162 — exact.
- Potent fractions 31 / 25 / 72 / 64% — exact.
- CV ρ 0.689 / 0.722 / 0.816 (stored) vs 0.716 / 0.743 / 0.828 (independent refit) — within 0.03.
- Model error ≈ 1.1× the experimental noise floor for every target.
- IRAK4: 0 of 36 designs both buildable (interior AD) and ownable (FTO Tc ≤ 0.40).
- NLRP3: 14 of 24 designs cleared the 6.0 floor; best lower bound 6.85.
- ENPP1: 0 of 8 de novo designs cleared the floor; predictions 6.2–6.7.

**Real-world (all live-verified):**
- ENPP1 — ISM5939, NCT06724042, Phase 1, advanced solid tumors, InSilico, starts 2026. Only 4 ENPP1 trials total.
- NLRP3 — ISM8969, NCT07581431, Phase 1 recruiting, obesity/cardiovascular risk, InSilico. First-in-class.
- TYK2 — deucravacitinib (Sotyktu), NCT03624127, Phase 3 complete, FDA-approved 2022. ~100 trials.
- IRAK4 — emavusertib (CA-4948, Curis), 10 trials; pivotal AML/MDS study NCT04278768 (n=366) SUSPENDED.

**Disclosure boundary honored throughout:** methods, counts, correlations, and the empty quadrant are fully disclosed; no SMILES, ChEMBL IDs, patent numbers, or compound structures. Publish the engine, patent the output.

**Citation verification:** all 28 DOIs across every deliverable re-checked live against the CrossRef API in the finalization pass — 28/28 resolve. No unverified reference ships.
