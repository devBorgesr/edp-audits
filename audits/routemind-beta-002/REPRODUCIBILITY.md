# Reproducibility note

## Frozen inputs

Evaluation snapshot:

`96be88fb94d3f50787bfe59683fb1938c134a025`

Historical corpus snapshot used for content inspection:

`4b26b3b68ba3168ebb5cc1087e0935c6d513d091`

## Input SHA-256

```text
54bc34d32689104fc2497a57e66faaa8df00065d102bf5db75b0083eb2c64e6a  eval/gold/hard.yaml
e226efe3c93af11e1c0abac37dfa379890cb0161fbfaa6dbbc197a742d0d57e1  eval/gold/hard-temporal.yaml
1f15201edf2194e50b0a1a451d921fe64c36b8ca6e5b56d8599ee8f5f1f0cd89  eval/runs/2026-09-20b-retrieval-hard.json
c449f74c2a295c66e434be123446cef5c538f393bf2d034418f7fb3bb2d795cb  eval/runs/2026-09-20b-retrieval-temporal.json
```

## Reproduced known context

```text
paired queries           700
reranker executed        390
reranker not executed    310
known no-rerank split    287 indirect + 23 temporal
```

These values were supplied before the audit and are treated as reproduction controls, not discoveries.

## Audit boundary

Only saved top-10 ordering is used for query-level rank claims. No score margins are inferred. No generation-stage claims are made. No corrected multi-document MRR is reported.
