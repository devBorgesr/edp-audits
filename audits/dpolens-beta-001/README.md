# DPOLens — EDP Beta Audit #001

**Status:** Technical analysis complete — beta feedback pending  
**Publication status:** narrative + forensic summary published; exact machine-generated K=5/K=10 outputs and checksum file still need to be mirrored byte-for-byte from the validated delivery package before this page is treated as the final public package.  
**Scope:** retrieval + reranker  
**Source snapshot audited:** `f1cddb56835cef31c4092d99e45e49383e425bf4`  
**Question set:** GDPR `held_out`, 30 queries  
**Searchable audit corpus:** 867 retrieval units

## What this beta used

This beta used the **public DPOLens repository and public evaluation artifacts**. No infrastructure credentials, private client data, or production access were required.

The goal was not to rediscover the already-known fact that the reranker performs worse on the published held-out set. The useful question is whether an independent audit can add **query-level evidence and a clearer next experiment**.

## Compared configurations

**Baseline**

`convex:0.5+normative`

**Candidate**

The same retrieval pipeline followed by `jina-reranker-v2-base-multilingual` at depth 25.

## Main result

The current-snapshot reproduction confirms the aggregate reranker regression and the EDP audit shows the same direction at K=5 and K=10.

| View | Baseline | rerank25 |
|---|---:|---:|
| DPOLens-style Recall@5 | 0.7667 | 0.4667 |
| DPOLens-style Recall@10 | 0.8333 | 0.6667 |
| DPOLens-style MRR | 0.5386 | 0.3276 |
| EDP strict MRR @ 5 | 0.528889 | 0.297222 |
| EDP strict MRR @ 10 | 0.538611 | 0.327579 |

**Metric boundary:** DPOLens-style “Recall@k” is the upstream binary hit-rate under its expected-key / descendant acceptance rule. It is not the same metric as EDP strict Recall.

The EDP relevance reference is explicitly **non-exhaustive**. Therefore EDP strict Recall and strict nDCG are intentionally not claimed.

## What was already known

DPOLens already documented that this reranker configuration underperformed the baseline. This audit does **not** present the aggregate regression as a new discovery.

## Additional query-level observation

A deterministic forensic follow-up found three queries where rerank25 introduced a duplicate-text group into the captured top-10:

- `health-data`
- `publish-dpo-contact`
- `bought-a-list`

Two of those also co-occurred with a relevant-rank regression:

- `publish-dpo-contact`
- `bought-a-list`

This is an **observed co-occurrence, not a causal claim**.

See [forensic/rerank25_forensic.md](forensic/rerank25_forensic.md).

## Suggested next experiment

A useful follow-up would be to predefine a deduplication/collapse rule for equivalent retrieval units **before reranking**, rerun the same held-out set, and compare the affected queries plus aggregate metrics.

That experiment would test whether duplicate candidate competition contributes to the observed regressions. The current audit alone does not establish that it does.

## Start here

1. [Executive summary](EXECUTIVE_SUMMARY.md) — result, what was known, and what the audit adds.
2. [Methodology and boundaries](METHODOLOGY.md) — population, reference semantics, reproduction limits, and non-claims.
3. [Forensic follow-up](forensic/rerank25_forensic.md) — deterministic query-level evidence.
4. [Beta feedback](FEEDBACK.md) — tell us what was already known, new/useful, incorrect, or worth pursuing.

## Machine artifacts

The locally validated package contains the original generated K=5/K=10 reports, manifests, results, reproduction evidence, forensic outputs, and hashes.

The public mirror must copy those files **byte-for-byte** rather than reconstructing them from this prose. Until that mirror is complete, the narrative documents above are the review surface and the local validated package remains the canonical machine-artifact bundle.

## Evidence labels

**Measured:** metric values calculated from captured rankings and the declared reference.

**Observed:** query-level or structural patterns visible in captured artifacts.

**Hypothesis:** a suggested follow-up experiment. A hypothesis is not presented as a demonstrated cause.

**Not claimed:** legal correctness, downstream generation quality, production impact, exhaustive retrieval recall, exhaustive nDCG, or root cause.
