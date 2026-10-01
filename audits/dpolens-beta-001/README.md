# DPOLens — EDP Beta Audit #001

**Status:** Complete — beta feedback pending  
**Scope:** retrieval + reranker  
**Source snapshot:** `f1cddb56835cef31c4092d99e45e49383e425bf4`  
**Question set:** GDPR `held_out`, 30 queries  
**Searchable audit corpus:** 867 retrieval units

## Compared configurations

**Baseline**

`convex:0.5+normative`

**Candidate**

Same retrieval pipeline followed by `jina-reranker-v2-base-multilingual` at depth 25.

## Main result

The independently reproduced reranker regression is also visible in the EDP strict audit.

| View | Baseline | rerank25 |
|---|---:|---:|
| Upstream-style Recall@5 | 0.7667 | 0.4667 |
| Upstream-style Recall@10 | 0.8333 | 0.6667 |
| Upstream-style MRR | 0.5386 | 0.3276 |
| EDP strict MRR @ 5 | 0.528889 | 0.297222 |
| EDP strict MRR @ 10 | 0.538611 | 0.327579 |

The EDP reference is explicitly **non-exhaustive**. Therefore strict Recall and strict nDCG are intentionally not claimed.

## Additional forensic observation

A deterministic query-level follow-up found three queries where rerank25 introduced a duplicate-text group into the captured top-10:

- `health-data`
- `publish-dpo-contact`
- `bought-a-list`

Two of those also co-occurred with a rank regression:

- `publish-dpo-contact`
- `bought-a-list`

This is an **observed co-occurrence**, not evidence that duplicate text caused the regression.

See [forensic/rerank25_forensic.md](forensic/rerank25_forensic.md).

## Read next

- [Executive summary](EXECUTIVE_SUMMARY.md)
- [Methodology and boundaries](METHODOLOGY.md)
- [Forensic follow-up](forensic/rerank25_forensic.md)
- [Beta feedback](FEEDBACK.md)
- [K=5 audit artifact slot](audit/k5/README.md)
- [K=10 audit artifact slot](audit/k10/README.md)
- [Checksums slot](checksums/README.md)

## Evidence labels

**Measured:** metric values calculated from the captured rankings and declared reference.

**Observed:** query-level or structural patterns visible in the captured artifacts.

**Hypothesis:** a suggested follow-up experiment. A hypothesis is not presented as a demonstrated cause.

**Not claimed:** legal correctness, downstream generation quality, production impact, exhaustive recall, exhaustive nDCG, or root cause.
