# Daemon: Quantum Runtime and Protected Execution Stack

Daemon is a quantum runtime stack for making structured workloads executable on noisy quantum hardware. It focuses on route selection, circuit protection, hardware-aware execution, and evidence-bounded reporting from live IBM runs.

This repository is the public benchmark and results showcase. Core compiler internals, calibration policy, derivations, and production parameters are intentionally withheld.

![Backend](https://img.shields.io/badge/Hardware-IBM%20Quantum-0a66c2)
![Focus](https://img.shields.io/badge/Focus-Protected%20Runtime-success)
![Apps](https://img.shields.io/badge/Application-Black--Scholes%20%2F%20PDE%20Scalar-blue)
![IP](https://img.shields.io/badge/Core%20IP-Withheld-lightgrey)

## What Daemon Does

Daemon is not a single transpiler pass. It is a route-aware runtime stack:

| Layer | Role |
| --- | --- |
| Problem route | Maps structured workloads into quantum-eligible forms such as PDE scalar targets, stochastic expectations, QAE fragments, and Hamiltonian/drift probes. |
| Circuit route | Builds hardware-survivable circuits instead of forcing full reversible arithmetic when that would collapse under noise. |
| Protection stack | Applies TSME, CONTOUR, X/XX handling, phase-zero support, sign symmetry, protected layout selection, and no-harm policies. |
| Hardware execution | Runs selected circuits on IBM backends when a backend/key pair is executable. |
| Evidence engine | Emits reports with backend, job IDs, QPU time, estimates, confidence intervals, and claim boundaries. |

## Current Evidence Snapshot

Daemon currently has public artifacts for:

- Live IBM Black-Scholes/Feynman-Kac scalar benchmark runs.
- Runtime protection ablations across phase-zero calibration, sign symmetry, protected layout selection, Hamming phase compression, TSME sheltering, CONTOUR ordering, terminal mirror decoding, and X/XX echo variants.
- CONTOUR deep-window protection benchmarks against baseline and XY4-style schedules.
- Matched Fire Opal comparison packets with explicit boundaries.
- Cross-backend/deep-window benchmark artifacts from the earlier CONTOUR result set.

Important boundary:

- Daemon does not claim a general quantum speedup for arbitrary PDEs.
- Daemon does not claim a full PDE-surface quantum solve.
- Speedup numbers in this repo belong to the exact benchmark assumptions and reports that produce them.
- The repo is evidence-facing, not an IP disclosure.

## Demonstrations

See [DEMONSTRATIONS.md](DEMONSTRATIONS.md) for the main public demo index.

| Demonstration | Artifact |
| --- | --- |
| Black-Scholes scalar benchmark | [Black-Scholes protection ablation](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md) |
| TSME/CONTOUR hardening | [Protection hardening update](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md) |
| CONTOUR deep-window hardware benchmark | [Hardware summary](reports/validation3_hardware_summary_20260419.md) |
| Fire Opal comparison packet | [Fire Opal summary](reports/fireopal_vs_pqmcf_summary_20260418.md) |
| Earlier public benchmark brief | [docs/results.md](docs/results.md) |

## Core Runtime Methods

### TSME

TSME is Daemon's semantic protection layer. It protects the logical object as a structured semantic state rather than treating the useful signal as an unprotected local amplitude. In the Black-Scholes runner, TSME appears as a phase shelter and optional terminal mirror decode. The latest hardening pass adds agreement gating, calibrated two-bit mirror decoding, and no-harm selection so physical TSME branches are deployed only when the routed tax is justified.

### CONTOUR

CONTOUR is Daemon's phase/drift protection layer. It is designed for deterministic, hardware-aware drift control. In the Black-Scholes scalar route, a low-tax CONTOUR mode reorders phase terms to balance phase-channel load without doubling the circuit. In deeper hardware benchmarks, CONTOUR is evaluated against baseline and XY4-style schedules.

### X/XX Handling

X/XX handling targets transverse pressure and echo-style cancellation around protected paths. Current evidence shows this is regime-dependent: heavier echo branches can help deeper circuits but may hurt shallow phase estimators, so Daemon records pressure and lets the selector decide.

### Selector

The selector is part of the invention. It prevents the runtime from blindly applying every protection. Some circuits need light phase-zero support; others need drift protection; others should reject heavy branches. Daemon's no-harm policy makes protection conditional on target-blind diagnostics and routed implementation tax.

## Selected Results

### Black-Scholes/Feynman-Kac Scalar Route

Best corrected live branch in the current ablation:

| Field | Value |
| --- | --- |
| Backend | `ibm_marrakesh` |
| Job | `d8c98ij8ch0s738ugrug` |
| QPU time | `3.000s` |
| Estimate | `0.6205121893` |
| Independent target | `0.6240388897` |
| Corrected error+CI | `0.055871195` |
| Projected high-dimensional MC comparison | `250.902x` |

Report:

[reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md)

### CONTOUR Hardware Benchmark

CONTOUR was tested against baseline and XY4-style schedules over deeper hardware windows.

Report:

[reports/validation3_hardware_summary_20260419.md](reports/validation3_hardware_summary_20260419.md)

### Fire Opal Comparison

The repo includes matched Fire Opal comparison packets. These should be read with the boundary in the report: some benchmark-scoped Daemon wins exist in the broader results set, but the included summary also records cases where Fire Opal beat raw PQMCF branches. This repository avoids presenting those comparisons as a universal dominance claim.

Reports:

- [reports/fireopal_vs_pqmcf_summary_20260418.md](reports/fireopal_vs_pqmcf_summary_20260418.md)
- [docs/fireopal_matched_results.md](docs/fireopal_matched_results.md)

## Repository Map

| Path | Contents |
| --- | --- |
| `docs/` | Public benchmark notes, figures, and result summaries. |
| `reports/` | Technical reports and comparison packets. |
| `reports/licensing_evidence/` | Evidence artifacts for Black-Scholes and protected-runtime claims. |
| `data/` | JSON run artifacts and benchmark outputs. |

## Reviewer Path

For a fast technical review:

1. [DEMONSTRATIONS.md](DEMONSTRATIONS.md)
2. [reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md)
3. [reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md)
4. [reports/validation3_hardware_summary_20260419.md](reports/validation3_hardware_summary_20260419.md)
5. [reports/fireopal_vs_pqmcf_summary_20260418.md](reports/fireopal_vs_pqmcf_summary_20260418.md)

## Contact

Parthiv Maddipatla  
TJHSST  
2027pmaddipa@tjhsst.edu  
https://github.com/ParthivM1

