# Open-Source Provenance & Attribution Ledger

*Honest attribution of every open-source tool, dataset, and license behind the CRISPRKing pipeline and this QC pass. Two categories are kept separate: (A) tools I ran for the independent QC/verification, with exact installed versions; (B) tools and data the pipeline methodology relies on, attributed by canonical citation and license.*

The engineering ethos of this project — *publish the engine, patent the output* — only works if the engine is built honestly on the open-source commons and says so. This ledger is that disclosure.

---

## A. Tools used in this QC/verification pass (exact versions)

Captured live from the `chembio` conda environment used for the orthogonal recomputation (Section 3 of the QC report):

| Tool | Version | Role in QC | License |
|---|---|---|---|
| Python | 3.13.14 | interpreter | PSF |
| RDKit | 2026.03.3 | Morgan fingerprints (r=2, 2048-bit), Murcko scaffolds | BSD-3-Clause |
| scikit-learn | 1.9.0 | `RandomForestRegressor` refit | BSD-3-Clause |
| NumPy | 2.5.0 | numerics | BSD-3-Clause |
| pandas | 3.0.3 | data handling, count/fraction recomputation | BSD-3-Clause |
| SciPy | 1.18.0 | `spearmanr` correlation | BSD-3-Clause |

These are the tools behind every number in the QC reconciliation table. All are permissively licensed (BSD/PSF) and freely redistributable.

## B. Tools & data the pipeline methodology relies on

| Component | Canonical attribution | License | Notes |
|---|---|---|---|
| **RDKit** | Landrum, G. *RDKit: Open-source cheminformatics* | BSD-3-Clause | fingerprints, scaffolds, standardization |
| **ECFP / Morgan fingerprints** | Rogers & Hahn, *J Chem Inf Model* 2010, 10.1021/ci100050t | (method) | ECFP4 = Morgan radius 2 |
| **Random Forest** | Breiman, *Machine Learning* 2001, 10.1023/A:1010933404324 | (method) | QSAR regressor |
| **Bemis–Murcko scaffolds** | Bemis & Murcko, *J Med Chem* 1996, 10.1021/jm9602928 | (method) | scaffold-split CV |
| **Conformal prediction** | Vovk, Gammerman & Shafer, *Algorithmic Learning in a Random World* 2005, 10.1007/b106715 | (method) | prediction intervals / applicability |
| **scikit-learn** | Pedregosa et al., *JMLR* 2011 | BSD-3-Clause | ML implementation layer |
| **Conformal/interval library (MAPIE-class)** | Taquet et al., MAPIE | BSD-3-Clause | inductive conformal regression |
| **ChEMBL** (bioactivity source) | Zdrazil et al., *Nucleic Acids Res* 2024 (ChEMBL) | **CC BY-SA 3.0** | share-alike obligation — see below |
| **UniProt** (target sequences/annotation) | UniProt Consortium, *Nucleic Acids Res* | CC BY 4.0 | target identity |
| **Protein Data Bank (PDB)** | Berman et al., *Nucleic Acids Res* 2000 | CC0 (public domain) | structural context |
| **AlphaFold DB** | Jumper et al., *Nature* 2021; Varadi et al., *NAR* 2024 | CC BY 4.0 | predicted structures |
| **Reproducibility of noise/scaffold conventions** | Landrum & Riniker, *J Chem Inf Model* 2024, 10.1021/acs.jcim.4c00049; Kramer et al., *J Med Chem* 2012, 10.1021/jm300131x | (method) | experimental-error floor |

## The ChEMBL share-alike obligation (honest disclosure)

ChEMBL is licensed **CC BY-SA 3.0**. The **share-alike** clause matters for a "publish the engine, patent the output" strategy: derivatives *of the ChEMBL data itself* inherit the share-alike obligation, but **models trained on the data and novel molecules designed downstream are not themselves redistributions of the database**. The clean, defensible position — and the one this project takes — is:

1. **Publish the engine** (code, methodology, SOP) under a permissive license — it contains no ChEMBL records.
2. **Do not redistribute ChEMBL-derived datasets** as share-alike-encumbered tables; point to ChEMBL and its version instead.
3. **Patent the output** (novel compounds), which are new chemical matter, not database excerpts.

This is why the deliverables disclose *methods, counts, and correlations* but **never** raw ChEMBL IDs, SMILES, or extracted activity tables. The engine is open; the data stays at its source under its own license; the molecules are new.

## Attribution summary

- **Code stack:** entirely BSD/PSF permissive (RDKit, scikit-learn, NumPy, pandas, SciPy). No copyleft code obligations.
- **Methods:** long-published, freely usable algorithms (ECFP, random forest, scaffold split, conformal prediction), each cited to its primary source.
- **Data:** ChEMBL (CC BY-SA 3.0, handled by reference not redistribution), UniProt (CC BY 4.0), PDB (CC0), AlphaFold (CC BY 4.0).
- **Nothing proprietary or license-encumbered is hidden.** The one non-trivial obligation — ChEMBL share-alike — is disclosed and handled by design.

*Versions in Section A are the QC environment's; the original pipeline's exact pins are not asserted here because they were not captured in the source artifacts. Where a version is not known, it is not invented.*
