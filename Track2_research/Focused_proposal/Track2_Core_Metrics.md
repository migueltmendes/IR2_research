# Track 2 · Core metrics and scoring rules

Use question as the paired analysis unit. For question i, G is the set of eligible labeled gold documents, W is the final staged set, and O(t) is the attributable evidence delivered by decision budget t. Report supporting qrels separately if used. Across metrics distinguish verified positive, verified negative under adequate observation, and unresolved. Existing “no documented event” needs verification before counting as negative.

## 1. Marginal gold gain per search batch

**Definition.** Let R be the union of top-K IDs for all queries in the batch, under a frozen ranker. Gain is the number of gold IDs in R but outside the prior reconstructed union, divided by the number of eligible gold IDs. Report the cumulative sum over complete observed prefixes and raw novel hits. A single-query initial batch uses an empty prior union. Parallel subqueries share the pre-batch union.

**Scope and value.** Adapted from interactive retrieval novelty evaluation. It separates an effective reformulation from a redundant high-ranked hit and captures incremental query value beyond final recall. Use a query-ID-plus-text crosswalk, chronological batches, common K and matching ranker configurations. Available secondarily for covered BM25-origin sequences. Neither archived manifest covers a complete SPLADE trajectory. Adaptive histories and stopping confound policy comparisons. [TREC Dynamic Domain](https://trec.nist.gov/pubs/trec26/papers/Overview-DD.pdf) · [TREC CAsT](https://trec.nist.gov/pubs/trec29/papers/OVERVIEW.C.pdf)

## 2. Final workspace gold recall

**Definition.** Per question, count the intersection of W and G and divide by the size of G, then macro-average. If eligible gold is empty, report the exclusion. Report any-gold as a binary companion for the diagnostic baseline, with workspace size alongside.

**Scope and value.** Established recall on the final inventory. Computable from pinned qrels, mapped workspace inventories and corpus eligibility. Identifies opportunity independent of reads. The union is unordered, coverage can increase with more queries, and labeled gold may omit valid alternatives. [BrowseComp-Plus](https://aclanthology.org/2026.acl-long.1023/)

## 3. Visible lead to same-source body access

**Definition.** Use the first verifiable path sighting per question, run and eligible target document. Of these leads, count those followed within five subsequent model decisions by verified delivery of body text attributable to that document. Exclude same-decision sibling calls from success attribution. Report all eligible leads, unresolved records and leads near termination. A final decision followed by no further opportunity is a nonconversion under the executed trajectory when subsequent behavior is known. Report a sensitivity table for leads with a full window. Report already-exposed sources separately.

**Scope and value.** Adapted stage transition for utilization. Establishes whether an available path was followed. Recoverable in part with path mapping, tool output and validated native read reconstruction. Preview metadata and failed opens do not count as body access. For paired comparisons predefine shared eligible documents from inventories, report path incidence as well as conversion, and use question-clustered summaries. Compare direct grep evidence through metric 4 even when no later read is needed. [AtlasNav, Track 1 cross-reference](https://arxiv.org/abs/2608.24764)

## 4. Decisive passage exposure by budget

**Definition.** For each t in 5, 10 and 20 decisions and at termination, count all questions with at least one verified answer-bearing span in O(t), divided by all eligible questions. A decisive span establishes a prespecified key clue. It may be incomplete for the whole answer. Count exposure when the tool response is available after the call, before the next decision. Stopped trajectories stay in the denominator. Report positive and unresolved fractions and bounds.

**Scope and value.** Adapted evidence realization. Shows when answer-bearing text reached the agent even without an explicit read or a successful answer. Existing timestamps and labels seed the measurement. Check source, span and provenance, including accepted alternate sources. A logged prefix provides positive evidence for what it contains. Missing text blocks some negatives. Decision and token budgets should be held comparable across interfaces. [AtlasNav, Track 1 cross-reference](https://arxiv.org/abs/2608.24764) · [HotpotQA](https://arxiv.org/abs/1809.09600)

## 5. Sufficient observed evidence before answering

**Definition.** Mark success if O before the final answer contains spans that jointly support every required clue in at least one pre-annotated acceptable evidence route. For a run without a final answer use observations through termination and mark the missing answer separately. Report question-level verified, inadequate under adequate observation, and unresolved counts. Cross-tabulate with correctness across all questions and within the gold-staged error stratum.

**Scope and value.** Adapted claim/support evaluation. Separates partial access and errors after enough evidence was seen. Needs masked human label validation and acceptance of alternative sources. Current audit labels are preliminary. Sufficiency depends on the codebook and source conflicts. Seeing evidence cannot identify causal use. [RAGChecker](https://arxiv.org/abs/2408.08067) · [Mind2Web 2](https://arxiv.org/abs/2506.21506)

## 6. Final answer correctness

**Definition.** Common-judge correct verdicts divided by all benchmark questions, treating missing finals as incorrect under the benchmark policy. Preserve matched question IDs and report discordant pairs. Disclose judge disagreements and keep support and correctness separate.

**Scope and value.** Established endpoint. Computable from the matched Qwen3.6 verdicts. It calibrates the practical importance of stage differences, while stage and sufficiency tables explain distinct observable failure locations. Exactness and judge sensitivity remain. [BrowseComp-Plus](https://aclanthology.org/2026.acl-long.1023/)
