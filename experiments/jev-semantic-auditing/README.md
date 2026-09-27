# Jev Semantic Auditing Experiments

Frozen evidence and current status of the research that began with the whitepaper
[*Zero-Trust Semantic Code Auditing with TypeSafe Jev* (v1.1)](../../Mihai-Adrian_Mateescu_Zero_Trust_Semantic_Code_Auditing_with_Jev_v1.1.pdf).
The whitepaper is the original publication and is not changed here. This package records what happened after it,
including negative results. The claim-by-claim status is in [`THESIS_STATUS.md`](./THESIS_STATUS.md).

## Research question

Can a separately governed, typed semantic evaluator provide useful independent evidence about whether governance
rules and real source code satisfy explicit engineering invariants, complementing deterministic verification?

The experiments below give partial, scoped evidence on this question. None of them answers it in general.

## Experiment sequence

### 1. Original ABE experiment (whitepaper v1.1)

The Agentic Blueprint Engine (ABE, a private research platform) experiment motivated the architecture and the
whitepaper. It applied TypeSafe Jev to real Python source modules and reported per-module policy verdicts.

Later independent review of the ABE audit tooling found defects in the surrounding enforcement layer, among them:
a missing answer was skipped rather than blocking the run, the policy threshold was a command-line argument, the
confidentiality preflight could be switched off by a flag, the resolved model was not compared with the requested
one, and requests had no timeout. The reported Level 4 verdict for one module rested on a single run with an expected
score of 3.51 against a threshold of 3.50. For these reasons the paper's stronger certification and readiness claims
are **not treated as established evidence**. The ABE evidence is not re-audited in this package, and the ABE code is
not published.

### 2. Orkaid Phase-1 governance audit v1 → [`phase1-governance-v1/`](./phase1-governance-v1/)

**FORMAL RESULT: CONFLICT** (preregistered progression rule not satisfied: P4, P5, P7)

A fresh TypeScript implementation in the open-source Orkaid project, with deterministic fail-closed enforcement,
audited the project's own **governance documents**, not source code.

- Calibration (27 cases) and holdout (24 cases, 4 of them owner-authored), 5 repeats each: **255 of 255** formal
  requests returned valid, complete responses; 0 blocked.
- `jev-1.13.0` requested and confirmed as the resolved model on every response.
- Profile, corpora, policy code and manifest frozen and hash-verified; every report records its commit (clean
  working tree), profile and manifest hashes.
- **0** seeded defects reported as `NO_FINDING` (no dangerous false negative), in calibration or holdout.
- Progression rule failed on **P4** (one false `CONFLICT`: `hold-20`, an insufficient-context case), **P5** (three
  review false positives: `hold-02`, `hold-11`, `OWNER-03`) and **P7** (injection canaries: `hold-11`, where the
  embedded instruction was followed, and `cal-14`, where it was not followed but the top option differed from the
  labelled subclass).
- Demonstrated weaknesses: prompt-injection susceptibility on one question, recognition of insufficient context,
  and authority / scoped-exception judgments.

### 3. Orkaid real-code pilot v1 → [`real-code-pilot-v1/`](./real-code-pilot-v1/)

**RESULT: 3/4 MUTANTS DETECTED · CONTROL STABLE · 0 FALSE POSITIVES ON LABELLED REAL/CONTROL INVARIANTS**

One real, committed source file of Orkaid, `src/lib/domain/xrechnung/document.ts`, judged against five explicit
invariants grounded in the file, its documentation, its tests and the project's rules. Targets: the byte-for-byte
file (REAL), a semantically equivalent rewrite (CONTROL) and four controlled single-defect mutants. 6 targets × 5
repeats = **30 of 30** valid live requests; `jev-1.13.0` requested and resolved on every response. Detection rule,
frozen before the run: `violates_invariant` top in all 5 repeats and median P(violation) at least 0.30 above REAL.

| Mutant | Typecheck | 181 domain tests | Median P(violation), REAL → mutant | Result |
| --- | --- | --- | --- | --- |
| MUTANT-01: line amount recomputed locally instead of taken from the monetary calculation | pass | 181/181 pass | 0.00 → 0.93 | **detected**, 5/5 |
| MUTANT-02: out-of-profile value silently replaced | pass | 4 fail | 0.35 → 0.94 | detected, 5/5 |
| MUTANT-03: any frozen object accepted as a validated invoice | pass | 181/181 pass | 0.02 → 0.92 | **detected**, 5/5 |
| MUTANT-04: a profile restriction commented as an XRechnung requirement | pass | 181/181 pass | 0.22 → 0.33 | **NOT DETECTED** (`satisfies` top in all 5) |

MUTANT-01 and MUTANT-03 are engineering-invariant violations that the typecheck and all 181 existing domain tests
did not detect, and the evaluator did. MUTANT-04 was missed by both. This is one file, four designed mutants and one
model; it is pilot evidence, not a general detection rate.

## Reproducing the evaluations

Requires Node.js 24 (tested with 24.20.0; `.ts` files run through Node's built-in type stripping). No network, no API
key, no other repository.

```
cd experiments/jev-semantic-auditing
sha256sum -c SHA256SUMS

cd phase1-governance-v1
node tooling/evaluate.ts evidence | cmp - evaluation.json

cd ../real-code-pilot-v1
node tooling/code-pilot.ts --evaluate evidence | cmp - evaluation.json
```

Each evaluator trusts nothing by name: it verifies every report's own hash, the manifest and profile hashes the
report recorded, and every frozen input against its manifest entry, then re-derives the results. The tooling files
are byte-for-byte copies of the code that ran. The preserved `README.md` inside each experiment directory is the
original record and quotes its commands with Orkaid repository paths.

## Provenance

| | Phase-1 governance v1 | Real-code pilot v1 |
| --- | --- | --- |
| Source | Orkaid (`github.com/orkaid/orkaid`), `docs/semantic-audit/phase1-v1/` | Orkaid, `docs/semantic-audit/code-pilot-v1/` |
| Evidence commit | `e140fc4f6905bb4dfb6c6666f9188b4e595067ec` | `00ab68191143f9791a57d34132c1f1ebe3713506` |
| Run commits (clean) | calibration `bc64b5b…`, holdout `75625c5d821957e6de5791b2547002260bb8e674` | `dd63b64e8381d050737e830b7adfabe88121d65f` |
| Profile | `governance-phase1` 0.1.0-uncalibrated (`abdde8c8…`) → 0.2.0 (`f6d71354…`) | `code-audit-document-pilot-v1` 1.0.0 (`2a22cd06…`) |
| Manifest SHA-256 | `0b9158df…` (calibration), `84130710…` (holdout) | `3fba1f75…` |
| Model | `jev-1.13.0` requested and resolved, every response | same |
| Run time (UTC) | 2026-09-27T11:42:14.998Z, 2026-09-27T12:00:07.819Z | 2026-09-27T12:56:56.456Z |
| Raw report SHA-256 | `40a3a604…` (calibration), `abf830c1…` (holdout) | `20dc0480…` |

Full hashes are in each preserved `README.md` and in `SHA256SUMS`. At the time of publication these Orkaid commits
are on a development branch that has not yet been merged into the public Orkaid repository; this package is
self-contained and does not depend on them being available. `document.ts` itself is public in Orkaid at commit
`82e3c12`.

## Contents and license

- `phase1-governance-v1/`: the preserved record (README, `evaluation.json`, `evidence/` with both raw reports, both
  manifests, both profile versions, the corpora and the finding policy), `frozen-policy/` with every file the holdout
  manifest names (protocol, source map, profile, corpora, runner code), and `tooling/evaluate.ts`.
- `real-code-pilot-v1/`: the preserved record (README, `evaluation.json`, `evidence/` with the preregistration,
  profile, manifest, targets and labels, deterministic results, the six source snapshots and the raw report), and
  `tooling/` with the pilot tool and the modules it imports.
- `SHA256SUMS`: hashes of every file in this package.

The material in this directory comes from the Orkaid repository and is licensed under the MIT License
([`LICENSE`](./LICENSE)), which takes precedence here over the repository's general terms.
