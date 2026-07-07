# CRISPRKing X Thread — "The Pipeline That Said No"

_Hero image (crisprking_hero_conformal.png) attaches to post 6. Reusability chart (sop_reusability_n4.png) optional on post 3._

**1/**

I built a drug-discovery pipeline and ran it, unchanged, across 4 protein targets.

The result I trust most is the target where it refused to nominate a single compound.

A discovery tool that can't say "no" is a random molecule generator with a comms team. 🧵

---

**2/**

The setup: 9 fixed stages — curation, noise floor, QSAR, scaffold-split CV, conformal bounds, applicability domain, freedom-to-operate, design, synthesis triage.

One rule: same code on every target, no tuning. Tune each dataset and you don't have a platform — you have anecdotes.

---

**3/**

4 targets, chosen to span a 23× range in public data:

ENPP1 — 445 compounds
NLRP3 — 765
TYK2 — 5,888
IRAK4 — 10,162

Cross-validated rank correlation ρ: 0.69 → 0.72 → 0.82 → 0.84.

It climbs with data, with zero hyperparameter changes. The monotonicity is the feature.

---

**4/**

ENPP1, the data-poor end. I wanted a lead & had every reason to find one.

Pipeline generated 8 designs. Point predictions all 6.2–6.7 — above my 6.0 "worth-making" floor.

8 hits, on paper.

---

**5/**

Then the honest lower bound (80% conformal, designs landing in extrapolation):

Every bound fell 5.09–5.33. Zero of 8 cleared 6.0.

A prediction of 6.4 with a lower bound of 5.3 isn't a hit — it's a hope. The pipeline said no. I listened. I didn't spend the money.

---

**6/**

This is the whole thesis in one frame.

Open circles = point predictions — all right of the line. Filled dots = honest lower bounds.

ENPP1 0/8. TYK2 2/8. NLRP3 14/24.

The gap between circle and dot is the uncertainty you'd otherwise pay a chemist to find the hard way.

---

**7/**

NLRP3: here the pipeline said yes — 14 of 24 cleared the floor, best lower bound 6.85.

But the real call was excluding the MCC950 sulfonylurea warhead *before* generating anything — a safety + FTO judgment, not a modeling one. ~25% of public space is too close to it anyway.

---

**8/**

TYK2 is where the pipeline caught itself.

It's allosteric — the drugs bind the JH2 pseudokinase domain, not the catalytic site. My model over-called binding there.

Debiased on JH2-vs-JH1 selectivity, the confident hits collapsed. 2 survived. The check I nearly skipped saved me.

---

**9/**

The noise floor: the same compound in 2 labs gives different numbers. That spread caps how good ANY model can be.

I measured it per target (0.35–0.68 log) — after discarding the ~60% of "replicates" that were re-deposits. A re-deposit fakes a floor of zero.

---

**10/**

Where I could measure it, model error ≈ 1.1× the noise floor.

That's the honest best case: as accurate as the experiments physically allow, no more.

Anyone reporting error *below* their data's noise floor is measuring their own leakage and calling it performance.

---

**11/**

What's shown here: the engine — methods, noise-floor discipline, the full 4-target test.

What's not: a single compound structure. No SMILES, no designs.

Publish the engine, patent the output. A public post with drawable structures is prior art filed by accident.

---

**12/**

None of these methods is new. Conformal prediction, noise floors, FTO, scaffold-split CV — all standard, some decades old.

What's uncommon: doing all of them at once, on every target, no retuning — and publishing the target where the answer was no.

Boring is what reproduces.

---

**13/ (reply — sources)**

Sources, to check the work.

Methods: ChEMBL 2023 (10.1093/nar/gkad1004) · public-data noise floor, Landrum & Riniker 2024 (10.1021/acs.jcim.4c00049) · ECFP, Rogers & Hahn 2010 (10.1021/ci100050t) · RF-QSAR, Svetnik 2003 (10.1021/ci034160g) · time-split CV, Sheridan 2013 (10.1021/ci400084k) · conformal prediction, Norinder 2014 (10.1021/ci5001168).

Biology: ENPP1/cGAMP, Li 2014 (10.1038/nchembio.1661) · NLRP3/MCC950, Coll 2015 (10.1038/nm.3806) & Tapia-Abellán 2019 (10.1038/s41589-019-0278-6) · TYK2 JH2, Burke 2019 (10.1126/scitranslmed.aaw1736) & Wrobleski 2019 (10.1021/acs.jmedchem.9b00444) · IRAK4/CA-4948, Gummadi 2020 (10.1021/acsmedchemlett.0c00255) & patent review, Xiang 2024 (10.1080/13543776.2024.2406825).

All numbers computed from public ChEMBL data (CC BY-SA 3.0). No proprietary structures disclosed.
