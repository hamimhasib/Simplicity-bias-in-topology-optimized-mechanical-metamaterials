# Topology optimized mechanical metamaterials are algorithmically simple

The pipeline optimizes a periodic unit cell by the SIMP method, computes the effective stiffness tensor by periodic homogenization, measures the algorithmic complexity of the resulting topology under three estimators, and compares that complexity against random cells matched on solid fraction and feature scale.

## Installation

```bash
pip install numpy scipy joblib pybdm matplotlib
```

Python 3.9 or later is required. The `zlib` module is part of the standard library. Parallel execution is handled by `joblib` and defaults to the number of available cores.

## Usage

Open `Source_Code.ipynb` and execute the sections in order.

| Section | Contents |
|---|---|
| 1. Environment | Imports and dependency checks |
| 2. Physics engine | Periodic homogenization, SIMP interpolation, density filter |
| 3. Objectives and complexity | Objective functions and the three complexity estimators |
| 4. Engine functions | Optimizer, baselines, statistics, sweeps |
| 5. Configuration | Parameters |
| 6. Physics validation | Verification of the homogenizer |
| 7. Main generation | Optima, matched background, candidate pool |
| 8. Gray-fraction control | Binarity of the converged optima |
| 9. Baselines | Four random baseline constructions |
| 10. Sweeps | Mesh resolution and base Poisson ratio |

Section 7 writes checkpoints to `sweep_data/` and is skipped when those files are present. The analysis and figures can therefore be regenerated without repeating the optimization.

```
sweep_data/
├── data_rmin_{1.5,2.0,2.5,3.0}.pkl
├── data_res_N{16,32,64}.pkl
└── data_nu_{-0.8 … 0.45}.pkl
```

### Reduced configuration

The default settings perform 2000 shear optimizations, 500 optimizations for each remaining objective, and construct a candidate pool of 15,000 cells. This requires several hours on a typical multi-core machine. For a first run, reduce the sample sizes in Section 5:

```python
N_PRIMARY = 50      # default 2000
N_OTHER   = 50      # default 500
AP_POOL   = 500     # default 15000
```

## Parameters

All parameters are defined in Section 5.

| Parameter | Default | Description |
|---|---|---|
| `N` | 32 | Elements per side of the unit cell |
| `VOL` | 0.4 | Solid volume fraction |
| `RMIN_LIST` | 1.5, 2.0, 2.5, 3.0 | Density-filter radius; 1.5 is the primary setting |
| `N_LIST` | 16, 32, 64 | Mesh-resolution sweep |
| `NU_LIST` | −0.8 to 0.45 | Base-material Poisson ratio sweep |
| `MEASURES` | clz, zlib, bdm | Complexity estimators |
| `N_PRIMARY` | 2000 | Shear optimizations at the primary setting |
| `N_OTHER` | 500 | Optimizations per remaining objective |
| `AP_POOL` | 15000 | Random candidate pool |
| `BASELINE_N` | 800 | Cells per baseline construction |
| `ITERS_PER` | 120 | Iteration budget per optimization |
| `TOL` | 1e-3 | Convergence tolerance on the density field |

## Objectives

Each objective is a component of the homogenized stiffness tensor in Voigt notation. The optimizer minimizes, so stiffness objectives are negated.

| Name | Objective | Maximizes |
|---|---|---|
| `shear` | −C₃₃ | Shear stiffness |
| `dirx` | −C₁₁ | Directional stiffness |
| `aux` | +C₁₂ | Auxetic coupling |
| `iso` | −(C₁₁+C₂₂)/2 | Isotropic stiffness |

Two outcomes are expected and are not faults in the code. The isotropic objective does not satisfy the convergence criterion within the iteration budget and is excluded from the analysis. The auxetic objective converges, but all converged runs reduce to a single distinct topology, for which an effect size is not defined.

## Interpretation of the output

Cliff's δ is the effect size reported throughout:

    δ = P(K_opt > K_rnd) − P(K_opt < K_rnd)

estimated over all pairs of optima and baseline cells. It takes values in [−1, 1]. Because complexity is the measured variable, δ is negative when the optima are the simpler population. A value of −1 indicates complete separation, and |δ| ≥ 0.474 is the conventional threshold for a large effect.

M denotes the number of distinct optima remaining after canonical deduplication. Where M is less than two, the effect size is undefined and the condition is reported as starved rather than assigned a value.

## Figures

Figure generation is not included in this notebook. The figure code reads the checkpointed `.pkl` files and writes both `.svg` and `.png` output. Execute Sections 1 through 5 to define the engine and load the configuration, then run the figure code.

```

## License

Licensed under the Apache License, Version 2.0.
