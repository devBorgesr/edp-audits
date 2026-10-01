# Executive summary — DPOLens Beta Audit #001

## Scope

This audit evaluates a public DPOLens GDPR held-out retrieval benchmark and compares the baseline `convex:0.5+normative` configuration against the same retrieval followed by the Jina multilingual reranker at depth 25.

The source snapshot used for the independent reproduction is:

`f1cddb56835cef31c4092d99e45e49383e425bf4`

The EDP input contains 30 queries and 867 searchable normative retrieval units.

## Reproduction

The upstream-style benchmark was independently reproduced before the EDP audit.

| Metric | Baseline | rerank25 |
|---|---:|---:|
| Recall@5 | 0.7667 | 0.4667 |
| Recall@10 | 0.8333 | 0.6667 |
| MRR | 0.5386 | 0.3276 |

This confirms the already-documented aggregate result that the reranker underperforms the baseline on this held-out set.

## EDP strict audit

The EDP relevance reference is non-exhaustive, so strict Recall and strict nDCG remain unavailable rather than being converted to zero.

### K = 5

| Metric | Baseline | rerank25 | Delta |
|---|---:|---:|---:|
| strict precision | 0.173333 | 0.106667 | -0.066666 |
| strict MRR | 0.528889 | 0.297222 | -0.231667 |

Precision increased on 1 query and decreased on 11. MRR increased on 4 queries and decreased on 16.

### K = 10

| Metric | Baseline | rerank25 | Delta |
|---|---:|---:|---:|
| strict precision | 0.100000 | 0.0833333 | -0.0166667 |
| strict MRR | 0.538611 | 0.327579 | -0.211032 |

Precision increased on 2 queries and decreased on 7. MRR increased on 4 queries and decreased on 18.

## Forensic follow-up

The follow-up did not rerun the retriever, embedding model, or reranker. It worked only from already captured ranking artifacts.

Top-10 rank status across 30 queries:

- improved: 3
- worsened: 12
- same: 4
- gained: 1
- lost: 6
- both miss: 4

At K=5 there were 10 hit regressions and 1 hit gain. At K=10 there were 6 hit regressions and 1 hit gain.

Rerank25 introduced duplicate-text groups in `health-data`, `publish-dpo-contact`, and `bought-a-list`. The latter two co-occurred with a rank regression.

That co-occurrence is useful as a follow-up experiment target but does not establish causation.

## Beta question

The aggregate reranker regression was already known upstream, so it is not presented as a new EDP discovery.

The validation question for this beta is narrower:

> Does the query-level decomposition and the additional structural forensic evidence reveal anything useful beyond the already-known aggregate regression?

Client feedback on that question is the primary outcome of Beta Audit #001.
