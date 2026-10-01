# EDP Audits

Independent retrieval and RAG quality audits produced with the EDP Retrieval Quality Audit workflow.

This repository is the public delivery and review layer for audits explicitly approved for publication. Raw client data, private exports, credentials, and non-public evidence do not belong here.

## Audit registry

| Audit | System | Status | Scope |
|---|---|---|---|
| [DPOLens Beta #001](audits/dpolens-beta-001/README.md) | DPOLens | Complete — beta feedback pending | Retrieval + reranker |

## Evidence labels

Each audit distinguishes:

- **Measured** — directly calculated from supplied or independently reproduced artifacts.
- **Observed** — structural or query-level patterns present in the audited artifacts.
- **Hypothesis** — a follow-up explanation or experiment suggested by the evidence.
- **Not claimed** — conclusions the available evidence does not support.

## Versioning

Published audit artifacts are not silently rewritten. Material corrections should be documented as an erratum or a new revision so the original evidence trail remains inspectable.

## Feedback

Each audit has its own feedback page and can also be discussed through GitHub Issues using the audit feedback template.

## Data policy

See [DATA_POLICY.md](DATA_POLICY.md).

## Methodology

See [METHODOLOGY.md](METHODOLOGY.md).
