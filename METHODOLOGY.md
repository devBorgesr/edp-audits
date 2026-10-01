# EDP public audit methodology

## Purpose

EDP audits retrieval and ranking behavior from explicit exported evidence. The public registry is designed to make audit scope, calculations, limitations, and follow-up observations inspectable.

## Separation of layers

A public audit should keep these layers distinct:

1. **Source-system semantics** — the upstream system's own retrieval and evaluation rules.
2. **Independent reproduction** — when feasible, the source benchmark is rerun or independently reconstructed.
3. **EDP audit** — EDP analyzes fixed exported rankings, references, and metadata.
4. **Forensic follow-up** — deterministic query-level inspection used to form testable follow-up hypotheses.

## Reference discipline

A relevance reference is not automatically ground truth.

When the supplied reference is non-exhaustive, metrics that require an exhaustive reference must remain unavailable rather than being silently converted to zero or treated as complete.

## Comparison discipline

A/B results describe the supplied paired sample. They do not establish root cause, production impact, statistical generalization, or business severity unless the audit contains evidence designed for those claims.

## Severity

Severity labels are auditor-assigned judgments, not measured metrics.

They should be interpreted together with the evidence class, scope, and limitations stated in the individual audit.

## Reproducibility

Where possible, an audit publishes:

- source snapshot or commit;
- input and output hashes;
- machine-readable results;
- generated reports;
- deterministic follow-up scripts;
- checksums for packaged artifacts.

## Immutability

Generated audit outputs should be preserved as originally produced. Template defects or interpretation clarifications should be recorded separately rather than silently editing historical evidence.
