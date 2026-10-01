# Data policy

## Public repository scope

This repository is the public delivery and review layer for EDP audits. It is **not** a raw-data store and is separate from the EDP research runtime/laboratory repositories.

Public audit folders may contain only material that is:

- already public;
- explicitly authorized for publication; or
- sanitized so publication does not expose private client information.

## Minimum-necessary principle

Private engagements should use the smallest authorized export needed to evaluate the agreed retrieval/RAG question.

An audit should not request infrastructure credentials, model weights, or broad production access when fixed retrieval/evaluation artifacts are sufficient.

## Do not publish by default

Do not place the following in this public repository unless publication is explicitly authorized:

- private client queries;
- credentials, tokens, cookies, or connection strings;
- proprietary corpus text;
- private traces or logs;
- personal or confidential information;
- raw conversation exports;
- internal infrastructure details that are not required to understand the audit.

## Research-repository separation

Client audit material should not be moved into `edp_v5` or `lab_edp` merely because those repositories contain related research tooling.

Public client delivery belongs here in `edp-audits`; private working evidence should remain in the authorized engagement workspace.

## Public evidence

For public-source audits, machine-readable results, manifests, deterministic forensic summaries, and checksums may be published when they materially improve reproducibility.

## Private engagements

A private audit may be represented in the registry only at the level authorized by the client. The client name itself should not be published without permission.

## Corrections

If public material must be corrected, preserve the historical version where practical and publish an explicit revision or erratum.
