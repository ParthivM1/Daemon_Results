# Daemon

This repository contains public benchmark results for Daemon, a quantum runtime stack for structured workloads on noisy IBM hardware. The current results focus on protected execution, high-dimensional Black-Scholes/Feynman-Kac scalar routing, CONTOUR drift protection, and matched Fire Opal comparisons.

The repository is results-facing. It does not include the full compiler, production selector, calibration policy, or proof-level method details.

## Summary of Public Results

| Evidence lane | Public result |
| --- | --- |
| Black-Scholes / Feynman-Kac scalar route | Live IBM Marrakesh run on a PDE-derived scalar route with job ID `d8c98ij8ch0s738ugrug`, 3.000s QPU time, estimate `0.6205121893`, independent target `0.6240388897`, corrected error+CI `0.055871195`, and projected high-dimensional MC comparison `250.902x` under the benchmark report assumptions. |
| Fire Opal matched comparisons | Multiple matched live IBM workloads where Daemon/PQMCF beats Q-CTRL Fire Opal, with strongest completed gaps of `+0.031709`, `+0.023570`, and `+0.010439`. Repeatability reruns are also shown when Fire Opal wins. |
| CONTOUR deep-time protection | Torino aggregate: `12/12` wins vs X, `12/12` wins vs BB1, `11/12` wins vs XY4, mean gain vs XY4 `+0.0531`; deep-only confirmation reports `6/6` wins vs X, BB1, and XY4. |
| Protection ablation | The Black-Scholes route compares phase-zero support, sign symmetry, protected layouts, Hamming phase compression, TSME sheltering, CONTOUR ordering, terminal mirror decode, and X/XX echo variants on the same application target. |

## Reports

Detailed result narrative:

[docs/evidence_story_20260529.md](docs/evidence_story_20260529.md)

Artifact index:

[DEMONSTRATIONS.md](DEMONSTRATIONS.md)

## 1. Black-Scholes / Feynman-Kac Scalar Route

The application lane is a high-dimensional Black-Scholes basket PDE routed through a Feynman-Kac scalar expectation. The reported target is a scalar value, not a full PDE surface.

Route:

```text
high-dimensional Black-Scholes basket PDE
-> Feynman-Kac scalar expectation
-> phase-estimator quantum route
-> protected IBM execution
-> independent target check
```

Best corrected live branch:

| Field | Value |
| --- | --- |
| Backend | `ibm_marrakesh` |
| Job | `d8c98ij8ch0s738ugrug` |
| QPU time | `3.000s` |
| Estimate | `0.6205121893` |
| Independent target | `0.6240388897` |
| Corrected error+CI | `0.055871195` |
| Projected high-dimensional MC comparison | `250.902x` |

![Black-Scholes estimates against independent target](docs/figures/black_scholes_estimates_vs_target_actual.png)

Primary report:

[reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md)

## Runtime Components Used in the Black-Scholes Runs

These are the runtime pieces referenced in the Black-Scholes ablation. Names are kept close to the run labels so the table below can be checked against the reports.

| Component | What it does |
| --- | --- |
| `readout_support_calibration` | Support calibration estimates measurement/readout behavior so reported phase-route estimates are not treated as raw uncalibrated counts. |
| `protected_layout` | Protected layout selection routes the circuit through a hardware layout chosen as part of Daemon's execution policy rather than blindly accepting an arbitrary physical mapping. |
| `real_only_phase_alignment` | Real-only phase alignment keeps the Black-Scholes phase-estimator route focused on the real component used by the scalar benchmark. |
| `sign_symmetric_phase_cancellation` | Sign symmetry runs matched phase branches so shared phase/readout bias can cancel when the branches are combined. |
| `transpilation_scored_layout_selector` | The layout selector scores candidate transpiled layouts and chooses the route with lower expected implementation risk. |
| `spectral_hamming_phase_compression` | Hamming phase compression reduces physical qubit pressure for the phase route while preserving a competitive corrected error+CI in the ablation. |
| `phase_zero_visibility_calibration` | Phase-zero calibration adds a neutral/reference phase branch to estimate visibility or offset effects before scoring the protected route. |
| `tsme_semantic_phase_shelter` | TSME phase sheltering adds a protected semantic phase branch so the logical phase signal is not left as an unprotected bare readout. |
| `contour_phase_ordering` | CONTOUR phase ordering reorders phase operations as a low-tax drift-control branch for the routed phase estimator. |
| `contour_toroidal_phase_echo` | CONTOUR phase echo is a heavier echo-style drift branch intended for regimes where extra phase protection can justify its circuit tax. |
| `x_xx_phase_echo` | X/XX echo is an optional echo branch targeting transverse and two-qubit pressure around protected phase paths. |
| `tsme_terminal_mirror_decode` | TSME terminal mirror decode mirrors the final phase bit onto a shelter wire and decodes through shelter/ancilla agreement. |
| `tsme_mirror_matrix_decoder` | The mirror matrix decoder uses a calibrated two-bit readout matrix over `00`, `01`, `10`, and `11` outcomes. |
| `tsme_auto_decoder` | Auto decoding chooses the mirror decoding path from support behavior instead of forcing a fixed decoder. |
| `mirror_agreement_gate` | The agreement gate rejects mirror packets when shelter/ancilla agreement falls below the frozen diagnostic threshold. |
| `effective_shot_ci_correction` | Effective-shot correction widens confidence intervals when filtering or postselection reduces the number of usable logical shots. |
| `no_harm_selector` | The no-harm selector disables optional physical protection when routed depth, two-qubit pressure, or diagnostics predict the branch will hurt more than help. |

Source basis for this section: [Black-Scholes protection ablation][component-source-1] and [protection hardening update][component-source-2]. The descriptions are architecture-level and omit private selector parameters and implementation details.

## 2. Black-Scholes Protection Ablation

The same scalar route was run with several Daemon protection/circuit branches.

![Black-Scholes protection ablation](docs/figures/black_scholes_ablation_actual.png)

| Rank | Method family | Corrected error+CI | QPU s | Qubits |
| ---: | --- | ---: | ---: | ---: |
| 1 | Phase-zero + sign symmetry + protected layout | `0.055871195` | `3.000` | `7` |
| 2 | Hamming phase compression + sign symmetry | `0.057348679` | `3.000` | `4` |
| 3 | Hamming + phase-zero | `0.059517201` | `3.000` | `4` |
| 4 | Sign symmetry only | `0.061124328` | `3.000` | `7` |
| 5 | Hamming + TSME shelter + CONTOUR ordering | `0.083265351` | `3.000` | `5` |
| 6 | Hamming + TSME shelter + CONTOUR echo + X/XX echo | `0.105975490` | `3.000` | `5` |
| 7 | TSME terminal mirror + matrix decoder | `0.114125250` | `3.000` | `5` |
| 8 | TSME terminal mirror + auto decoder | `0.142901850` | `3.000` | `5` |

What the ablation says:

| Component | Observed role in this benchmark |
| --- | --- |
| Phase-zero calibration | Present in the best branch; stabilizes the shallow phase route. |
| Sign symmetry | Present in the strongest branches; cancels part of the phase/readout bias. |
| Protected layout selection | Keeps physical qubit routing inside the runtime decision. |
| Hamming phase compression | Reduces qubit pressure from 7 to 4 while staying close to the best branch. |
| TSME shelter + CONTOUR ordering | Implemented and live-tested; not yet best on this shallow route. |
| CONTOUR echo + X/XX echo | Available, but too expensive for this specific shallow route in the current report. |
| TSME terminal mirror | Physically implemented and measured; selector-gated because forced deployment worsened this route. |

The ablation shows that protection has to be selected by circuit regime. On this shallow Black-Scholes route, the best branch is low-tax phase support rather than the heaviest TSME/CONTOUR/X-XX branch.

Hardening report:

[reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md)

## 3. Matched Fire Opal Comparisons

Daemon/PQMCF has completed matched wins against Q-CTRL Fire Opal on several live IBM workloads. The graph includes both completed Daemon wins and repeatability reruns where Fire Opal wins.

![Matched Fire Opal comparison gaps](docs/figures/fireopal_matched_gaps_actual.png)

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

Repeatability reruns:

| Workload | Backend | Daemon / PQMCF best | Fire Opal best | Gap |
| --- | --- | ---: | ---: | ---: |
| TFIM mixed n16 v6 repeat | IBM Marrakesh | 0.882243 | 0.895440 | -0.013197 |
| XY ring n16 v12 repeat | IBM Marrakesh | 0.970332 | 0.975651 | -0.005319 |

These results should be read as benchmark-scoped matched wins, not a general dominance claim across all workloads or calibration windows.

Primary artifact:

[docs/fireopal_matched_results.md](docs/fireopal_matched_results.md)

## 4. CONTOUR Deep-Time Protection

CONTOUR is Daemon's phase/drift protection branch. The strongest public CONTOUR evidence is in deeper windows where baseline schedules decay.

![Deep-time decay curve](docs/figures/deep_time_decay_curve.png)

![Lattice scaling chart](docs/figures/lattice_scaling_bar_chart.png)

Torino aggregate:

| Metric | Result |
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
| Mean headroom | +0.0159 |

Deep-only confirmation:

| Comparison | Result |
| --- | --- |
| CONTOUR vs X | 6/6 wins |
| CONTOUR vs BB1 | 6/6 wins |
| CONTOUR vs XY4 | 6/6 wins |
| Mean absolute gain vs XY4 | +0.0423 |

Primary artifacts:

| Artifact | Link |
| --- | --- |
| Results summary | [docs/results.md](docs/results.md) |
| Deep check today 2 | [docs/deep_check_today2.md](docs/deep_check_today2.md) |
| Deep check today 5 | [docs/deep_check_today5.md](docs/deep_check_today5.md) |
| Marrakesh deep run | [docs/marrakesh_deep_today6.md](docs/marrakesh_deep_today6.md) |

## 5. Method Notes

### TSME

TSME is the semantic protection branch. In the current Black-Scholes runner, it appears as phase sheltering and optional terminal mirror decode. The hardening pass added agreement gating, calibrated two-bit mirror decoding, auto decode selection, and no-harm selection.

### CONTOUR

CONTOUR is the phase/drift protection branch. In shallow scalar routes, low-tax CONTOUR ordering balances phase-channel load without doubling the circuit. In deeper protection benchmarks, CONTOUR is evaluated against baseline and XY4-style schedules where drift exposure is larger.

### X/XX Handling

X/XX handling targets transverse pressure and echo-style cancellation around protected paths. The Black-Scholes ablation shows that heavy echo branches can hurt shallow routes, so these branches are deployed only when their added circuit cost is justified.

### Selector

The selector chooses between light support, compression, TSME/CONTOUR sheltering, heavy echo paths, or no-harm fallback. The Black-Scholes ablation shows why this matters: the best branch is not always the heaviest branch.

## 6. Repository Map

| Path | Contents |
| --- | --- |
| `docs/` | Public result summaries, evidence story, and graph artifacts. |
| `docs/figures/` | Existing hardware plots and data-driven Matplotlib PNG charts. |
| `reports/licensing_evidence/` | Black-Scholes and protection hardening reports. |
| `data/` | Public JSON artifacts for the deep-window benchmark set. |

## 7. Discussion and Limits

The strongest public evidence is the combination of an application-linked Black-Scholes scalar route, deep-window CONTOUR protection data, and matched Fire Opal comparison tables. The Black-Scholes result is most useful as an application-route demonstration: it connects the runtime stack to a PDE-derived scalar target and shows which protection branches help or hurt on that route.

Supported by this public repo:

- Daemon has live IBM evidence for a Black-Scholes/Feynman-Kac scalar route.
- Daemon has public CONTOUR deep-window protection evidence.
- Daemon has benchmark-scoped matched Fire Opal wins.
- Daemon has protection ablations showing which branches helped this route.

Not claimed from this public repo:

- Daemon solves arbitrary PDEs.
- Daemon solves a full PDE surface on quantum hardware.
- Daemon universally beats Fire Opal.
- Daemon has proven general commercial Black-Scholes quantum advantage.

Those claims require broader frozen repeated validation than what is included here.

## 8. Fast Reviewer Path

1. Read [docs/evidence_story_20260529.md](docs/evidence_story_20260529.md).
2. Read [DEMONSTRATIONS.md](DEMONSTRATIONS.md).
3. Check the Black-Scholes ablation report: [reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md](reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md).
4. Check the hardening report: [reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md](reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md).
5. Check the Fire Opal table: [docs/fireopal_matched_results.md](docs/fireopal_matched_results.md).
6. Check the CONTOUR summary: [docs/results.md](docs/results.md).

## Contact

Parthiv Maddipatla  
TJHSST  
2027pmaddipa@tjhsst.edu  
https://github.com/ParthivM1

[component-source-1]: reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md
[component-source-2]: reports/licensing_evidence/daemon_black_scholes_protection_hardening_update_20260528.md
