# Track 2 · Evidence-flow diagnosis in RISE

## Proposal for Ark

Jingfen highlighted three parts of the search process in our discussion: the queries the agent writes, what those queries put in the workspace, and how the agent uses that workspace. For Track 2, propose a retrospective study using the 100 matched BM25 and SPLADE questions.

**Research question.** Among incorrect answers with gold evidence staged, how often can verified tool outputs distinguish a missed or incomplete evidence exposure from an error after sufficient evidence was seen, beyond what document recall and answer accuracy reveal?

**Hypothesis.** A document-level utilization label covers several materially different failure locations. A passage-level audit can reliably distinguish some of them, while identifying cases where observability is insufficient. The contribution holds whether BM25 or SPLADE looks stronger. It does not require a significant difference in accuracy.

## What we add to existing evaluation

The strongest contribution is an empirical diagnostic analysis supported by an adaptation of existing metrics to RISE. The framework specifies how to link observations and handle missing data. A new metric or composite score would require validation beyond this study.

- **Conventional baselines.** Report gold recall, any gold staged, and answer accuracy. They tell us what was available and whether the answer passed. They cannot distinguish a visible lead, a failed read, clipping, or a correct span followed by an incomplete answer.
- **Document-level diagnostic.** Liu et al. split incorrect trajectories by whether retrieval returned any gold. Our primary inventory analogue splits by any gold staged. A second baseline uses attributable returned search-preview gold where recoverable. Gold paths from all tools are reported separately because they also include grep and listings. Compare the coarse label with independent evidence-level diagnoses. Retrieval ranks, the staged union, and what the model sees are different objects. https://arxiv.org/html/2608.01913
- **Passage and evidence diagnostics.** AtlasNav already tracks evidence realization under finite budgets. RAGChecker already applies claim-level entailment to RAG. HotpotQA provides a span-supervision precedent. Our comparison tests the incremental value of those distinctions for a particular retriever swap and a partially observed interactive workspace. Track 1 cross-reference AtlasNav motivates the exposure curve. Our contribution is the comparative audit with provenance, uncertainty, and independent validation. https://arxiv.org/abs/2608.24764 https://arxiv.org/abs/2408.08067 https://arxiv.org/abs/1809.09600

## Six metrics to take forward

1. **Marginal gold gain per search batch** measures query formulation on frozen rankings. Include as a secondary analysis, restricted to covered query strings.
2. **Final workspace gold recall** measures which labeled documents became accessible. Report any-gold counts as the document diagnosis baseline.
3. **Visible lead to same-source body access** measures utilization. Count a verified read of the same document within five subsequent model decisions and report how many leads occurred near termination.
4. **Decisive passage exposure by budget** measures whether a useful span reached the agent by 5, 10 and 20 model decisions and by termination.
5. **Sufficient observed evidence** measures whether the agent saw at least one complete, adjudicated set of clues before its final answer or termination. Cross-tabulate it with correctness.
6. **Final answer correctness** uses the existing common-judge verdicts. It is the endpoint against which the diagnostic information is interpreted.

See Track2_Core_Metrics.md for formulas, denominators, evidence requirements and limitations.

## What the 100 paired questions already show

The common-judge comparison uses BM25 job 29379798 with its Qwen3.6 rejudge and SPLADE job 29423816. Both arms use gpt-oss-120b, the plain 100k corpus, K=1,000 per query and ten preview results. The provider's actual reasoning behavior was not independently verified. All 200 trace, verdict and inventory paths were verified in the initial feasibility audit.

| Outcome per 100 in each arm | BM25 | SPLADE |
|---|---:|---:|
| Mean final workspace gold recall | 86.3% | 92.3% |
| Any gold staged | 93 | 96 |
| Gold path observed | 66 | 69 |
| Gold document body access verified | 43 | 42 |
| Decisive passage verified | 43 | 37 |
| Decisive passage uncertain | 28 | 38 |
| Sufficient evidence verified | 37 | 35 |
| Sufficient evidence uncertain | 34 | 40 |
| Correct final answer | 45 | 37 |

The inventories measure the realized workspace union. Per-query retrieved gold at a common K belongs in the separate frozen-ranking analysis. The 43/37 passage counts are verified positives, and the remaining observations include 29/25 with no documented event. That label alone does not establish a verified negative. A negative requires enough observation to rule out the event for the specified source and window. A recorded failed read or clipped response can rule out that exposure through that call, even while other calls remain unresolved. Native read reconstruction must match the pinned corpus, logged arguments, saved prefix and returned length. Model-visible exposure does not establish attention, retention, or causal use.

Among incorrect answers, 48/55 BM25 and 59/63 SPLADE had gold staged. Within those groups, the existing sufficiency labels are 0/1 verified sufficient, 25/37 uncertain, and 23/21 without a documented event. This preliminary table establishes a difference in what the labels say. Independent review must determine how many of the last group can be classified as verified incomplete exposure. The existing paired interval for the verified decisive passage difference spans roughly −15 to +2 percentage points, SPLADE minus BM25. That interval and uncertainty from incomplete outputs address different questions.

### Demonstrated refinements from recorded trajectories

- **q288, visible lead without verified decisive access.** Both arms surfaced the same Chartwell biography after the same query. It ranked second for SPLADE and third for BM25. BM25 opened it and saw both militaries. The SPLADE trace records a failed guessed-path read and a grep phrasing that missed the source wording, with no verified sighting of the decisive passage. The observed failure is downstream of ranking and lead visibility. Whether opening the same file would have changed the answer is a hypothesis.
- **q1038, clipping.** SPLADE matched a line containing the talk title, but the recorded response clipped before the title. BM25 recovered the title with a bounded match. A candidate hit and an evidence-bearing observation differ in this recorded call. An output renderer change might improve exposure in a future controlled study.
- **q229, error after adequate exposure.** SPLADE saw “Russell David Lyons” before returning a name without “David.” The common judge marked the truncated name wrong. This refines the broad utilization label to an observed answer-completeness issue. It does not reveal which internal process produced the omission.

## Minimum viable study

1. **Freeze the protocol.** Use all 100 pairs for inventory, observable event and verdict summaries. Record corpus, ranker and judge versions, call budgets, termination and output provenance. Define one grain per metric and treat parallel calls as a single observation boundary until their outputs are available to the next model decision.
2. **Create an auditable event table after team agreement.** Reuse existing path mappings, qrels, trace parsing and audit anchors. Save question, arm, decision and call order, document ID, span, evidence kind and observability. Distinguish direct logged text, validated reconstruction, prefix-only text and live clipping. Credit decisive evidence supplied directly by grep or a non-gold source. Verify fixtures for failed calls, same-batch reads, clipped output and missing prefixes. Do not execute archived shell commands.
3. **Validate the diagnostic increment.** Pilot on six varied pairs, including the three known examples. Exclude pilot pairs from the held-back validation. Randomly sample about 20 more pairs using predeclared strata for answer agreement and incomplete trace coverage, ensuring enough gold-staged errors. Two reviewers independently score with retriever, verdict, coarse label and prior annotations masked where feasible. Define clue alternatives before scoring. Adjudicate scope, exposure, sufficiency and mechanism with cited spans. Report the agreement matrix and class counts before adjudication. Keep purposive examples and stratified validation summaries separate from population estimates unless weighting and its uncertainty are reported.
4. **Compare endpoints and diagnoses.** Primary diagnostic endpoint is the proportion of reviewed gold-staged errors with an independently corroborated specific diagnosis among all reviewed gold-staged errors. Report specific navigation/delivery loss, partial exposure, post-sufficiency error and unresolved counts, plus exceptions and denominator. A diagnosis is corroborated when both independent reviewers agree on its location and cite compatible evidence. Report adjudicated counts separately. Use paired question bootstrap intervals for full-cohort stage differences and exposure curves, and exact McNemar only for binary observed endpoints or explicitly labeled verified-positive indicators. For incomplete exposure labels show bounds [verified positives/N, (verified positives + unresolved)/N]. Treat related secondary comparisons as exploratory.
5. **Present the result.** Deliver a full-cohort paired stage table, a document-baseline versus reviewed diagnosis table with unknowns, a fixed-decision-budget exposure plot, and at least two contrasting cases. A meaningful minimum is at least two distinct, independently corroborated refinements in held-back cases, with source anchors, reported reviewer agreement and an explicit unresolved fraction. If the holdback cannot support this, report the observability limits and narrow the conclusion to the cases the traces support.

Estimate one to two person-days for extraction and figures and two to four shared person-days for review and write-up. No GPU, Slurm, or new agent execution is needed. Main threats are incomplete or differential logging, incomplete qrels, source conflicts, hindsight in scoring, unequal lead opportunities, historical serving differences, one trajectory per arm and use of a development set. Conditioning on staged or visible evidence is descriptive. Fairer harness comparisons would need shared tasks, common observation criteria and decision or tokenizer budgets, with architecture-specific opportunity measures kept separate.

## Secondary query analysis

A read-only join of verdict query occurrences to archived query manifests used question ID and whitespace-normalized text. Job 29390998 covers 1,619/1,619 BM25 occurrences and 131/1,815 SPLADE occurrences, including first queries for 100 and 11 trajectories. Job 29392173 covers 1,619/1,619 and 59/1,815, and first queries for 100 and 6. Neither manifest covers a complete SPLADE trajectory. These are text matches. Verify actual ranking rows, depth and archive configuration before computing gains.

- **Marginal gain and cumulative recall:** feasible for BM25-generated sequences replayed through compatible archived rankers, with matched K and complete prefixes. Restore batch boundaries from traces. Stop the cumulative curve at the first missing lookup. Use the archived full-question result and first batch as reference points.
- **Redundancy:** count exact repeated normalized occurrences in both logs, including repeats within or across batches. Treat repetition as descriptive because scope and intent can change. Result overlap needs compatible rankings.
- **Fixed-depth effectiveness and first versus later:** compare the same text across rankers, or compare full question, first and later queries under one ranker for the covered source. Report a fixed cohort or the number with each later query to avoid treating earlier stopping as evidence of low effectiveness. Sparse SPLADE matches permit case checks only.

Include the BM25-origin frozen-query analysis as a secondary study if ranks and configurations pass verification. Postpone live query-policy comparisons. Even equal text coverage would leave different prior observations and adaptive stopping as confounders. The published reformulation comparison motivates the design, while the offline exercise estimates ranking behavior on fixed strings. https://trec.nist.gov/pubs/trec29/papers/OVERVIEW.C.pdf

## Decisions for the group

Agree on the baseline split and metric panel. Set five decisions as the lead window and 5/10/20 as exposure checkpoints, or choose replacements before seeing the curves. Assign two reviewers, agree how to annotate alternate sufficient evidence and decide the validation sample and reporting threshold. Keep the query analysis secondary and contingent on verified ranks. Approve a separate follow-up only if the retrospective results motivate one.

Basis: Track2_research/Initial_proposal and the SPLADE_Full_Evidence_Audit CSVs, final verification, and Evidence Stage Research Section. The current task added a read-only manifest coverage check. No experiments or implementation were run.
