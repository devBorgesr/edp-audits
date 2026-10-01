# Methodology and boundaries — DPOLens Beta Audit #001

## Source and access

Public DPOLens repository snapshot audited:

`f1cddb56835cef31c4092d99e45e49383e425bf4`

This beta used public repository/evaluation material only. No credentials, private client data, or production-system access were required.

## Historical reproduction boundary

The upstream published benchmark was associated with an earlier repository commit. The audit reproduction was performed on the snapshot listed above.

The reproduced baseline aggregate matches the published aggregate, but the audit does **not** claim byte-for-byte historical reproduction of the earlier commit.

The rerank25 rankings used for the candidate comparison were captured with a custom runner against the repository's reranker implementation. This is not claimed as native CLI reranker parity.

## Evaluation population

- 30 GDPR held-out queries.
- 867 published, effective, normative, searchable retrieval units in the audit corpus.
- baseline and candidate rankings captured query by query.

## Baseline

`convex:0.5+normative`

## Candidate

Baseline retrieval followed by `jina-reranker-v2-base-multilingual` at depth 25.

## Upstream metric semantics

DPOLens accepts an expected clause or one of its descendants as a hit.

Its reported “Recall@k” is therefore a binary per-query hit-rate under that source-system rule. It should not be conflated with EDP strict Recall.

## EDP relevance semantics

Judgments were mechanically derived from the public DPOLens held-out `expected_keys` using the upstream exact-or-descendant acceptance rule.

EDP did **not** independently re-annotate the legal correctness of those labels.

The reference is explicitly non-exhaustive.

Therefore:

- strict precision and strict MRR are reported under the EDP explicit-reference protocol;
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

A causal claim would require a predefined intervention, such as deduplicating or collapsing equivalent retrieval units before reranking and rerunning the same evaluation.

## Not evaluated

This audit does not establish:

- legal correctness of the upstream labels;
- downstream answer quality;
- citation correctness;
- generation grounding;
- production impact;
- financial impact;
- exhaustive retrieval recall;
- root cause;
- statistical generalization beyond this supplied 30-query set.

## Generated-report note

The original generated EDP report contains a generic K=1 / positive-only warning even though this case was evaluated at K=5 and K=10. It is renderer boilerplate and does not affect the calculations or interpretation in this audit.

The generated reports are preserved unchanged; this methodology document records the clarification rather than rewriting historical output.

Severity labels are auditor-assigned judgments, not measured metrics.
