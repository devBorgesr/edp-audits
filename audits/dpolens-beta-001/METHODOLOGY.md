# Methodology and boundaries — DPOLens Beta Audit #001

## Source

Public DPOLens repository snapshot:

`f1cddb56835cef31c4092d99e45e49383e425bf4`

## Evaluation population

- 30 GDPR held-out queries.
- 867 published, effective, normative, searchable retrieval units in the audit corpus.
- baseline and candidate rankings captured query by query.

## Baseline

`convex:0.5+normative`

## Candidate

Baseline retrieval followed by `jina-reranker-v2-base-multilingual` at depth 25.

## Relevance semantics

Judgments were mechanically derived from the public DPOLens held-out `expected_keys` using the upstream exact-or-descendant acceptance rule.

EDP did **not** independently re-annotate the legal correctness of those labels.

The reference is explicitly non-exhaustive.

Therefore:

- strict precision and strict MRR can be reported for the explicitly judged returned items;
- strict Recall and strict nDCG are not claimed.

## Forensic method

The query-level forensic step reconstructed the captured rerank25 ordering from saved reranker scores and compared:

- first relevant rank before and after reranking;
- K=5 and K=10 hit transitions;
- duplicate normalized text under distinct retrieval IDs;
- duplicate groups introduced or removed by reranking.

The forensic step did not execute the retriever, embedding model, or reranker again.

## Causality boundary

Duplicate-text groups and ranking regressions are reported as descriptive co-occurrences.

This audit does not claim that duplicate text caused the regression.

A causal claim would require a predefined intervention, such as deduplicating or collapsing equivalent retrieval units and rerunning the same evaluation.

## Not evaluated

This audit does not establish:

- legal correctness of the upstream labels;
- downstream answer quality;
- citation correctness;
- generation grounding;
- production impact;
- financial impact;
- exhaustive retrieval recall;
- root cause.

## Template note

The original generated EDP report contains a generic K=1 / positive-only warning even though this case was evaluated at K=5 and K=10. It is treated as renderer boilerplate and does not affect the calculations or interpretation in this audit.

Severity labels are auditor-assigned judgments, not measured metrics.
