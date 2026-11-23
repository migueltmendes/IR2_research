# Summary for Ark — Evaluation Metrics and Proposed Direction

## Slack version

My reading of Jingfen’s feedback is that we should evaluate how the agent forms queries, what enters the workspace, and what the agent actually gets to see and use. Our current SPLADE comparison gives us a good test case. Across 100 matched questions, SPLADE increased average workspace gold recall from 86.3% to 92.3%, while the audit verified decisive passages on 37 questions versus 43 for BM25. The passage difference is uncertain and the logging gaps matter. This is a good reason to evaluate the stages explicitly.

I’d propose a paired evidence-flow audit. Use final workspace coverage for construction, then measure visible paths, successful body access, decisive passage or clue exposure at a fixed model-decision budget, and final answer correctness. Keep the denominators and unknown cases visible. For query formulation, we can measure how many new gold or supporting documents each search batch adds at equal budgets, using archived rankings after verifying the query joins. An identical-query comparison can tell us something about ranking without rerunning the agents, while live query choices still depend on earlier observations.

AtlasNav already describes a similar evidence funnel, and SearchAtlas traces evidence and dependencies between queries. Our contribution could be a validated paired analysis that shows when a gain in recall becomes usable evidence under the same RISE interface. We have all 200 trajectories, verdicts and workspace inventories. I’d start by freezing the metric definitions, independently labeling a small masked set of question pairs, and producing the paired table and a budgeted exposure plot. That gives us something concrete to discuss before deciding whether any new experiment is worth running.

## Short version

I think Jingfen was suggesting that we measure how agents write queries, build workspaces, and actually reach usable evidence. I’d propose a paired evidence-flow audit using the 200 saved BM25 and SPLADE traces, with decisive evidence exposure at fixed decision budgets and explicit handling of incomplete logs. AtlasNav and SearchAtlas give us precedents, so the contribution would be validating the measures and applying them to the paired RISE comparison. I’d start with a small masked annotation sample and check whether the archived rankings can support a query-gain analysis.
