# Daemon Demonstrations

Updated 2026-05-29.

This page is the public demo index for the Daemon results repository. It is designed for a fast technical reader: what was run, what the result shows, where the raw report lives, and what boundary should be attached to the claim.

For the full narrative, start here:

[docs/daemon_results_story_20260529.md](docs/daemon_results_story_20260529.md)

## 1. Stack Overview

Daemon is a route-aware quantum runtime stack. It is not only a transpiler, only a circuit compressor, or only a postprocessor.

![Daemon stack story](docs/figures/daemon_stack_story.svg)

The public evidence currently covers four lanes:

| Lane | What it demonstrates | Primary artifact |
| --- | --- | --- |
| Fire Opal matched comparisons | Daemon has benchmark-scoped live IBM wins against Fire Opal on matched workloads. | [docs/fireopal_matched_results.md](docs/fireopal_matched_results.md) |
| CONTOUR deep-time protection | CONTOUR preserves signal better than baseline and standard schedules in deeper drift-sensitive windows. | [docs/results.md](docs/results.md) |
| Black-Scholes scalar route | Daemon connects protected execution to a PDE-derived Black-Scholes/Feynman-Kac scalar target. | [Black-Scholes ablation report](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md) |
| TSME/CONTOUR hardening | Daemon records which heavier protection branches help or hurt and uses no-harm selection. | [Protection hardening update](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md) |

## 2. Demonstration A: Matched Fire Opal Comparisons

This demo compares Daemon/PQMCF branches against Q-CTRL Fire Opal on matched IBM workloads.

![Fire Opal gap chart](docs/figures/fireopal_gap_bar.svg)

Completed matched wins:

| Workload | Backend | Daemon / PQMCF best | Fire Opal best | Gap |
| --- | --- | ---: | ---: | ---: |
| TFIM mixed n16 v5 | IBM Marrakesh | 0.904184 | 0.872475 | +0.031709 |
| TFIM mixed n16 v4 | IBM Kingston | 0.921005 | 0.897435 | +0.023570 |
| TFIM mixed n16 v11 | IBM Fez | 0.917041 | 0.906602 | +0.010439 |
| Heisenberg mixed n16 v1 | IBM Marrakesh | 0.932244 | 0.923357 | +0.008887 |
| Heisenberg mixed n16 v2 | IBM Kingston | 0.929821 | 0.925930 | +0.003891 |
| XY ring n16 v1 | IBM Kingston | 0.971454 | 0.967945 | +0.003509 |
| XY ring n16 v8 | IBM Fez | 0.979066 | 0.975757 | +0.003309 |
| XY ring n16 v4 | IBM Marrakesh | 0.974682 | 0.972600 | +0.002082 |

Repeatability reruns also include Fire Opal wins:

| Workload | Backend | Daemon / PQMCF best | Fire Opal best | Gap |
| --- | --- | ---: | ---: | ---: |
| TFIM mixed n16 v6 repeat | IBM Marrakesh | 0.882243 | 0.895440 | -0.013197 |
| XY ring n16 v12 repeat | IBM Marrakesh | 0.970332 | 0.975651 | -0.005319 |

What this shows:

| Point | Interpretation |
| --- | --- |
| Strongest completed gap | Daemon led Fire Opal by +3.1709 percentage points on TFIM mixed n16 v5. |
| Cross-backend coverage | Completed wins include Marrakesh, Kingston, and Fez. |
| Boundary | This is benchmark-scoped evidence, not a universal Fire Opal dominance claim. |

## 3. Demonstration B: CONTOUR Deep-Time Hardware Protection

This demo isolates Daemon's drift/phase protection behavior in deeper windows where unprotected schedules decay.

![CONTOUR deep-window chart](docs/figures/contour_deep_summary.svg)

Earlier Torino summary:

| Metric | Value |
| --- | ---: |
| Slots | 12 |
| Wins vs X | 12/12 |
| Wins vs BB1 | 12/12 |
| Wins vs XY4 | 11/12 |
| Mean gain vs X | +0.1966 |
| Mean gain vs BB1 | +0.0833 |
| Mean gain vs XY4 | +0.0531 |
| Mean CONTOUR fidelity | 0.2669 |
| Mean no-drift ceiling | 0.2829 |

Deep-only confirmation:

| Comparison | Result |
| --- | --- |
| CONTOUR vs X | 6/6 wins |
| CONTOUR vs BB1 | 6/6 wins |
| CONTOUR vs XY4 | 6/6 wins |
| Mean absolute gain vs XY4 | +0.0423 |

Primary reports:

| Artifact | Link |
| --- | --- |
| Public results summary | [docs/results.md](docs/results.md) |
| Deep check today 2 | [docs/deep_check_today2.md](docs/deep_check_today2.md) |
| Deep check today 5 | [docs/deep_check_today5.md](docs/deep_check_today5.md) |
| Marrakesh deep run | [docs/marrakesh_deep_today6.md](docs/marrakesh_deep_today6.md) |

## 4. Demonstration C: Black-Scholes / Feynman-Kac Scalar Route

This is the newer application-layer demonstration. The target is a scalar value route derived from a high-dimensional Black-Scholes basket PDE through a Feynman-Kac expectation.

Public route:

```text
high-dimensional Black-Scholes basket PDE
-> Feynman-Kac scalar expectation
-> phase-estimator quantum route
-> protected IBM execution
-> independent target check
-> evidence report
```

Best corrected live branch in the current ablation:

| Field | Value |
| --- | --- |
| Backend | IBM Marrakesh |
| Job | `d8c98ij8ch0s738ugrug` |
| QPU time | `3.000s` |
| Estimate | `0.6205121893` |
| Independent target | `0.6240388897` |
| Corrected error+CI | `0.055871195` |
| Projected high-dimensional MC comparison | `250.902x` |

Boundary:

| Claim | Status |
| --- | --- |
| PDE-derived scalar route ran on IBM hardware | Supported by the report. |
| Full PDE surface solve | Not claimed. |
| General Black-Scholes quantum advantage | Not claimed. |
| Benchmark-specific projected comparison | Reported under the benchmark assumptions. |

Primary report:

[reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md)

## 5. Demonstration D: Black-Scholes Protection Ablation

This demo is important because it separates "having a protection scheme" from "blindly applying every scheme." The best branch is currently light protection; heavier branches are implemented, measured, and selector-gated.

![Black-Scholes protection ablation](docs/figures/black_scholes_ablation_error.svg)

Ablation ranking:

| Rank | Method family | Corrected error+CI | QPU s | Qubits |
| ---: | --- | ---: | ---: | ---: |
| 1 | Phase-zero + sign symmetry + protected layout | 0.055871195 | 3.000 | 7 |
| 2 | Hamming phase compression + sign symmetry | 0.057348679 | 3.000 | 4 |
| 3 | Hamming + phase-zero | 0.059517201 | 3.000 | 4 |
| 4 | Sign symmetry only | 0.061124328 | 3.000 | 7 |
| 5 | Hamming + TSME shelter + CONTOUR ordering | 0.083265351 | 3.000 | 5 |
| 6 | Hamming + TSME shelter + CONTOUR echo + X/XX echo | 0.105975490 | 3.000 | 5 |
| 7 | TSME terminal mirror + matrix decoder | 0.114125250 | 3.000 | 5 |
| 8 | TSME terminal mirror + auto decoder | 0.142901850 | 3.000 | 5 |

Interpretation:

| Result | Meaning |
| --- | --- |
| Phase-zero + sign symmetry is best here | The shallow Black-Scholes route currently benefits most from low-tax bias cancellation. |
| Hamming compression stays competitive | The compressed branch materially reduces qubit pressure while staying close to the best branch. |
| Heavier TSME/CONTOUR branches are not forced | The selector treats implementation tax as real and avoids overprotection when it hurts. |
| Hardening work remains useful | The heavier branches are available for deeper/noisier route families where their protection may be worth the tax. |

## 6. Demonstration E: TSME/CONTOUR/X-XX Hardening

The latest hardening pass adds protection branches and no-harm selection logic for the Black-Scholes route.

Implemented branches:

| Branch | Purpose |
| --- | --- |
| TSME terminal mirror decode | Adds a terminal semantic consistency check before decoding. |
| Calibrated two-bit mirror matrix decode | Uses a calibrated branch-specific decoder rather than a fixed terminal readout rule. |
| Auto decoder selection | Chooses the lower-risk decoder path from measured branch behavior. |
| Mirror agreement gating | Rejects mirror branches when internal agreement is poor. |
| Effective-shot confidence correction | Accounts for reduced effective sample quality after filtering/gating. |
| No-harm selector | Avoids deploying heavy protection when diagnostics predict the implementation tax is too high. |

Primary report:

[reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md)

## 7. Combined Readout

The repository should be read as a combined evidence package:

| Evidence lane | Strongest public signal | Current boundary |
| --- | --- | --- |
| Fire Opal comparisons | Multiple completed matched wins, strongest gap +0.031709. | Not universal dominance; repeatability still being expanded. |
| CONTOUR deep-time protection | 12/12 vs X, 12/12 vs BB1, 11/12 vs XY4 in Torino summary. | Protection benchmark, not an application result by itself. |
| Black-Scholes scalar route | IBM Marrakesh job with independent target check and benchmark-specific projected comparison. | Scalar route benchmark, not full PDE surface solve. |
| Protection ablation | Shows which protection branches help this route and which branches should be gated. | Current best branch is light protection; heavier schemes are still maturing. |
| Hardening update | TSME/CONTOUR/X-XX branches are becoming deployable through no-harm selection. | More repeated windows needed for stronger public claims. |

## 8. Reviewer Path

Recommended reading order:

| Step | Artifact |
| ---: | --- |
| 1 | [README.md](README.md) |
| 2 | [docs/daemon_results_story_20260529.md](docs/daemon_results_story_20260529.md) |
| 3 | [docs/fireopal_matched_results.md](docs/fireopal_matched_results.md) |
| 4 | [docs/results.md](docs/results.md) |
| 5 | [reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md) |
| 6 | [reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md) |

