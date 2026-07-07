# The Pipeline That Said No

### I built a computational drug-discovery platform and ran it, unchanged, across four protein targets. This is the story of the target where the correct answer was "make nothing" — and why that's the result I trust most.

---

If you have spent any time near computational drug discovery, you have seen the post. A model, an impressive validation number, a gallery of molecules the model "designed," a gesture at a funding round. You have never seen the target where that same model failed, because failure does not trend.

I want to tell you the opposite story. I built a fixed discovery pipeline, ran it without modification across four protein targets spanning a 23-fold range in available data, and I am going to spend most of this essay on the target where the pipeline refused to nominate a single compound. That refusal is not the embarrassing part I'm burying at the bottom. It is the whole point. **A discovery tool that cannot say *no* is a random molecule generator with a communications team.**

Here is the reader's problem I'm actually writing to. Suppose you are a founder with a novel target and two hundred compounds in the published literature. Your computational team will build a model. It will generate hits. It will show you a ranked list. The only question that matters is whether those hits are real — or whether your medicinal chemist is about to spend six months and several hundred thousand dollars synthesizing compounds the model never actually believed in. Everything below is about the difference between those two worlds, and the specific, boring machinery that tells them apart.

---

## Act I — Why "boring" is the thesis

The platform — I call it CRISPRKing — is nine stages, and I fixed all nine in advance:

1. **Curation** — pull every public bioactivity measurement, standardize the structures, collapse replicate measurements to one honest value per compound, discard the unusable.
2. **Noise estimation** — measure the experimental floor from independent replicate pairs, per target.
3. **QSAR** — a random forest [11] on ECFP4 fingerprints [10]. Deliberately unfashionable. No graph neural network, no transformer, nothing I would have to defend to a skeptical reviewer.
4. **Scaffold-split cross-validation** — never score the model on a Bemis–Murcko chemical scaffold [12] it trained on.
5. **Conformal prediction** — every prediction carries an *honest lower bound* [13], not merely a point estimate.
6. **Applicability domain** — every prediction labeled interior, edge, or extrapolation.
7. **Freedom-to-operate** — Tanimoto distance from every design to the public intellectual-property landscape.
8. **Design** — generate candidates under target-specific chemical rules.
9. **Synthesis triage** — can a chemist actually make this, and at what cost?

The discipline I imposed on myself was a single rule: **the same code runs on every target with no per-target hand-tuning.** The moment you start lovingly massaging each dataset, you no longer have a platform — you have a sequence of anecdotes. The test of a pipeline is whether it survives contact with a target you did not tune it for.

So I chose four targets to span the data-availability spectrum on purpose — from a target with a few hundred compounds in the literature to one with over ten thousand:

| Target | Curated compounds | Potent | Generic scaffolds | Noise floor (log) | CV Spearman ρ | CV R² |
|---|---|---|---|---|---|---|
| ENPP1 | 445 | 138 (31%) | 117 | 0.35 | 0.689 ± 0.19 | 0.47 |
| NLRP3 | 765 | 189 (25%) | 248 | 0.68 | 0.722 ± 0.11 | 0.54 |
| TYK2 | 5,888 | 4,224 (72%) | 1,178 | 0.48 | 0.816 ± 0.01 | 0.66 |
| IRAK4 | 10,162 | 6,514 (64%) | 2,008 | 0.44 | 0.838 ± 0.03 | 0.71 |

Look at the cross-validated rank correlation column. It climbs monotonically with data — 0.689 on data-poor ENPP1 to 0.838 on data-rich IRAK4 — and it climbs *without* me touching a single hyperparameter between targets. That monotonicity is the feature. The hardest target is the one with the least data, and the pipeline reports that plainly instead of flattering it. An honest platform should be visibly worse where the data is thin. Mine is.

![Reusability across four targets: cross-validated rank correlation climbs monotonically with training-set size, with no per-target retuning](../figures/reusability_across_4_targets.png)

---

## Act II — Four targets, four verdicts

### ENPP1: the pipeline says no

ENPP1 is the data-poor end of the spectrum — 445 compounds, and a scattered, structurally diverse set at that (mean pairwise Tanimoto 0.16). It is a genuine target with genuine biology: it hydrolyzes cGAMP [2], the agonist of the STING innate-immune pathway, and extracellular cGAMP is itself a cancer-cell-produced immunotransmitter [3], which puts ENPP1 squarely in the anti-tumor immunity conversation. I wanted a lead. I had every motivation to find one.

I ran the full design pipeline. It generated candidates. Their point predictions came back between pActivity 6.2 and 6.7 — all of them above the 6.0 potency floor I use to decide whether a compound is worth a chemist's time. On a point-prediction basis, I had eight hits.

Then I applied the conformal lower bound: the 80%-confidence floor under each prediction, computed on a target where nearly every design lands in the *extrapolation* zone of a 445-compound model. Every single lower bound fell between 5.09 and 5.33. **Not one of the eight cleared the 6.0 line. Zero for eight.**

This is the moment the whole apparatus earns its keep. I was looking at eight point predictions that all said "make me," and a lower-bound column that said, in effect: *you do not have enough data to believe any of this.* The honest read on an extrapolative, data-poor target is that a point prediction of 6.4 with a lower bound of 5.3 is not a hit — it is a hope. I could have shown you the point predictions and called them a hit list. Instead the pipeline said no, and I listened, and I did not spend the money.

That is the single most valuable output of this entire project. Three targets produced compounds worth pursuing. One did not, and the platform told me so — in numbers, before the chemistry, not after.

![Every design looks like a hit until you take the honest lower bound: open circles (point predictions) all clear the 6.0 floor; filled dots (80% conformal lower bounds) tell the real story](../figures/conformal_bounds_hero.png)

The figure above is the entire thesis in one frame. Every open circle — the point prediction — sits to the right of the 6.0 line. On point predictions alone, all forty designs across three targets look like hits. The filled dots are the honest 80% lower bounds, and they tell the real story: ENPP1 clears zero of eight, TYK2 two of eight, NLRP3 fourteen of twenty-four. The gap between the open circle and the filled dot *is* the uncertainty you were about to pay a chemist to discover the hard way.

### NLRP3: yes, but gated — and the warhead I refused to design in

NLRP3 is the inflammasome sensor at the center of a genuine gold rush. Different problem entirely: here the model *did* produce candidates that survived the lower bound. Fourteen of twenty-four designs cleared the 6.0 floor, led by a design whose lower bound sat at 6.85 — real margin, not a squeaker.

But the interesting decision on NLRP3 was not which compounds to keep. It was a compound feature I chose to *exclude before generating anything*. The dominant chemical series in the known NLRP3 inhibitors — the MCC950 lineage [1] — carries a sulfonylurea warhead. It works. It also carries a liability profile I did not want baked into a fresh design campaign from the first atom. So the warhead policy for NLRP3 was set to **exclude** the sulfonylurea, deliberately, as a biological and developability judgment rather than a modeling one. Roughly a quarter of the public NLRP3 chemical space sits close enough to that warhead to be a freedom-to-operate concern anyway; walking away from it was both a safety call and an IP call at once.

The fourteen survivors were then packaged the way you would actually hand work to a synthesis house: sorted into chemical families by their disconnection strategy, split into a validation wave and an expansion wave, with a go/no-go gate between them so the chemistry gets proven on a cheap, fast subset before the full batch commits. That is the difference between "the model likes these fourteen molecules" and "here is a wave-gated synthesis campaign a contract lab can quote." The model output is the beginning of the medicinal chemistry, not the end of it.

### TYK2: the catch that justified building the selectivity check

TYK2 is where the pipeline caught itself doing something wrong, and where I'm most glad I built a step I was tempted to skip.

TYK2 is an allosteric target. The interesting drugs — deucravacitinib, approved for psoriasis, is the proof of concept — do not bind the catalytic kinase (JH1) site at all. They bind the JH2 *pseudokinase* domain, a regulatory module [4], and the medicinal-chemistry program that produced deucravacitinib was built entirely around that pseudokinase-domain selectivity [5]. My affinity model, trained across the available data, systematically *over-called* binding at exactly this JH2 domain. Left unchecked, it would have handed me a ranked list of confident, wrong "hits."

I only caught it because the pipeline scores JH2-versus-JH1 *selectivity* as a separate quantity — it does not just ask "will this bind," it asks "will this bind the right domain rather than the wrong one." When I debiased the rankings on that selectivity score, the confident hits collapsed. Most of them were artifacts of the model's JH2 inflation. **Two survived.** Two out of the batch.

And one of the two is worth the whole exercise: a differentiated allosteric lead with a novel ring architecture and a clean freedom-to-operate position against the public landscape — the kind of structure that justifies a real, specialized synthesis campaign rather than a parallel array. It only reached that status because a selectivity check I could easily have omitted told me which of the tempting hits were real. The difference between a screen and a scoreboard is whether you bother to check the number that is easiest to fake.

### IRAK4: the target where the model is confident and the answer is still no

IRAK4 was the fourth target, chosen to push the data axis to its richest end. Ten thousand curated compounds, the strongest model of the four (cross-validated ρ 0.838), and a prediction error that landed at about **1.1× the target's measurement noise floor** — the model is as good as the data physically permits.

So I ran the full conformal-gated design stage, expecting a clean "yes." What came back is the most conceptually interesting verdict of the four, and it is a *different kind of no* than ENPP1's.

I generated two design populations: emavusertib-class amides built on the pyrazolo-fused hinge binder that anchors the clinical IRAK4 chemotype [6], and novel-hinge bioisosteres designed to walk away from it. Then I scored both through the same funnel — conformal lower bound, applicability domain, and freedom-to-operate against the public patent landscape [7].

The two populations split cleanly, and catastrophically for the "yes" I wanted:

- **The emavusertib-class amides are confident.** Fifteen of them land in the *interior* applicability domain, with conformal lower bounds up to **5.97** — right at the worth-making floor. But every one of them sits at freedom-to-operate Tanimoto **0.47–0.69** to the public IP. They are confident because they look like the drug that already works — which is exactly why you cannot own them.
- **The novel-hinge bioisosteres are free.** They clear FTO down to Tanimoto **0.33**, comfortably clear of the patent. But they fall into the *edge* of the applicability domain, where the model has never seen this chemistry — and their conformal lower bounds collapse to **3.6–4.6**, far under the floor. They are ownable precisely because the model has no idea whether they work.

**Zero designs are simultaneously confident and ownable.** The buildable-and-ownable corner is empty. On a mature, heavily-patented target, the region where the model is confident is exactly the region the intellectual property already occupies — and the moment you step outside the patent, you step outside what the model can vouch for.

![IRAK4 confidence–freedom frontier: the buildable-and-ownable quadrant is empty. Emavusertib-class designs (filled) are confident but inside the patent; novel-hinge bioisosteres (open) clear freedom-to-operate but fall where the model cannot vouch](../figures/irak4_confidence_freedom_frontier.png)

This is the second "no" of the four targets, and it is worth sitting with how *unlike* ENPP1's it is. ENPP1 said no from **data starvation** — 445 compounds, everything extrapolative, the model honestly admitting it could not see far enough. IRAK4 says no from a **saturated IP landscape** — ten thousand compounds, the strongest model of the whole set, confident exactly where it isn't allowed to go. Same pipeline, same conformal gate, two completely different failure modes surfaced by the same machinery. That is what a platform is supposed to do: tell you *why* the answer is no, not just that it is.

---

## Act III — What this means

### The ceiling nobody benchmarks against

Here is the uncomfortable fact about public bioactivity data. The same compound, assayed against the same target in two different laboratories, does not return the same number [9]. That spread is real, and it sets a hard ceiling on how good *any* model can possibly be. No algorithm, however sophisticated, can predict a measurement more precisely than the measurement itself is reproducible.

So before trusting a single prediction, I estimated that ceiling for each target. I found the compounds measured independently more than once and — this part matters — I discarded the roughly 60% that were the same value simply re-deposited into the database. A re-deposit is not a replicate; counting it as one manufactures a fake noise floor of zero. This is not a hypothetical concern: in curated public IC50 data, a large fraction of nominally independent measurements differ by more than 0.3 log units, and a quarter by more than a full log unit [8]. From the genuine cross-source pairs, the real measurement noise for my four targets came out between 0.35 and 0.68 log units.

That number is the bar every model must be judged against. A model whose error is *below* the noise floor of its own training data is not brilliant — it is overfit, or it is leaking test information into training. Where I could measure it, my prediction error sat at about 1.1× the noise floor. That is the honest best case: the model is as accurate as the underlying experiments physically permit, and no more. Anyone reporting error meaningfully below their data's noise floor is measuring their own leakage and calling it performance.

### Where the pipeline says yes, it says it conditionally

On the targets that produced candidates, none of the "yes" answers were unconditional. Every nominated design was filtered on freedom-to-operate by Tanimoto distance to public IP, because a potent molecule you cannot own is an academic exercise — as IRAK4 demonstrated in the extreme. Every design was subject to a warhead policy set from the target's biology — *require* a zinc-binding group for the ENPP1 metalloenzyme because there is no potency without it; *exclude* the sulfonylurea for NLRP3 because of the liability profile. And every prediction was tagged interior, edge, or extrapolation, so that a reviewer can see at a glance which "hits" are interpolations within known chemistry and which are hopeful reaches beyond it.

### The clinic was running the same experiment

Here is the part I did not expect. The pipeline never saw a disease, a trial, or a patent office. It saw data density, applicability domains, and Tanimoto distances. Yet when I checked its four verdicts against the live clinical landscape — every trial pulled fresh from ClinicalTrials.gov, every indication verified — the pipeline's reasons for saying yes and no lined up, target for target, with what the real world is doing.

**ENPP1.** Only four ENPP1-inhibitor trials exist. The clinical lead, ISM5939 (InSilico Medicine), only just entered a first-in-human Phase 1 for advanced solid tumors (NCT06724042, primary completion 2029) — riding the same extracellular-cGAMP/STING immuno-oncology rationale [3] that makes the target interesting. An emerging, data-thin space. The pipeline read "not yet" from 445 compounds; the clinic is only now taking its first swing.

**NLRP3.** The verified lead, ISM8969 (InSilico Medicine), is recruiting right now in a Phase 1 for obesity and cardiovascular risk (NCT07581431) — a first-in-class footprint, matching the pipeline's "go." The NLRP3/IL-1 axis in cardiovascular inflammation is well established [14], and gout alone is one of the larger disease burdens tracked by the Global Burden of Disease study [15]. The advocacy communities are real — the Autoinflammatory Alliance for the rare CAPS patients, the broader arthritis community for gout.

**TYK2.** Deucravacitinib (Sotyktu, BMS) is FDA-approved and sits behind roughly a hundred trials, with a completed pivotal Phase 3 in psoriasis (NCT03624127) [16]. Psoriasis affects a low-single-digit percent of the global population — tens of millions of people [17], served by advocacy bodies like the National Psoriasis Foundation. The pipeline's most accurate model, on the most solved target. Confirmation, not discovery.

**IRAK4.** Ten trials, a crowded field, and the pivotal AML/MDS study — emavusertib (CA-4948, Curis), NCT04278768, 366 patients — is currently **suspended** [18], while combination trials continue to recruit. AML and MDS remain high-unmet-need diseases [19,20], carried by advocacy organizations like the Leukemia & Lymphoma Society and the MDS Foundation. A saturated, high-risk, IP-dense field — exactly the shape of the empty quadrant the pipeline found. The clinic is saying "not here" in dollars.

Two of the four verdicts were noes. The clinical world, running its own far more expensive version of the same experiment, independently reached the same two noes. That is the strongest external validation I could have asked for, and I did not engineer it — I just checked.

### Publish the engine, patent the output

A word on what is in this essay and what is not. The platform, the methods, the noise-floor discipline, and the full four-target reusability test are disclosed completely, because they are the *reusable* part — the engine. The specific winning structures are not shown here: not the fourteen NLRP3 designs, not the TYK2 lead, not a single SMILES string. That is not evasiveness. It is the clean split that any operator in this space should make: **publish the engine, patent the output.** The methods are what make the platform credible and worth talking about; the compounds are target-specific assets that stay behind the wall until the intellectual-property position is settled. A public post with drawable structures is prior art, and prior art filed by accident is an own-goal. Showing you the machine while withholding the parts is the correct disclosure boundary, not a hedge.

### Why boring is the entire argument

None of the individual pieces here is novel dry-lab science. Conformal prediction is not new [13]. Noise-floor estimation is not new [9]. Random forests [11], circular fingerprints [10], scaffold-split validation [12], freedom-to-operate analysis — all of them are standard, some of them decades old in their home fields. I want to be precise about that, because overclaiming novelty is exactly the sin this whole project is built to avoid.

What is uncommon is doing all of them at once, on every target, with no retuning, and then *publishing the two targets where the answer was no.* The value is not in any single method. It is in the refusal to skip the unglamorous checks — the ones that separate a result that reproduces from a result that gets a press release and then quietly fails to replicate. Boring is not a limitation of this approach. Boring is the argument. Boring is what reproduces.

### What's next

Four targets, four model validations, four design verdicts — two yeses and two structurally different noes. The pipeline is built and the arc is complete, but the interesting frontier now is the *fifth* target and beyond: does the "confident-but-patented / free-but-uncertain" frontier that IRAK4 exposed generalize to every mature target, and is there a principled way to push designs into the empty corner — active learning that spends its next experiments precisely on the ownable chemistry the model currently can't vouch for? The pipeline already tells you *where* that corner is. The next build is a design loop that aims for it on purpose. The data is public. The only variable left is whether the biology cooperates — and the entire point of building it this way is that when the biology doesn't cooperate, the pipeline will be the first to say so.

---

## References

1. Coll RC, Robertson AAB, Chae JJ, et al. A small-molecule inhibitor of the NLRP3 inflammasome for the treatment of inflammatory diseases. *Nat Med.* 2015;21(3):248–255. doi:10.1038/nm.3806
2. Li L, Yin Q, Kuss P, et al. Hydrolysis of 2′3′-cGAMP by ENPP1 and design of nonhydrolyzable analogs. *Nat Chem Biol.* 2014;10(12):1043–1048. doi:10.1038/nchembio.1661
3. Carozza JA, Böhnert V, Nguyen KC, et al. Extracellular cGAMP is a cancer-cell-produced immunotransmitter involved in radiation-induced anticancer immunity. *Nat Cancer.* 2020;1(2):184–196. doi:10.1038/s43018-020-0028-4
4. Burke JR, Cheng L, Gillooly KM, et al. Autoimmune pathways in mice and humans are blocked by pharmacological stabilization of the TYK2 pseudokinase domain. *Sci Transl Med.* 2019;11(502):eaaw1736. doi:10.1126/scitranslmed.aaw1736
5. Wrobleski ST, Moslin R, Lin S, et al. Highly selective inhibition of tyrosine kinase 2 (TYK2) for the treatment of autoimmune diseases: discovery of the allosteric inhibitor BMS-986165. *J Med Chem.* 2019;62(20):8973–8995. doi:10.1021/acs.jmedchem.9b00444
6. Gummadi VR, Boruah A, Ainan BR, et al. Discovery of CA-4948, an orally bioavailable IRAK4 inhibitor for treatment of hematologic malignancies. *ACS Med Chem Lett.* 2020;11(12):2374–2381. doi:10.1021/acsmedchemlett.0c00255
7. Xiang W, et al. Therapeutic compounds targeting interleukin-1 receptor-associated kinase 4 (IRAK4): an updated patent review (2016–present). *Expert Opin Ther Pat.* 2024. doi:10.1080/13543776.2024.2406825
8. Landrum GA, Riniker S. Combining IC50 or Ki values from different sources is a source of significant noise. *J Chem Inf Model.* 2024;64(5):1560–1567. doi:10.1021/acs.jcim.4c00049
9. Kramer C, Kalliokoski T, Gedeck P, Vulpetti A. The experimental uncertainty of heterogeneous public Ki data. *J Med Chem.* 2012;55(11):5165–5173. doi:10.1021/jm300131x
10. Rogers D, Hahn M. Extended-connectivity fingerprints. *J Chem Inf Model.* 2010;50(5):742–754. doi:10.1021/ci100050t
11. Breiman L. Random forests. *Mach Learn.* 2001;45(1):5–32. doi:10.1023/A:1010933404324
12. Bemis GW, Murcko MA. The properties of known drugs. 1. Molecular frameworks. *J Med Chem.* 1996;39(15):2887–2893. doi:10.1021/jm9602928
13. Vovk V, Gammerman A, Shafer G. *Algorithmic Learning in a Random World.* Springer; 2005. doi:10.1007/b106715
14. Grebe A, Hoss F, Latz E. NLRP3 inflammasome and the IL-1 pathway in atherosclerosis. *Circ Res.* 2018;122(12):1722–1740. doi:10.1161/circresaha.118.311362
15. Vos T, Abajobir AA, Abate KH, et al. Global, regional, and national incidence, prevalence, and years lived with disability for 328 diseases and injuries, 1990–2016. *Lancet.* 2017;390(10100):1211–1259. doi:10.1016/s0140-6736(17)32154-2
16. Strober B, Thaçi D, Sofen H, et al. Deucravacitinib versus placebo and apremilast in moderate to severe plaque psoriasis (POETYK PSO-2). *J Am Acad Dermatol.* 2023;88(1):40–51. doi:10.1016/j.jaad.2022.08.061
17. Parisi R, Symmons DPM, Griffiths CEM, Ashcroft DM. Global epidemiology of psoriasis: a systematic review of incidence and prevalence. *J Invest Dermatol.* 2013;133(2):377–385. doi:10.1038/jid.2012.339
18. Parrondo RD, Iqbal M, Von Roemeling R, et al. IRAK-4 inhibition: emavusertib for the treatment of lymphoid and myeloid malignancies. *Front Immunol.* 2023;14:1239082. doi:10.3389/fimmu.2023.1239082
19. Shallis RM, Wang R, Davidoff A, Ma X, Zeidan AM. Epidemiology of acute myeloid leukemia: recent progress and enduring challenges. *Blood Rev.* 2019;36:70–87. doi:10.1016/j.blre.2019.04.005
20. Rollison DE, Howlader N, Smith MT, et al. Epidemiology of myelodysplastic syndromes and chronic myeloproliferative disorders in the United States, 2001–2004. *Blood.* 2008;112(1):45–52. doi:10.1182/blood-2008-01-134858

Clinical trial records (ClinicalTrials.gov, verified live): NCT06724042 (ISM5939, ENPP1) · NCT07581431 (ISM8969, NLRP3) · NCT03624127 (deucravacitinib, TYK2) · NCT03328078 and NCT04278768 (emavusertib, IRAK4).

---

*Built on the CRISPRKing platform. Every quantitative claim in this essay is computed from public ChEMBL bioactivity data and backed by versioned analysis artifacts. Data derived from ChEMBL (CC BY-SA 3.0). No proprietary compound structures are disclosed. All fourteen literature references were verified against CrossRef before publication.*
