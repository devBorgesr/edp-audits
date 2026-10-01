# Executive summary — DPOLens Beta Audit #001

## Scope

This audit evaluates the public DPOLens GDPR held-out retrieval benchmark and compares the baseline `convex:0.5+normative` configuration against the same retrieval followed by the Jina multilingual reranker at depth 25.

**Audited source snapshot:**  
`f1cddb56835cef31c4092d99e45e49383e425bf4`

The evaluation contains 30 held-out queries and 867 published, effective, normative, searchable retrieval units in the audit corpus.

No private data, infrastructure credentials, or production access were required for this beta.

## Reproduction boundary

The public DPOLens results were originally associated with an earlier repository commit. This audit reproduced the benchmark on the audited current snapshot above.

The reproduced baseline aggregate matches the published aggregate, but this should **not** be described as a byte-for-byte historical reproduction of the earlier commit.

The rerank25 rankings were captured with a custom runner against the source reranker implementation. They should not be described as a native CLI reranker execution.

## Aggregate result

| Metric | Baseline | rerank25 |
|---|---:|---:|
| DPOLens-style Recall@5 | 0.7667 | 0.4667 |
| DPOLens-style Recall@10 | 0.8333 | 0.6667 |
| DPOLens-style MRR | 0.5386 | 0.3276 |

DPOLens-style “Recall@k” is the upstream binary hit-rate under its expected-key / descendant rule. It is not EDP strict Recall.

The direction of the aggregate result was already known upstream: rerank25 underperforms the baseline on this held-out set.

## EDP strict audit

The EDP reference is non-exhaustive. Strict Recall and strict nDCG therefore remain unavailable rather than being converted to zero or presented as complete.

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

The EDP MRR@10 values align with the independent capture to rounding, which provides a cross-check on first-relevant-rank semantics.

## Query-level forensic follow-up

The follow-up did not rerun the retriever, embedding model, or reranker. It analyzed already captured ranking artifacts.

Top-10 rank status across 30 queries:

- improved: 3
- worsened: 12
- same: 4
- gained: 1
- lost: 6
- both miss: 4

At K=5 there were 10 hit regressions and 1 hit gain. At K=10 there were 6 hit regressions and 1 hit gain.

Rerank25 introduced duplicate-text groups in `health-data`, `publish-dpo-contact`, and `bought-a-list`. The latter two co-occurred with a relevant-rank regression.

That co-occurrence is a **candidate experiment target**, not evidence that duplicate text caused the regression.

## What the beta is testing

The aggregate reranker regression was already known. The beta therefore tests a narrower service hypothesis:

> Does the independent query-level decomposition and structural forensic evidence reveal something useful beyond the already-known aggregate result?

A useful outcome can be a new observation, a clearer next experiment, a correction to the audit, or evidence that this level of external analysis is not useful.

## Suggested next experiment

Predefine a rule that collapses or deduplicates equivalent retrieval units before reranking, rerun the same held-out set, and compare:

- the three queries where new duplicate groups appeared;
- the two queries where duplicate introduction co-occurred with a rank regression;
- aggregate K=5/K=10 outcomes.

The intervention and acceptance criteria should be fixed before seeing the result.
