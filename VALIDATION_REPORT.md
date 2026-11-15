# Validation Report — Digital Fringe Processing (research-extension v2)

**Date:** 2026-11-16. **Execution:** local Python 3.13 notebook kernel (`nbclient`). **Data:** synthetic only. **Result:** all **5 notebooks** executed end-to-end without cell errors; `assert` checks passed. **Not** experimental qualification and not Google Colab execution verification.

## Scientific scope of the new Case Study 05

The notebook contains the three original synthetic cases and three deeper metrological stress tests:

1. **A2 — clamped circular Kirchhoff–Love plate**, whose displacement is tied to E, ν, t, a and q; compare nominal quadrature with **oracle-calibrated** least-squares phase stepping.
2. **B2 — dual-pitch grid phase analysis** with a large rigid translation, demonstrating phase ambiguity and integer fringe-order recovery.
3. **C2/C3 — structured-light 3D profilometry** with nonlinear camera/projector intensity response (`I^γ` synthetic) and baseline miscalibration; separate random noise from systematic artifacts.

All new numeric values below were obtained by executing Notebook 05 (fixed seeds, present simulator), not taken from experiments or published datasets.

## Reproduced numerical findings

**A2** (`λ0=532 nm`; `E=70 GPa`; `t=1 mm`; `a=20 mm`; `ν=0.33`; target central deflection `270 nm`): `D=6.546 N·m`, modeled pressure `q=707.0 Pa`. Height RMSE: **3.42 nm** for nominal quadrature, **0.72 nm** for calibrated-step LS. The LS uses **known true steps**, not an inferred/experimentally established phase-step calibration.

**B2** (`p1=18 px`, `p2=23 px`, synthetic pitch `82.8 px`, mean rigid translation `12.96 px`): displacement RMSE **18.001 px** (single pitch, wrong order), **0.329 px** (dual-pitch coarse), **0.051 px** (integer-order-refined); 100% of integer orders were correctly inferred **for this single synthetic seed and disturbance level only**.

**C2** (synthetic pinhole: `Z0=600 mm`, `b=75 mm`, `f=1200 px`, `P=24 px`; injected Gaussian noise) yielded:

| Synthetic scenario | RMSE height (mm) | Bias (mm) |
|---|---:|---:|
| 4-step, gamma=1 | 0.4373 | -0.0033 |
| 4-step, gamma=2.2 | 0.4537 | -0.0033 |
| 8-step, gamma=2.2 | 0.4495 | +0.0019 |
| 4-step, modeled baseline +3% | 0.5298 | -0.2269 |

These are **illustrative paired-seed comparisons**, not empirical rankings of hardware or optimized algorithms. The per-frame noise model for different step counts is a heuristic, not a full fixed-photon-budget model.

**C3 — gamma-only, zero noise**: height RMSE 4-step `gamma=1`: approximately zero; 4-step `gamma=2.2`: **0.057057 mm**; 8-step `gamma=1`: approximately zero; 8-step `gamma=2.2`: **0.000023 mm**. This isolates nonlinear harmonic artifact in the **particular simulated response**, not all forms of gamma or motion.

**Sensitivity audit** (analytical checked against finite differences for C): interferometry **42.34 nm/rad** at `λ=532 nm`; grid displacement **2.86 pixel/rad** at `p=18 px`; FPP at `Z0=600 mm` **15.28 mm/rad**. These numbers correspond to **different physical measurands**, so cannot be compared as instrument resolutions.

## Multi-seed baseline benchmark, 10 seeds × 3 synthetic noise levels

| Case | Unit | σ=0.004 median RMSE | σ=0.014 median RMSE | σ=0.035 median RMSE |
|---|---|---:|---:|---:|
| A, interferometry | µm | 0.00034 | 0.00120 | 0.00299 |
| B, grid/moiré | pixel | 0.03370 | 0.11797 | 0.29551 |
| C, FPP | mm | 0.17485 | 0.61228 | 1.53458 |

**Failure thresholds** in this table are simulation-specific teaching criteria; observed failures: B 10/10 at σ=0.035, C 10/10 at σ=0.035, A 0/10. They **are not** automatically unwrapping-failure counts (failure is defined by RMSE threshold for baseline benchmark). To detect actual incorrect fringe order, evaluate integer-order residuals directly against independent ground truth.

## Verification and limitations

- Notebook cells and type structure validated by `nbformat`; five complete notebooks passed `nbclient` execution.
- Derived plate stiffness and pressure, phase-to-displacement signs, synthetic pitch and stereo sensitivity audited by formulas and code assertions.
- No actual optical bench, projector–camera synchronization, laser calibration, lens distortion, spatially correlated camera noise, photometric calibration, real surface roughness, mechanical boundary imperfection, or measurement traceability validated.
- Research protocol in `CASE_STUDY_RESEARCH_PROTOCOL.md` specifies needed metadata, control/calibration plans, independent references, and experimental benchmarks.
- Reproduction command: `python tests/validate_notebooks.py` after installing `requirements.txt`.

---

## Final five-notebook academic quality audit (2026-11-16)

After the original v2 baseline above, the course notebooks were upgraded and independently executed again. The original *v2 data tables remain historical synthetic results* for traceability; the new appended simulations are intentionally not spliced into those old benchmarks.

- **Notebook 01** now includes ensemble camera shot/read-noise statistics, a variance–mean regression and ADC saturation bias (synthetic).
- **Notebook 02** now tests a two-dimensional oblique Fourier carrier and quantifies spectral leakage from a noninteger-bin spatial carrier.
- **Notebook 03** now tests the small-noise phase-estimator variance analytically against Monte Carlo and a fixed-total-photon-budget shot/read-noise acquisition model.
- **Notebook 04** now checks a signed covariance-aware GUM uncertainty budget against a 30,000-sample correlated Monte Carlo. λ/n uncertainty values in this stress test are deliberately inflated to reveal covariance effects; they are **not laser or environmental specifications**.
- **Notebook 05** now contains a synthetic geometric-moiré beat-frequency reconstruction and intensity-correlation of a simulated coherent speckle field under translation/decorrelation. This is **not ESPI experimental validation**.

**Final execution results:**

- 5/5 notebooks passed structural `nbformat` validation and end-to-end `nbclient` execution, including all notebook `assert`s.
- 7/7 separate independent analytic-invariant tests passed with `python tests/test_research_properties.py`.
- Five static HTML files in `html/` were generated by **executing each notebook**, embedding generated plots (not copying static images from unrelated experiments).
- The accompanying `AUDIT_AKADEMIK_9_PER_NOTEBOOK.md` supplies a transparent weighted rubric: **01=9.0, 02=9.2, 03=9.2, 04=9.3, 05=9.2**, valid **only as an internal course-material audit**. It is not evidence of external peer-review scores or experimental validity.

Remaining validation requirements for publication: reference-grade optical bench/camera/projector calibration, physical samples with known measurands, environmental monitoring, inter-session repeatability, external datasets, uncertainty traceability and independent reviewer scrutiny.
