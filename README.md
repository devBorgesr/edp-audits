# EDP Audits

**Independent retrieval and RAG-quality audits built around explicit evidence, reproducible calculations, and bounded claims.**

This is the **client-facing delivery and review repository**. It is separate from the EDP research runtime and laboratory repositories.

## What an audit is designed to answer

Given a representative query set and retrieval outputs, an audit can help determine:

- where relevant material is missed or moved down the ranking;
- whether a candidate retrieval/reranking change improves or regresses the supplied sample;
- which query-level failure patterns deserve the next experiment;
- which conclusions are supported by the evidence — and which are not.

An audit does **not** require access to model weights or infrastructure credentials. For private engagements, the preferred input is the smallest authorized export needed to evaluate retrieval behavior.

## What the client receives

A delivery can include:

- an executive summary;
- a technical methodology and scope statement;
- machine-readable audit results and manifests;
- query-level failure / regression analysis;
- deterministic forensic follow-up where useful;
- hashes/checksums and source snapshot identifiers;
- an explicit list of limitations and non-claims;
- a feedback path for corrections and usefulness validation.

## Privacy boundary

This public repository is **not a raw-data repository**.

Raw private queries, proprietary corpus text, credentials, cookies, private traces, and non-public exports do not belong here unless a client has explicitly authorized publication. Public audit pages should contain only public, authorized, or sanitized evidence.

See [DATA_POLICY.md](DATA_POLICY.md).

## Audit registry

| Audit | System | Audit status | Maintainer review | Scope |
|---|---|---|---|---|
| [DPOLens Beta #001](audits/dpolens-beta-001/README.md) | DPOLens | Complete | Requested; delivery acknowledged; no substantive post-audit review received as of 2026-10-08 | Retrieval + reranker |
| [RouteMind Beta #002](audits/routemind-beta-002/README.md) | RouteMind | Complete | Requested; no substantive post-audit review received as of 2026-10-08 | Retrieval + reranker scoring semantics |

## Evidence labels

Each audit distinguishes:

- **Measured** — directly calculated from supplied or independently reproduced artifacts.
- **Observed** — structural or query-level patterns present in the audited artifacts.
- **Hypothesis** — a follow-up explanation or experiment suggested by the evidence.
- **Not claimed** — conclusions the available evidence does not support.

## Method discipline

A relevance reference is not automatically ground truth. Non-exhaustive judgments stay non-exhaustive; metrics that require exhaustive relevance are not silently manufactured.

A/B observations describe the supplied paired sample. They do not establish root cause, production impact, business severity, or generalization unless the evidence was designed to support those claims.

See [METHODOLOGY.md](METHODOLOGY.md).

## Versioning

Published audit artifacts are not silently rewritten. Material corrections should be documented as an erratum or a new revision so the evidence trail remains inspectable.

## Feedback

Maintainer review is tracked separately from audit completion. If no substantive post-audit review is received, the audit remains complete and the review status is recorded explicitly. Lack of maintainer feedback is not treated as endorsement or rejection of the findings.

Each audit can still keep a feedback path open for later corrections or usefulness validation.
