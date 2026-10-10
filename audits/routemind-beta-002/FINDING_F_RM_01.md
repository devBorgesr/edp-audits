# F-RM-01 — Retrieval scoring contract vs. gold completeness semantics

**Status (2026-10-02 original):** Candidate audit finding — independently reproduced; owner intent pending.  
**Status (2026-10-10 review):** **Confirmed scoring-contract discrepancy**, reproduced by RouteMind maintainer and addressed in project code/docs. See [maintainer review](MAINTAINER_FEEDBACK_2026-10-10.md).

## Evidence chain

1. `bench/temporal.py` generates the `both` lever as two historical versions required at once and sets `needs="all"`.
2. `bench/walkscore.py` explicitly requires all `D_true` entries when `needs == "all"` and accepts per-entry `D_alt`.
3. `bench/run.py` computes retrieval success from the existence of any `D_true` in top-k:
   - it does not branch retrieval success on `needs`;
   - it does not apply `D_alt` to `retrieval_hit`.
4. The committed run outputs contain 15 query/arm rows where these contracts produce different binary outcomes.
5. The discrepancy is concentrated in the 60 temporal questions and especially the 10 `both` questions.

## Mismatch count

- Total query/arm rows checked: **1,400**
- Semantic mismatches: **15**
- Stored hit -> incomplete under `needs: all`: **14**
- Stored miss -> satisfied by explicit `D_alt`: **1**

## Quantified effect

| Group | Arm | Stored | Gold-semantic reinterpretation |
|---|---|---:|---:|
| All 700 | rag | 361 | 355 |
| All 700 | rag+rerank | 379 | 372 |
| Temporal 60 | rag | 35 | 29 |
| Temporal 60 | rag+rerank | 37 | 30 |
| both / needs:all 10 | rag | 8 | 1 |
| both / needs:all 10 | rag+rerank | 8 | 1 |

## Example

`t-threshold-11` requires both:

- `threshold-table`
- `hard-threshold-v2`

and is marked `needs: all`.

The saved retrieval top-10 contains `hard-threshold-v2` but not `threshold-table`.

Current retrieval scoring records a hit because at least one `D_true` is present.

Under the temporal gold / walk-scoring completeness semantics, the case is incomplete because both required evidence units are not present.

## Counter-direction case

`t-accrual-03` is stored as a retrieval miss, but `accrual-rule` is an explicitly accepted `D_alt` for `leave-accrual`.

Under the per-entry alternative semantics used by the walk scorer, this case becomes satisfied.

This matters because the reinterpretation is not simply a stricter rule that lowers every metric; it corrects cases in both directions.

## Classification at initial publication (2026-10-02)

Do **not** report this as a confirmed implementation bug without maintainer confirmation.

There is evidence for both interpretations:

- Current code and `eval/DESIGN.md` are consistent with an **any-D_true retrieval metric**.
- The temporal generator and walk scorer are consistent with an **all-required-evidence completeness metric** for `needs: all`.

The audit finding is therefore the **unresolved semantic asymmetry and its quantified effect**, not a claim about which contract was intended.

## Recommended resolution

Expose both concepts explicitly:

- `hit@10_any`: at least one acceptable evidence document appears in top-10.
- `complete@10`: all evidence required by `needs: all` is satisfied, with `D_alt` honored per required entry.

This preserves the existing retrieval metric while making answer-completeness semantics directly measurable.

No corrected MRR is claimed here because multi-document rank semantics must be specified separately.

## Post-publication addendum — 2026-10-10

[CSP911 confirmed](https://github.com/CSP911/routemind/issues/2#issuecomment-6096856584) the metric-contract asymmetry was not previously known or intentional and independently reproduced the same 15 rows, group counts, reranker delta and matching SHA-256s. The finding is **confirmed as a cross-arm scoring comparability discrepancy**; this is not a claim that the retrieval model itself regressed or that every response is correct/incorrect.

**Reframed materiality:** The original audit emphasized that the within-retrieval reranker delta remains similar, but the bigger consequence was an invalid like-for-like comparison in RouteMind's headline retrieval-versus-walk table: retrieval `hit@10` and walk completeness had been calculated with different evidence requirements. The maintainer explicitly identified this underweighting as critical feedback.

**Resolution:** [RouteMind commit `5d8bf45`](https://github.com/CSP911/routemind/commit/5d8bf455356df098c989ae71412d30a9b60e14fe) revises overall reported retrieval values from `0.516/0.541` to `0.507/0.531`, retains the routing/walk rate `0.999`, adds `retrieval_complete` alongside `retrieval_hit`, and asserts the corrected summary in `bench/attribution.py`. Historical saved runs were not rewritten.

The original 2026-10-02 evidence and recommendation remain visible above as historical record. Full feedback and boundaries: [MAINTAINER_FEEDBACK_2026-10-10.md](MAINTAINER_FEEDBACK_2026-10-10.md).
