# RouteMind — Independent Retrieval / Reranking Regression Audit
## Beta #002

**Status:** Technical audit complete — client feedback pending  
**Audit type:** Independent Retrieval / Reranking Regression Audit  
**Evaluation snapshot:** `96be88fb94d3f50787bfe59683fb1938c134a025`  
**Corpus snapshot used for historical content inspection:** `4b26b3b68ba3168ebb5cc1087e0935c6d513d091`  
**Audit date:** 2026-10-02

## Executive summary

This audit first reproduced the evaluation context supplied by the RouteMind owner before looking for additional findings.

Reproduced controls:

- 700 paired retrieval queries.
- 390 cases where reranking executed.
- 310 cases where reranking did not execute.
- The known no-rerank split reproduced exactly: 287 `indirect` + 23 `temporal`.
- The aggregate reranker direction also reproduced: `rag` 361/700 (51.57%) vs `rag+rerank` 379/700 (54.14%).

Those values are treated as **known context, not new findings**.

The main additional result is a scoring-contract discrepancy on multi-document temporal questions.

## Main finding — F-RM-01

The temporal generator defines the `both` lever as two versions required at once with `needs: all`.

The walk scorer explicitly honors `needs: all`: when two documents are required, both must be satisfied. It also supports per-entry `D_alt` substitutions.

The retrieval scorer in `bench/run.py` uses a different contract for `retrieval_hit`: a hit is recorded when **any** `D_true` document appears in top-k. It does not apply `needs: all`, and it does not apply `D_alt` to `retrieval_hit`.

Across the committed outputs, 15 of 1,400 query/arm rows differ under the gold-completeness interpretation.

| Population | Arm | Stored result | Gold-semantic reinterpretation |
|---|---|---:|---:|
| All 700 | `rag` | 361/700 = 51.57% | 355/700 = 50.71% |
| All 700 | `rag+rerank` | 379/700 = 54.14% | 372/700 = 53.14% |
| Temporal 60 | `rag` | 35/60 = 58.33% | 29/60 = 48.33% |
| Temporal 60 | `rag+rerank` | 37/60 = 61.67% | 30/60 = 50.00% |
| `both` / `needs: all` | `rag` | 8/10 = 80% | 1/10 = 10% |
| `both` / `needs: all` | `rag+rerank` | 8/10 = 80% | 1/10 = 10% |

The aggregate reranker direction remains stable:

- Stored delta: +18/700 = **+2.57 percentage points**
- Gold-semantic reinterpretation: +17/700 = **+2.43 percentage points**

So this finding does **not** invalidate the project's aggregate reranker conclusion.

## Interpretation boundary

This audit does **not** label the behavior a confirmed implementation bug.

`eval/DESIGN.md` describes retrieval `hit@10` as whether **a** `D_true` document is in the ten returned, which is compatible with the current `bench/run.py` implementation.

At the same time:

- `bench/temporal.py` defines `both` as “two versions at once, `needs: all`”.
- `bench/walkscore.py` explicitly states that `needs: all` requires both documents.
- `walkscore.py` applies `D_alt`, while the retrieval scorer does not.

The result is therefore best described as a **metric-contract / comparability discrepancy** until the intended contract is confirmed by the project owner.

## Final novelty check

Before publication, the repository content, issues, pull requests, and relevant commit history were checked for an explicit decision stating that retrieval scoring intentionally ignores `needs: all` or `D_alt`.

No such explicit decision was found.

The history does document `needs: all` and per-entry `D_alt` semantics for the walk scorer.

## Files

- [F-RM-01 — detailed finding](FINDING_F_RM_01.md)
- [Reproducibility and frozen inputs](REPRODUCIBILITY.md)
- [Audit scope](AUDIT_SCOPE.txt)
- [Input SHA-256](INPUT_SHA256.txt)
- [Machine-readable mismatch cases](data/scoring_semantics_cases.csv)
- [Machine-readable summary](data/scoring_semantics_summary.json)

## Client feedback requested

The primary validation question is:

> Was the difference between retrieval `hit@10` and `needs: all` completeness intentional and already known, or did this audit surface a distinction you would want to change or document?

A second validation question is:

> If you had another retrieval/reranking change to investigate, would an external audit at this level of query-level and scoring-contract analysis be useful again?

Corrective or negative feedback is welcome. This beta is specifically testing whether the audit adds useful evidence beyond what the system owner already knew.
