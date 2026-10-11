# EDP — RouteMind buyer-facing sample audit report

**Public beta case study · Independent retrieval evaluation · Finding F-RM-01**

> **Scope boundary:** This is a compact buyer-facing presentation of the already published RouteMind Beta #002 audit. It is **not** a new evaluation, a paid engagement, a general certification of production readiness, or an endorsement of EDP's commercial offer by RouteMind.

## Executive finding

**A reproducible RAG benchmark was using different success contracts in its headline comparison.**

RouteMind compared traditional retrieval, retrieval plus reranking, and document-routing walks. Retrieval `hit@10` was satisfied when **any** required `D_true` document appeared in top-10. Some temporal questions required **all** specified evidence (`needs: all`) and allowed `D_alt` substitutes; the routing/walk evaluator already enforced those conditions.

**Decision for the frozen 2026-10-02 evaluation:** The original retrieval-versus-routing headline values should **not** be treated as like-for-like under the complete-evidence contract until comparable scoring is used.

**Current status:** The RouteMind maintainer independently reproduced the finding and **addressed the benchmark/reporting discrepancy** in commit [5d8bf45](https://github.com/CSP911/routemind/commit/5d8bf455356df098c989ae71412d30a9b60e14fe), confirmed in the [public review](https://github.com/CSP911/routemind/issues/2#issuecomment-6096856584) on 2026-10-10. This is an **evaluation reporting** assessment, not a GO/HOLD verdict on deploying RouteMind.

## Quantified evidence

- **700** evaluation questions and **1,400** query/arm outcomes.
- **15** outcomes disagreed with the complete-evidence interpretation.
- **14** original hits were incomplete under the all-required-evidence rule.
- **1** original miss satisfied an explicitly accepted `D_alt`.

| Query population | Original retrieval_hit | Under complete-evidence semantics |
|---|---:|---:|
| RAG (all 700) | 361/700 = 51.57% | 355/700 = 50.71% |
| RAG + rerank (all 700) | 379/700 = 54.14% | 372/700 = 53.14% |
| `needs: all`, RAG (10 cases) | 8/10 = 80% | 1/10 = 10% |
| `needs: all`, RAG + rerank (10 cases) | 8/10 = 80% | 1/10 = 10% |

The reranker within-retrieval delta remained similar; the greater consequence was the unfair **cross-arm comparison** between retrieval and routing.

## Two reproducible query examples

1. **`t-threshold-11`:** expected both `threshold-table` and `hard-threshold-v2`. The saved top-10 had the latter but not the former. Recorded retrieval hit; incomplete under `needs: all`.
2. **`t-accrual-03`:** the saved run recorded a retrieval miss, but `accrual-rule` was a permitted `D_alt` for `leave-accrual`. Under the shared contract it was satisfied.

These examples are from the original [finding F-RM-01](../audits/routemind-beta-002/FINDING_F_RM_01.md).

## What changed after maintainer verification

The maintainer reported independently reproducing all 15 mismatches, the corrected group counts, and the four source-file SHA-256 digests.

- Main comparison updated to RAG **0.507**, RAG + rerank **0.531**, routing/walk **0.999**.
- New `retrieval_complete` metric added while preserving historical `retrieval_hit`.
- Reproducibility assertions added to `bench/attribution.py`.

**Not claimed:** improvement or deterioration in retrieval model behavior, root cause of any production answer, customer impact, or validation of an automated EDP release gate.

## Sources and full evidence

- [Original RouteMind Beta #002 audit](../audits/routemind-beta-002/README.md)
- [Frozen inputs and SHA-256s](../audits/routemind-beta-002/REPRODUCIBILITY.md)
- [Original detailed finding](../audits/routemind-beta-002/FINDING_F_RM_01.md)
- [Dated review and limitations](../audits/routemind-beta-002/MAINTAINER_FEEDBACK_2026-10-10.md)
- [Maintainer's public review](https://github.com/CSP911/routemind/issues/2#issuecomment-6096856584)
- [RouteMind corrective commit](https://github.com/CSP911/routemind/commit/5d8bf455356df098c989ae71412d30a9b60e14fe)

## How a private EDP engagement differs

A proposed **$400 fixed-scope engagement** is restricted to **up to 30 queries**, **two comparable retrieval configurations**, authorized/sanitized exports, and a **seven-day turnaround after compatible inputs are available**, subject to a fit check. The larger public RouteMind beta case is **an example of analytical method**, not a promise that the $400 package includes 700 questions or 1,400 audit rows.

Buyer deliverables can include a scoped report, per-query changes, supporting evidence, reproducibility details, limitations, and prioritized follow-up checks. This is **not** a code-implementation or model-tuning service.

**Date of buyer sample:** 2026-10-11. Evidence frozen as described in the original audit; factual status updated from 2026-10-10 maintainer review.
