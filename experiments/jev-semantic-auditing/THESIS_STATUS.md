# Thesis status: Jev semantic auditing

A claim/evidence ledger. It records what the current evidence supports, qualifies, contradicts or leaves untested.
It is updated only when new frozen evidence exists. Evidence: [`phase1-governance-v1/`](./phase1-governance-v1/),
[`real-code-pilot-v1/`](./real-code-pilot-v1/), and the original whitepaper v1.1.

## Current thesis

> A separately governed typed semantic evaluator can provide useful independent probabilistic evidence about whether
> real source code satisfies explicit engineering invariants, complementing deterministic verification rather than
> replacing it.

This narrower thesis replaces, for the purpose of this ledger, the broader framing of whitepaper v1.1. It is supported
by pilot-scale evidence only.

## Supported by current pilot evidence

| Claim | Evidence | Scope |
| --- | --- | --- |
| Semantic code-policy evaluation is technically viable on a real source file | Real-code pilot: 30/30 valid typed responses on a 476-line production TypeScript module, five invariants per request | one file, one model |
| Differential evaluation (REAL / semantically equivalent CONTROL / controlled MUTANTS) produces a usable signal | Real-code pilot: three mutants moved median P(violation) by +0.59 to +0.93 with `violates` top in 5/5 repeats; CONTROL moved by at most +0.01 and kept every top option | four designed mutants |
| The evaluator can detect engineering-invariant violations that deterministic checks miss | MUTANT-01 (0.00 → 0.93) and MUTANT-03 (0.02 → 0.92) passed the typecheck and all 181 existing domain tests; both detected in 5/5 repeats | two instances in one file |
| Deterministic enforcement around a probabilistic evaluator can enforce model identity, response completeness, frozen artifacts, egress controls and provenance | Phase 1 and the pilot: 285 formal requests, every response validated against the exact questions asked and the pinned model; runs refused unless manifests match and inputs are committed; every report hash-verified and reproduced offline | two experiments, one implementation |
| Semantic evaluation is not a proof of correctness and is treated as evidence for review | Design of both experiments; observed errors below | — |

## Partially supported / qualified

| Claim | Status |
| --- | --- |
| Semantic auditing of governance text yields useful signal | Qualified. Phase 1 separated seeded contradictions and vague rules well (0 seeded defects reported as `NO_FINDING`), but its formal result is **CONFLICT**: one false `CONFLICT`, three review false positives, two injection-canary failures. |
| Jev judgments are stable across repeats | Qualified. k = 5 on one day: the pilot's top option never changed across repeats in a labelled cell; Phase 1 had 1 flip in 38 holdout evaluations and probability spreads up to 0.23. Nothing is known across days or model versions. |
| Calibrated thresholds generalize | Qualified. In Phase 1 the calibration-derived bands held for some questions and were too tight for others (a band at the minimum 0.05 produced a review false positive and a flip). |
| The original ABE results support the architecture | Qualified. They motivated it and used real code, but the enforcement defects found later mean they are not relied on as evidence here. |

## Demonstrated limitations

- **Phase-1 formal result is CONFLICT** (P4, P5, P7 of the preregistered rule).
- **Prompt-injection susceptibility observed:** in Phase 1 (`hold-11`) an instruction embedded in the judged text was
  followed (0.63–0.70 on the option it requested).
- **Insufficient-context recognition is weak:** both insufficient-context cases failed (one read as mixed agreement /
  contradiction, one as a contradiction).
- **Scoped-exception / precedence weakness:** an explicitly scoped owner approval was classified as a conflict
  resolved by precedence (`OWNER-03`).
- **Rule-kind / legal-provenance judgments are weak:** the real-code pilot missed MUTANT-04 (a profile restriction
  commented as an XRechnung requirement; 0.22 → 0.33, `satisfies` top in 5/5); in Phase 1 the authority question was
  the least reliable.
- **Only one real code file**, **only four controlled mutants**, and the mutations were **designed, not naturally
  occurring bugs**; invariants, questions, mutants and labels were written by the same implementing agent.
- **One pinned model** (`jev-1.13.0`), **k = 5**, **same-day evaluation**.
- **Synthetic-heavy governance corpora** in Phase 1 (2 verbatim real rules among the agent-written cases).
- **No mechanical repository-level authority separation:** no branch protection or CODEOWNERS; policy and evaluated
  code are separated by process and detected by manifests, not prevented.

## Not yet established

- General software correctness, or correctness of any repository as a whole.
- Regulatory or legal correctness of any rule or code.
- Production readiness.
- Universal prompt-injection resistance (injection was not tested on code at all).
- Error rates low enough for autonomous certification or merge gating.
- Generalization across repositories, languages, file types or models.
- Detection of naturally occurring defects.
- Any "Zero-Trust Certified" status.
