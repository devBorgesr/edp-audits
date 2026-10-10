# RouteMind — Independent Retrieval / Reranking Regression Audit
## Beta #002

**Status:** Technical audit complete; F-RM-01 confirmed by maintainer (2026-10-10)  
**Maintainer review:** received; independently reproduced and acted upon ([review](https://github.com/CSP911/routemind/issues/2#issuecomment-6096856584))  
**Audit type:** Independent Retrieval / Reranking Regression Audit  
**Evaluation snapshot:** `96be88fb94d3f50787bfe59683fb1938c134a025`  
**Corpus snapshot used for historical content inspection:** `4b26b3b68ba3168ebb5cc1087e0935c6d513d091`  
**Audit date:** 2026-10-02  
**Post-publication update:** 2026-10-10 (maintainer confirmation and impact addendum; frozen inputs unchanged)

## Executive summary

This audit first reproduced the evaluation context supplied by the RouteMind owner before looking for additional findings.

Reproduced controls:

- 700 paired retrieval queries.
- 390 cases where reranking executed.
- 310 cases where reranking did not execute.
- The known no-rerank split reproduced exactly: 287 `indirect` + 23 `temporal`.
- The aggregate reranker direction also reproduced: `rag` 361/700 (51.57%) vs `rag+rerank` 379/700 (54.14%).

Those values are treated as **known context, not new findings**.

The main additional result is a scoring-contract discrepancy on multi-document temporal questions. **As confirmed by the maintainer on 2026-10-10, its most consequential effect was the unfair headline comparison of retrieval with routing walks scored under a different completeness contract.** See the [dated maintainer review](MAINTAINER_FEEDBACK_2026-10-10.md).

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

So this finding does **not** invalidate the project's aggregate reranker conclusion. **However, the original headline retrieval-versus-walk comparison was not like-for-like.** The maintainer corrected that reporting in [commit `5d8bf45`](https://github.com/CSP911/routemind/commit/5d8bf455356df098c989ae71412d30a9b60e14fe): overall RAG **0.516 → 0.507**, RAG + rerank **0.541 → 0.531**, with walks **0.999**. The retrieval pipeline did not become worse; the comparison changed to a common scoring contract.

## Interpretation boundary

This audit does **not** label the behavior a confirmed implementation bug.

`eval/DESIGN.md` describes retrieval `hit@10` as whether **a** `D_true` document is in the ten returned, which is compatible with the current `bench/run.py` implementation.

At the same time:

- `bench/temporal.py` defines `both` as “two versions at once, `needs: all`”.
- `bench/walkscore.py` explicitly states that `needs: all` requires both documents.
- `walkscore.py` applies `D_alt`, while the retrieval scorer does not.

At initial publication (2026-10-02), the result was appropriately classified as a **metric-contract / comparability discrepancy pending maintainer intent**. On 2026-10-10 the maintainer confirmed that the asymmetry was **not intentional or previously recognized**, reproduced the 15 cases and updated the project. This confirms the discrepancy; it is **not** a claim about production answer quality.

## Final novelty check

Before publication, the repository content, issues, pull requests, and relevant commit history were checked for an explicit decision stating that retrieval scoring intentionally ignores `needs: all` or `D_alt`.

No such explicit decision was found.

The history does document `needs: all` and per-entry `D_alt` semantics for the walk scorer.

## Files

- [F-RM-01 — detailed finding](FINDING_F_RM_01.md)
- [Maintainer feedback and post-publication resolution (2026-10-10)](MAINTAINER_FEEDBACK_2026-10-10.md)
- [Reproducibility and frozen inputs](REPRODUCIBILITY.md)
- [Audit scope](AUDIT_SCOPE.txt)
- [Input SHA-256](INPUT_SHA256.txt)
- [Machine-readable mismatch cases](data/scoring_semantics_cases.csv)
- [Machine-readable summary](data/scoring_semantics_summary.json)

## Maintainer review — received 2026-10-10

CSP911 [responded publicly](https://github.com/CSP911/routemind/issues/2#issuecomment-6096856584): the distinction was **neither known nor intentional**; the maintainer independently reproduced the 15 divergences, the summary counts and the input hashes. The finding was incorporated into [commit `5d8bf45`](https://github.com/CSP911/routemind/commit/5d8bf455356df098c989ae71412d30a9b60e14fe), which aligns the headline comparison, adds `retrieval_complete` without changing historical `retrieval_hit`, and introduces reproducibility checks.

**Corrective feedback:** The EDP executive framing focused on the near-unchanged reranker delta, underplaying the more important cross-arm comparability problem between retrieval and walks. This criticism is accepted and recorded in the [dated review note](MAINTAINER_FEEDBACK_2026-10-10.md). The original CSV/JSON results and frozen-input checksums remain untouched.

**Outcome:** independently confirmed F-RM-01 with a maintainer-verified project change; **not** a paid engagement or an endorsement of every possible EDP claim.

## Discuss this audit

Technical corrections, reproduction attempts, questions about the methodology, and later maintainer feedback are welcome.

**Reddit:** [u/No_Ranger_9788](https://www.reddit.com/user/No_Ranger_9788/)
