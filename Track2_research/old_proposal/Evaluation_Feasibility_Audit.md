# Evaluation feasibility audit

## Scope and sources checked

The active branch is `miguel/splade-investigation` and the RISE submodule was inspected at `7cffd574`. The workbook has 13 sheets, including Runs, Retrieval recall, Search analysis and Extensions. The prepared meeting brief and reconstruction in the request provide context. The reconstruction is the source for Jingfen's recent feedback. The repository's meeting note dated November 12 is earlier. The full audit, its quality control, the earlier 40-question subset, final ranking verification and prior replay findings inform this proposal.

I checked existence for 100 trace, 100 verdict and 100 workspace inventory records in each arm listed by the full-audit CSV. All 200 traces have schema 1.1. The auditable pairing is BM25 job 29379798 with Qwen3.6 rejudge and SPLADE job 29423816 with Qwen3.6 verdicts. The original BM25 score with the earlier judge was 44. Use the matched rejudge score of 45. Same recorded gpt-oss-120b, 100k plain corpus, K=1,000 and ten preview hits, with caps of 100 calls and 3,600 seconds. The final verification distinguishes recorded request configuration from unverifiable provider-side reasoning behavior. Its replay parity check is bounded. Deep ranking differences remain possible.

| Outcome, denominator 100 per arm | BM25 | SPLADE |
|---|---:|---:|
| Mean final workspace gold recall | 86.3% | 92.3% |
| Workspace contains any gold | 93 | 96 |
| Gold path documented | 66 | 69 |
| Gold body access verified | 43 | 42 |
| Decisive passage verified | 43 | 37 |
| Decisive passage uncertain | 28 | 38 |
| Correct final verdict | 45 | 37 |
| Saved search calls | 1372 | 1599 |
| Saved grep calls | 290 | 250 |

The counters are descriptive. Passage may be supplied directly by grep or a source outside the gold set, so gold opening and any-source passage exposure are distinct events. The 100-question audit reports a paired interval of about −15 to +2 percentage points for verified passage exposure and −17 to 0 for accuracy, SPLADE minus BM25. Uncertainty from missing observations is additional. Report the paired outcomes and coverage separately from these sampling intervals.

## A. Directly computable

- Benchmark correctness, final answer, final workspace size and qrel gold/evidence recall from verdicts, compressed inventories and `/home/scur0903/ir2-data/qrels/`. Filter eligibility to the pinned corpus and report exclusions.
- Existing stage labels, evidence anchors, question-paired tables, search/grep counts and termination from the full audit CSVs and trace metadata. Counts describe the recorded attempt. For budget reporting reconcile retained trajectory costs with retry accounting.
- Recorded elapsed time, turn/token totals and per-call seconds where present. The schema exposes the fields. Field completeness and clock comparability across all records still need checking. Keep model, tool, judging and infrastructure costs separately labeled.

## B. Computable with processing

- Normalize query strings and retain each occurrence, call, batch and position. Exact repeat rate is recoverable, including repeated subqueries in the same call. Use `args` and cross-check runner-specific counters. Parallel members of one call share a pre-call state.
- The retrieval archive has BM25, SPLADE and dense .trec files. Job 29303898 and 29383308 each have 100 full questions. Job 29390998 has 3,138 derived query rows and job 29392173 has 1,617, in addition to 100 full questions each. `build_queries` deduplicates text per question and assigns synthetic suffixes. Join by question ID plus canonical exact text and archived ranker/corpus configuration. Measure coverage separately per arm before reporting fixed-query results. These archives are frozen offline rankings. Neither their full coverage of the 200 live trajectories nor equivalence to exact live ranks is established here.
- Parse path sightings, valid line ranges, explicit failures and clips. Pinned `RISE/src/rise/agent.py` records a 200-character payload prefix by default. Search and native reads can omit the rest of the text, while shell text can already have been clipped by the live tool. Corpus reconstruction of a native read requires matching the saved prefix and recorded length on the exact pinned text and rendering. Label a prefix-only record unresolved when replay verification fails. Exclude filename, TOC or a header from body exposure unless the annotation explicitly accepts its content. Never execute archived shell strings.
- Derive budget curves, leads followed, repeated observed spans and path-to-read events. Require model decision ordering. A same-batch follow-up gets no automatic credit for having seen its sibling output. Establish per-event observability before classifying absence.

## C. Requires annotation

- Required clue groups and alternative sufficient sets, span sufficiency, source disagreement, genuine bridge reuse, and whether the final answer is supported by observed evidence. Existing corpus spans are seeds for independent checking. Annotate alternative evidence, including evidence outside the gold qrels.
- Query specificity or reformulation quality beyond repeat signatures needs behavioral labels or outcome proxies. Prefer concrete labels such as adds prior entity, relaxes constraint, or changes topic. No automatic credit for longer queries or terms shared with the answer.

## D. Requires new instrumentation or experiments

- Exact per-action workspace arrivals and live rank order where not archived. Log ranked identifiers, scores, corpus/index IDs and workspace additions keyed by call and query slot.
- Complete model-visible tool output and context-retention boundaries, output-token cost under a common tokenizer, stage-specific compute and load, and reliable evidence-supplied or evidence-removal continuations. Log live truncation and logging omission independently. Offline replay can estimate ranking opportunity without predicting the next agent action.
- Attribution quality needs cited outputs or manual reconstruction. The 730 held-out questions are candidates for later validation only after verifying matched records, permissions and costs.

## E. Not feasible from the present records

An exhaustive list of all relevant or alternative evidence in the corpus, exact unlogged shell output and historical provider-side behavior, and the causal effect of reading a particular passage on an already-completed trajectory. Dollar-per-correct depends on a credible common pricing convention. Report measured resource usage and, if available, a separately labeled cost estimate.

## Project references

`SPLADE_Full_Evidence_Audit/{SPLADE_Evidence_Stages_200_Trajectories.csv,SPLADE_Full_Evidence_Audit_Report.md,SPLADE_Audit_Quality_Control.md}`, `SPLADE_Final_Verification/`, `SPLADE_Consolidated_Review/`, `context/recall-experiment.md`, `tools/{search_analysis,breadth_depth,retrieval_recall}.py`, `RISE/src/rise/agent.py`. Existing reports and experimental outputs are preserved.
