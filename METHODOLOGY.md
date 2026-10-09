# EDP public audit methodology

## Purpose

EDP audits retrieval and ranking changes from explicit exported evidence. The public registry is designed to make audit scope, calculations, limitations, and follow-up observations inspectable.

The central question is not only whether an aggregate metric moved. It is whether the evidence supports a defensible statement about **which cases changed, how they changed, and what should be investigated next**.

## Scope before comparison

A deviation is only meaningful relative to a defined evaluation target.

Before a paired comparison, the audit should freeze or explicitly identify, where applicable:

- query cohort;
- corpus snapshot;
- pipeline/configuration version;
- embedding model and index/build version;
- retrieval cutoff and/or context budget;
- relevance / expected-evidence contract;
- expected query IDs and execution-status handling.

If any of those differ between runs, the audit must state the difference rather than silently attributing all movement to the candidate pipeline.

## Run-to-run stability

A frozen configuration is not always deterministic.

When stochastic stages are present — for example LLM query rewriting, HyDE, multi-query expansion, or agentic routing — an A/A stability control may be needed before interpreting A/B differences.

Where feasible, repeated baseline runs can be used to estimate:

- query state-flip rate;
- rank / evidence movement;
- missing / timeout / execution variance;
- class-specific instability.

Candidate effects that sit inside observed run-to-run variance should not be presented as cleanly attributable regressions without additional evidence.

## Separation of layers

A public audit should keep these layers distinct:

1. **Source-system semantics** — the upstream system's own retrieval and evaluation rules.
2. **Independent reproduction** — when feasible, the source benchmark is rerun or independently reconstructed.
3. **Paired regression analysis** — fixed baseline/candidate evidence is compared query-by-query.
4. **Forensic follow-up** — deterministic case inspection used only where the available artifacts support a deeper explanation.

Forensic analysis is optional. A scoped release decision may already be possible from the evaluation contract and paired results without assigning a causal explanation to every deviation.

## Reference discipline

A relevance reference is not automatically ground truth.

When the supplied reference is non-exhaustive, metrics that require an exhaustive reference must remain unavailable rather than being silently converted to zero or treated as complete.

## Paired transition discipline

Where the artifacts support it, EDP distinguishes transitions such as:

- baseline failure → candidate success (**recovery**);
- baseline success → candidate failure (**regression**);
- failure in both runs (**shared failure**);
- expected query → timeout/error/missing output (**execution failure**);
- evidence retrieved → evidence absent from final retained context (**evidence eviction**);
- rank movement while the required evidence remains usable (**rank-only movement**).

These categories are descriptive. They do not automatically establish cause.

## Comparison discipline

A/B results describe the supplied paired sample. They do not establish root cause, production impact, statistical generalization, or business severity unless the audit contains evidence designed for those claims.

Retrieval and generation failures should remain separate when the artifacts allow that distinction. A worse answer with unchanged ranked evidence is not the same failure as a retrieval regression.

## Severity and release rules

Severity labels are scoped judgments, not universal metrics.

A team may define release-blocking behavior by query class, evidence survival, business criticality, traffic, or another predeclared rule. EDP should preserve that rule in the audit scope rather than inventing a threshold after seeing the results.

## Reproducibility

Where possible, an audit publishes:

- source/corpus snapshot or commit;
- pipeline/configuration identifiers;
- input and output hashes;
- machine-readable paired results;
- generated reports;
- deterministic follow-up scripts;
- checksums for packaged artifacts.

## Immutability

Generated audit outputs should be preserved as originally produced. Template defects or interpretation clarifications should be recorded separately rather than silently editing historical evidence.
