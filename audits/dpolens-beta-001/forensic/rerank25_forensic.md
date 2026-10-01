# DPOLens rerank25 — query-level forensic

Independent follow-up over the already captured baseline and rerank25 rankings. No retriever, embedding model, or reranker was executed again.

**Important:** duplicate/rank relationships below are descriptive co-occurrences, not a causal claim.

## Summary

- Queries: 30
- Top-10 rank status: improved 3, worsened 12, same 4, gained 1, lost 6, both miss 4
- K=5 hit status: regression 10, gain 1, both hit 13, both miss 6
- K=10 hit status: regression 6, gain 1, both hit 19, both miss 4

Baseline queries with duplicate text:

- `fix-wrong-address`
- `dpia-large-scale`
- `bought-a-list`
- `profiling-meaning`
- `legitimate-interests-test`

Rerank25 queries with duplicate text:

- `fix-wrong-address`
- `health-data`
- `publish-dpo-contact`
- `bought-a-list`
- `profiling-meaning`
- `legitimate-interests-test`

Rerank25 introduced a duplicate-text group in:

- `health-data`
- `publish-dpo-contact`
- `bought-a-list`

Introduced duplicate + rank-regression co-occurrence:

- `publish-dpo-contact`
- `bought-a-list`

## Query-level cases of interest

| query | baseline relevant rank | rerank25 relevant rank | post-rerank rank in captured top50 | K5 | K10 | new duplicate group |
|---|---:|---:|---:|---|---|---|
| `health-data` | — | — | — | both miss | both miss | yes |
| `publish-dpo-contact` | 1 | 6 | 6 | regression | both hit | yes |
| `bought-a-list` | 3 | 4 | 4 | both hit | both hit | yes |
| `breach-internal-record` | 4 | — | 25 | regression | regression | no |
| `send-data-to-competitor` | 2 | — | 14 | regression | regression | no |
| `plain-language` | 4 | — | 12 | regression | regression | no |
| `two-companies-sharing` | 1 | — | 16 | regression | regression | no |

## Introduced duplicate-text groups

### health-data

Rank status: `both_miss`; K5 `both_miss`; K10 `both_miss`.

Ranks 9 and 10:

- `gdpr:art-13:para-2:pt-a`
- `gdpr:art-14:para-2:pt-a`

Shared text:

> the period for which the personal data will be stored, or if that is not possible, the criteria used to determine that period;

### publish-dpo-contact

Rank status: `worsened`; K5 `regression`; K10 `both_hit`.

Ranks 2 and 3:

- `gdpr:art-13:para-1:pt-b`
- `gdpr:art-14:para-1:pt-b`

Shared text:

> the contact details of the data protection officer, where applicable;

The first relevant item moved from rank 1 to rank 6.

### bought-a-list

Rank status: `worsened`; K5 `both_hit`; K10 `both_hit`.

Ranks 3 and 4:

- `gdpr:art-13:para-1:pt-a`
- `gdpr:art-14:para-1:pt-a`

Shared text:

> the identity and the contact details of the controller and, where applicable, of the controller's representative;

The first relevant item moved from rank 3 to rank 4.

## Interpretation boundary

The reranker is already known to reduce retrieval quality on this held-out set.

This forensic step asks whether rank regressions co-occur with additional observable structure such as duplicate text. Co-occurrence can justify a follow-up experiment, but it is not evidence that duplication caused the regression.
