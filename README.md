# EDP Audits

**Independent RAG regression audits for retrieval changes, release risk, and “this worked before” incidents.**

This is the **client-facing delivery and review repository** for EDP audits. It is separate from the EDP research runtime and laboratory repositories.

The goal is not to produce another aggregate score. The goal is to determine **which queries changed, what evidence changed, whether the regression matters, and what the available artifacts actually support**.

## Typical use cases

An EDP audit is most useful when:

- a RAG system “worked before” and a team needs a reproducible before/after investigation;
- a reranker, embedding, chunking, hybrid-retrieval, query-rewriting, or packing change improves the average but may hide query-level regressions;
- a team needs evidence that version B is safer or more useful than version A on a fixed evaluation cohort;
- retrieval succeeds but evidence may be displaced during reranking or dropped during final context construction;
- the corpus changed and the team needs to separate pipeline regression from corpus/evaluation drift;
- a client or release process needs an independent evidence package rather than another architecture opinion.

## Evaluation contract

A meaningful paired comparison should freeze or explicitly identify, where applicable:

- the query cohort;
- the corpus snapshot;
- the pipeline/configuration version;
- the embedding and index/build version;
- the retrieval cutoff and/or context budget;
- the relevance / expected-evidence contract;
- the execution status for every expected query.

For pipelines with stochastic stages — for example LLM query rewriting, HyDE, multi-query expansion, or agentic routing — run-to-run stability should be measured before attributing observed state changes to a candidate modification.

## What EDP distinguishes

Depending on the artifacts available, an audit can distinguish:

- **Recovery** — the baseline failed and the candidate succeeds.
- **Regression** — the baseline succeeded and the candidate fails.
- **Shared failure** — both versions fail under the same frozen contract.
- **Execution failure** — timeout, error, or missing output rather than a completed relevance failure.
- **Evidence eviction** — relevant evidence was retrieved but did not survive a later reranking / packing / context boundary.
- **Rank-only movement** — ranking changed while usable evidence remained available.
- **Corpus / eval drift** — the evaluation target changed, so an apparent regression may not be attributable to the pipeline change alone.

Not every audit contains every layer. EDP reports only what the available evidence can support.

## What an audit is designed to answer

Given a representative query set and comparable retrieval outputs, an audit can help determine:

- where relevant material is missed or moved down the ranking;
- which exact queries were recovered or regressed;
- which failure classes deserve the next experiment;
- where the available artifacts support stage-level investigation;
- which conclusions are measured, observed, hypothesized, or not supported.

An audit does **not** require access to model weights or infrastructure credentials. For private engagements, the preferred input is the smallest authorized export needed to evaluate behavior.

## What the client receives

A delivery can include:

- an executive summary;
- a technical methodology and scope statement;
- machine-readable audit results and manifests;
- query-level recovery / regression analysis;
- failure slices and deterministic forensic follow-up where useful;
- hashes/checksums and source snapshot identifiers;
- an explicit list of limitations and non-claims;
- a reproducibility package and a path for corrections.

## Privacy boundary

This public repository is **not a raw-data repository**.

Raw private queries, proprietary corpus text, credentials, cookies, private traces, and non-public exports do not belong here unless a client has explicitly authorized publication. Public audit pages should contain only public, authorized, or sanitized evidence.

See [DATA_POLICY.md](DATA_POLICY.md).

## Audit registry

| Audit | System | Audit status | Maintainer review | Scope |
|---|---|---|---|---|
| [DPOLens Beta #001](audits/dpolens-beta-001/README.md) | DPOLens | Complete | Requested; delivery acknowledged; no substantive post-audit review received as of 2026-10-08 | Retrieval + reranker |
| [RouteMind Beta #002](audits/routemind-beta-002/README.md) | RouteMind | Complete; **F-RM-01 confirmed** | **Received 2026-10-10**: reproduced by maintainer; [finding incorporated in code](https://github.com/CSP911/routemind/commit/5d8bf455356df098c989ae71412d30a9b60e14fe) | Retrieval + reranker scoring semantics; cross-arm comparability |

## Evidence labels

Each audit distinguishes:

- **Measured** — directly calculated from supplied or independently reproduced artifacts.
- **Observed** — structural or query-level patterns present in the audited artifacts.
- **Hypothesis** — a follow-up explanation or experiment suggested by the evidence.
- **Not claimed** — conclusions the available evidence does not support.

## Method discipline

A relevance reference is not automatically ground truth. Non-exhaustive judgments stay non-exhaustive; metrics that require exhaustive relevance are not silently manufactured.

A/B observations describe the supplied paired sample. They do not establish root cause, production impact, business severity, or generalization unless the evidence was designed to support those claims.

A severe regression or release-blocking threshold is a **scoped decision rule**, not something inferred from an aggregate metric alone.

See [METHODOLOGY.md](METHODOLOGY.md).

## For teams and RAG builders

If you already have a fixed evaluation cohort and two comparable retrieval / reranking configurations, you can send the public repository or a sanitized artifact/schema description.

A fit check first determines whether the available artifacts can support a defensible paired audit. **No paid paired analysis starts during the fit check.**

**Discuss / contact:** [u/No_Ranger_9788 on Reddit](https://www.reddit.com/user/No_Ranger_9788/)

## Versioning

Published audit artifacts are not silently rewritten. Material corrections should be documented as an erratum or a new revision so the evidence trail remains inspectable.

## Feedback

Maintainer review is tracked separately from audit completion. If no substantive post-audit review is received, the audit remains complete and the review status is recorded explicitly. Lack of maintainer feedback is not treated as endorsement or rejection of the findings.

Technical corrections, reproduction attempts, and usefulness feedback remain welcome after publication.
