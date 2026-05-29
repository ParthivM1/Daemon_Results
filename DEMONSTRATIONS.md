# Daemon Demonstrations

Updated 2026-05-29.

This is the quick demonstration index. It points to the actual public result artifacts and uses data-driven plots from the benchmark tables.

Full story:

[docs/evidence_story_20260529.md](docs/evidence_story_20260529.md)

## 1. Black-Scholes / Feynman-Kac Scalar Route

Daemon ran a Black-Scholes/Feynman-Kac scalar route on IBM hardware. The workload is a high-dimensional stochastic-pricing scalar target routed through a quantum-executable expectation/phase-estimation path.

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

![Black-Scholes estimates against independent target](docs/figures/black_scholes_estimates_vs_target_actual.png)

Boundary:

| Claim | Status |
| --- | --- |
| PDE-derived scalar route ran on IBM hardware | Supported by the report. |
| Full PDE surface solve | Not claimed. |
| General Black-Scholes quantum advantage | Not claimed. |
| Benchmark-specific projected comparison | Reported under the benchmark assumptions. |

## 2. Black-Scholes Protection Ablation

Daemon compared multiple protection and circuit routes on the same scalar target.

![Black-Scholes protection ablation](docs/figures/black_scholes_ablation_actual.png)

| Rank | Method family | Corrected error+CI | QPU s | Qubits |
| ---: | --- | ---: | ---: | ---: |
| 1 | Phase-zero + sign symmetry + protected layout | `0.055871195` | `3.000` | `7` |
| 2 | Hamming phase compression + sign symmetry | `0.057348679` | `3.000` | `4` |
| 3 | Hamming + phase-zero | `0.059517201` | `3.000` | `4` |
| 4 | Sign symmetry only | `0.061124328` | `3.000` | `7` |
| 5 | Hamming + TSME shelter + CONTOUR ordering | `0.083265351` | `3.000` | `5` |
| 6 | Hamming + TSME shelter + CONTOUR echo + X/XX echo | `0.10597549` | `3.000` | `5` |
| 7 | TSME terminal mirror + matrix decoder | `0.11412525` | `3.000` | `5` |
| 8 | TSME terminal mirror + auto decoder | `0.14290185` | `3.000` | `5` |

What this demonstrates:

| Module | Observed behavior |
| --- | --- |
| Phase-zero support | Included in the best current branch. |
| Sign symmetry | Included in the strongest routes and improves bias cancellation. |
| Protected layout selection | Keeps hardware routing inside the runtime decision. |
| Hamming phase compression | Reduces qubit pressure from 7 to 4 while staying close to the best branch. |
| TSME/CONTOUR/X-XX | Implemented and measured; selector-gated when overhead is too high for this shallow route. |

## 3. TSME / CONTOUR Protection Hardening

The latest protection work added TSME terminal mirror decode, calibrated mirror matrix decode, auto decode selection, agreement gating, effective-shot CI correction, and no-harm selector logic.

Report:

[reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md)

What this demonstrates:

| Feature | Meaning |
| --- | --- |
| TSME terminal mirror | A physical shelter/mirror branch exists and was tested. |
| Matrix and auto decoding | Mirror branches can be decoded through measured support behavior. |
| No-harm selector | Heavy physical protection is disabled when it worsens a shallow route. |

## 4. CONTOUR Deep-Window Hardware Benchmark

CONTOUR was evaluated on IBM hardware against baseline and XY4-style schedules over drift-sensitive windows.

![Deep-time decay curve](docs/figures/deep_time_decay_curve.png)

![Lattice scaling chart](docs/figures/lattice_scaling_bar_chart.png)

Selected aggregate results:

| Metric | Result |
| --- | ---: |
| Slots | 12 |
| Wins vs X | 12/12 |
| Wins vs BB1 | 12/12 |
| Wins vs XY4 | 11/12 |
| Mean dX | +0.1966 |
| Mean dBB1 | +0.0833 |
| Mean dXY4 | +0.0531 |

Report:

[docs/results.md](docs/results.md)

## 5. Fire Opal Comparison Packet

Daemon includes matched comparison reports against Q-CTRL Fire Opal.

![Matched Fire Opal comparison gaps](docs/figures/fireopal_matched_gaps_actual.png)

Completed matched wins include TFIM, Heisenberg, and XY-ring workloads across Marrakesh, Kingston, and Fez. The strongest observed completed gap is `+0.031709`.

Summary:

[docs/fireopal_matched_results.md](docs/fireopal_matched_results.md)

Boundary:

| Claim | Status |
| --- | --- |
| Benchmark-scoped Fire Opal wins | Supported by the matched tables. |
| Universal Fire Opal dominance | Not claimed. |
| Repeatability across all windows | Still being expanded. |

## Reviewer Path

1. Read [docs/evidence_story_20260529.md](docs/evidence_story_20260529.md).
2. Check the Black-Scholes ablation report.
3. Check the protection hardening update.
4. Check [docs/results.md](docs/results.md).
5. Check [docs/fireopal_matched_results.md](docs/fireopal_matched_results.md).

