# Daemon Black-Scholes Protection Hardening Update

Generated: `2026-05-29T03:37:00Z`

## What Changed

This update hardened the Black-Scholes/Feynman-Kac quantum-core runner around Daemon's protection stack instead of adding more blind hardware trials.

Implemented protection changes:

- `TSME terminal mirror decode`: the final phase bit can be mirrored onto the shelter wire and decoded through ancilla/shelter agreement.
- `TSME mirror matrix decoder`: the shelter pair can be decoded through a calibrated two-bit readout matrix using all four outcomes: `00`, `01`, `10`, `11`.
- `TSME auto decoder`: target-blind support circuits choose matrix decoding only when cross-state leakage is visible; otherwise agreement postselection is used.
- `TSME mirror agreement gate`: packets are rejected if shelter/ancilla agreement falls below a frozen threshold.
- `Effective-shot CI correction`: mirror postselection now widens confidence intervals when logical shots are discarded.
- `No-harm protection selector`: optional physical TSME terminal mirror is disabled when local routed depth/2Q tax exceeds thresholds.
- `Counts persistence`: target artifacts now retain raw counts for later target-blind decoder reanalysis.

## Live IBM Findings

The new terminal-mirror branch did run on `ibm_marrakesh`, but the hardware result showed that forced mirror decode is not yet production-safe for this shallow route.

| Branch | Job | Estimate | Error+CI | QPU s | Notes |
| --- | --- | ---: | ---: | ---: | --- |
| Low-tax TSME + CONTOUR ordering | `d8cdqmqjki0s73ar4j70` | `0.5931180336` | `0.083265351` | `3.000` | Best current physical TSME/CONTOUR branch. |
| TSME mirror + matrix decoder | `d8cgeur8ch0s738uro0g` | `0.5622581304` | `0.11412525` | `3.000` | Matrix decode amplified disagreement bias. |
| TSME mirror + auto decoder | `d8cggss7avuc73dqvm7g` | `0.5360632305` | `0.14290185` | `3.000` | Auto correctly selected postselection, but mirror circuit tax still hurt. |

Current corrected ablation:

`reports/licensing_evidence/daemon_black_scholes_corrected_protection_ablation_current_20260528.md`

## Engineering Diagnosis

The useful result is not that every protection should be forced on. The useful result is:

Daemon now has evidence that TSME terminal mirror is an available physical protection, but on this shallow Black-Scholes phase route the extra terminal mirror step introduces more implementation tax than it removes error.

That is exactly why the no-harm selector was added. The production policy should use:

```text
phase-zero calibration
+ sign symmetry
+ protected layout
+ spectral Hamming compression when selected
+ low-tax CONTOUR ordering
+ TSME shelter only when routed tax is acceptable
+ TSME terminal mirror only when routed tax and support diagnostics justify it
```

The selector should not force heavy TSME/CONTOUR/X-XX layers just because they are novel. It should deploy them when their target-blind diagnostics predict net improvement.

## Current Best Branch

Best measured corrected branch:

```text
real-only + sign symmetry + phase-zero + protected layout + full-stack policy
job: d8c98ij8ch0s738ugrug
estimate: 0.6205121893
corrected error+CI: 0.055871195
QPU seconds: 3.000
speedup vs projected high-dimensional MC: 250.902x
```

Best physical TSME/CONTOUR branch:

```text
hamming + sign symmetry + phase-zero + TSME shelter + CONTOUR ordering
job: d8cdqmqjki0s73ar4j70
estimate: 0.5931180336
corrected error+CI: 0.083265351
QPU seconds: 3.000
speedup vs projected high-dimensional MC: 250.902x
```

## Next Protection Target

The next productive target is not more forced mirror jobs. It is a pre-submit protection selector that chooses among:

```text
light phase-zero branch
hamming phase compression
low-tax TSME shelter + CONTOUR ordering
terminal mirror only when support and routed-tax gates pass
heavy CONTOUR/X-XX echo only for deeper circuits where implementation tax is justified
```

The immediate objective is to make the physical TSME/CONTOUR branch match or beat the current light branch without increasing QPU cost.

