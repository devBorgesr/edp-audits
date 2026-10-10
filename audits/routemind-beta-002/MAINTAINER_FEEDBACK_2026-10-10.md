# RouteMind Beta #002 — Maintainer review (2026-10-10)

**Scope of this note:** post-publication review of finding `F-RM-01` from the EDP independent audit dated 2026-10-02. This is not a new paid audit or a second evaluation run by EDP.

## Primary sources

- Maintainer review by CSP911: https://github.com/CSP911/routemind/issues/2#issuecomment-6096856584
- Maintainer's implementation: https://github.com/CSP911/routemind/commit/5d8bf455356df098c989ae71412d30a9b60e14fe
- Original finding: [FINDING_F_RM_01.md](FINDING_F_RM_01.md)
- Original frozen inputs: [REPRODUCIBILITY.md](REPRODUCIBILITY.md)

## Responses to the three validation questions

1. **Previously known or intentional?** The maintainer says neither. Retrieval `hit@10` retained the earlier any-`D_true` contract after temporal questions introduced `needs: all` and `D_alt` in walk scoring. The maintainer identifies no deliberate decision to keep the two scoring rules asymmetric.
2. **Technically correct?** The maintainer reports independently reproducing all 15 affected rows (14 recorded hits incomplete under the completeness contract; one recorded miss satisfied through `D_alt`), the group counts and reranker delta, and matching all four SHA-256 values.
3. **Useful and actionable?** Yes, per maintainer. The finding prompted a correction to the project's principal comparison, a new `retrieval_complete` measure alongside the unchanged legacy `retrieval_hit`, and consistency checks in `bench/attribution.py`. The maintainer explicitly credited the outside audit.

## Impact that the original executive framing underweighted

The original EDP report correctly noted that the **within-retrieval reranker delta** remains broadly stable after reinterpretation. The maintainer rightly pointed out that this framing underplayed the more significant consequence: the project's **headline comparison between retrieval arms and routing walks was not like-for-like**, because the walks already enforced `needs: all` and `D_alt` while the retrieval arms were reported under the looser any-`D_true` metric.

The commit changes the comparative README figures without claiming an improvement or regression in the retrieval system itself:

| RouteMind README metric | Previously reported | Same-contract comparison |
|---|---:|---:|
| RAG overall | 0.516 | 0.507 |
| RAG + rerank overall | 0.541 | 0.531 |
| RAG, two revisions back | 0.133 | 0.200 |
| RAG + rerank, two revisions back | 0.200 | 0.200 |

The routing/walk value remains 0.999 in that comparison. These are **scoring/reporting corrections**, not evidence that code changes improved or degraded retrieval on the frozen run.

## Confirmed code/documentation changes

In commit `5d8bf45`, the maintainer:

- documented one comparable scoring contract for all three README columns;
- added `retrieval_complete` to `bench/run.py` while preserving `retrieval_hit` for backward comparability;
- added drift checks for 15 mismatches and corrected headline values to `bench/attribution.py`;
- updated project evaluation and report documentation, crediting finding `F-RM-01`.

## Boundaries and attribution

- **Confirmed externally:** `F-RM-01` identified a previously unnoticed scoring-contract comparability discrepancy that the maintainer reproduced and addressed.
- **Not claimed:** all audit conclusions were independently revalidated, production quality improved, the project owner bought an audit, or the new attribution script represents an EDP discovery.
- The maintainer separately published `eval/runs/2026-10-10-attribution.json` for routing-failure attribution. That is RouteMind's own analysis, not the core `F-RM-01` finding.
- The original 2026-10-02 findings, hashes, CSV and JSON audit outputs are preserved. This note is a dated post-publication addendum.

**Status:** `MAINTAINER_REVIEW_RECEIVED — F-RM-01_CONFIRMED — PROJECT_CHANGE_VERIFIED` (EDP's internal evidence label; not an external certification or paid engagement).
