# Daemon Demonstrations

This page collects the most useful public-facing demonstrations in the repository. It is written for technical review: what was run, what was measured, where the artifact is, and what boundary should be attached to the result.

Implementation details are intentionally summarized at the architecture level.

## 1. High-Dimensional Black-Scholes Scalar Benchmark

Daemon ran a Black-Scholes/Feynman-Kac scalar route on IBM hardware. The workload is a high-dimensional stochastic-pricing scalar target: estimate the scalar value of a large-dimension basket digital payoff through an expectation route.

Best corrected live branch:

| Item | Value |
| --- | --- |
| Backend | `ibm_marrakesh` |
| Job | `d8c98ij8ch0s738ugrug` |
| QPU time | `3.000s` |
| Estimate | `0.6205121893` |
| Independent target | `0.6240388897` |
| Corrected error+CI | `0.055871195` |
| Projected high-dimensional MC comparison | `250.902x` |
| Report | [Black-Scholes protection ablation](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md) |

What this demonstrates:

- Daemon routes a differential-equation-derived Black-Scholes scalar target into a quantum-executable expectation route.
- The run uses real IBM hardware, reportable job IDs, QPU timing, and independent target checks.
- The benchmark compares against a projected high-dimensional brute-force MC baseline under the report's assumptions.

Boundary:

- This is a scalar PDE value route, not a full PDE surface solve.
- The speedup number belongs to this benchmark setup and baseline, not arbitrary Black-Scholes pricing.

## 2. Black-Scholes Protection Ablation

Daemon compared multiple protection and circuit routes on the same Black-Scholes scalar target.

| Rank | Method family | Corrected error+CI | QPU s | Qubits |
| ---: | --- | ---: | ---: | ---: |
| 1 | Phase-zero + sign symmetry + protected layout | `0.055871195` | `3.000` | `7` |
| 2 | Hamming phase compression + sign symmetry | `0.057348679` | `3.000` | `4` |
| 3 | Hamming + phase-zero | `0.059517201` | `3.000` | `4` |
| 5 | Hamming + TSME shelter + CONTOUR ordering | `0.083265351` | `3.000` | `5` |
| 6 | Hamming + TSME shelter + CONTOUR echo + X/XX echo | `0.10597549` | `3.000` | `5` |

Report:

[reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md)

What this demonstrates:

- Daemon measures protection schemes rather than assuming heavier protection is always better.
- Phase-zero support and sign symmetry are currently the most effective Black-Scholes scalar route.
- TSME/CONTOUR physical branches are implemented and measured, but the selector must avoid implementation tax on shallow circuits.

Boundary:

- The best branch is not the heaviest branch.
- The protection selector is part of the result: optional protections must prove they help under target-blind diagnostics.

## 3. TSME/CONTOUR Protection Hardening

The latest protection work added TSME terminal mirror decode, calibrated mirror matrix decode, auto decode selection, agreement gating, effective-shot CI correction, and no-harm selector logic.

Report:

[reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md)

What this demonstrates:

- TSME terminal mirror is now a real physical protection path with agreement diagnostics.
- Matrix decoding and postselection are both available.
- The no-harm selector disables terminal mirror when the local routed tax is too high.

Boundary:

- Forced terminal mirror did not improve the current shallow Black-Scholes route.
- The useful system behavior is selector-gated deployment, not always-on protection.

## 4. CONTOUR Deep-Window Hardware Benchmark

CONTOUR was evaluated on IBM hardware against baseline and XY4-style schedules over a range of deep windows.

Report:

[reports/validation3_hardware_summary_20260419.md](reports/validation3_hardware_summary_20260419.md)

Selected results:

| Backend | Deep-regime result |
| --- | --- |
| `ibm_fez` | CONTOUR was the best overall and deep arm in the report. |
| `ibm_marrakesh` | CONTOUR lost slightly overall but won the deeper regime. |

What this demonstrates:

- CONTOUR is not only a local simulator idea; it has live hardware evidence in drift-heavy windows.
- The strongest use case is deeper phase/drift exposure, not every shallow circuit.

Boundary:

- This is a hardware protection benchmark, not a full application workload.
- Fire Opal-level external dominance is not claimed from this report.

## 5. Fire Opal Comparison Packet

Daemon includes matched comparison reports against Q-CTRL Fire Opal-style execution.

Summary report:

[reports/fireopal_vs_pqmcf_summary_20260418.md](reports/fireopal_vs_pqmcf_summary_20260418.md)

What this demonstrates:

- The repository contains same-family comparison artifacts, provider job IDs, and boundaries.
- Fused TSME/CONTOUR branches were among the strongest Daemon raw branches in the comparison.

Boundary:

- The included summary says Fire Opal beat the raw PQMCF branches in that matched run.
- This report should be read as an external comparison artifact, not as a blanket Fire Opal dominance claim.

## 6. Method And Claim Matrix

Daemon's method map and claim boundaries are centralized in:

[reports/daemon_methods_claims_matrix_20260506.md](reports/daemon_methods_claims_matrix_20260506.md)

This is the best starting point for understanding what is currently evidence-backed versus what is still a roadmap item.

## Reviewer Path

For a short technical review:

1. Read the project overview in [README.md](README.md).
2. Read the Black-Scholes ablation report.
3. Read the protection hardening update.
4. Read the CONTOUR hardware summary.
5. Read the methods and claims matrix.

