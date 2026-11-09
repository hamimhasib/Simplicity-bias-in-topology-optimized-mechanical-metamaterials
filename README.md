# Topology optimized mechanical metamaterials are algorithmically simple
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21361351.svg)](https://doi.org/10.5281/zenodo.21361351)

---
# Algorithmic simplicity of topology-optimized metamaterials

## Purpose

Optimizes a periodic unit cell for effective stiffness from random initial
fields, measures the algorithmic complexity of the resulting topologies with
three estimators, and compares them against matched random structures.

## Requirements

Python 3.9 or later with `numpy`, `scipy` and `matplotlib`. Optional:
`joblib` for parallel runs, `pybdm` for the Block Decomposition Method. Both
degrade gracefully; without `pybdm` a block-counting estimator is used instead.

```
pip install numpy scipy matplotlib joblib pybdm
```

## Running

Open the notebook and run all cells. Results are cached to `study.pkl`, so a
second run only redraws the figures. Delete that file to recompute.

Set `FULL = True` in the configuration cell for the full study (2000 shear and
500 directional runs). The default is a reduced configuration that reproduces
every result at smaller sample sizes.

## Configuration

All settings are in the second cell.

| | |
|---|---|
| `N` | cell resolution, 32 by default |
| `NU`, `EMIN` | base Poisson ratio and void stiffness floor |
| `VF`, `RMIN` | volume fraction and density filter radius |
| `STAGES`, `ITERS` | SIMP continuation schedule and iterations per stage |
| `THRESH` | binarization threshold |
| `FULL` | full study or reduced |

## Output

Figures are written to `figures/` as SVG and PNG at 300 dpi. Tables print to the
notebook.

| | |
|---|---|
| Figure 1 | complexity against stiffness, both objectives, and the effect sizes |
| Figure 2 | baseline construction, the matching functions, and the effect sizes |
| Figure 3 | gray fraction and binarization threshold |
| Figure 4 | neutral-set sizes |
| Figure 5 | where the simplicity originates |
| Figure 6 | robustness across filter radius, mesh and base Poisson ratio |
| S3 to S5 | spectra, origin dependence, bootstrap intervals |

## Runtime

One optimization is about 9 s single-core at N = 32. The reduced configuration
takes a few minutes with `joblib`; the full study is around half an hour on
twelve cores.

## Method notes

Homogenization is verified against the analytic plane-stress matrix for a solid
cell, and the sensitivities against central differences.

Topologies are counted as distinct after quotienting by the dihedral symmetries
of the square cell and by translation of the origin. The complexity of a
periodic cell depends on where it is cut, so the translation quotient is needed
for counts to describe structures rather than representations.

