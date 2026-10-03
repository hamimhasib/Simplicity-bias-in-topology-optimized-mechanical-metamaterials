# Topology optimized mechanical metamaterials are algorithmically simple
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21361351.svg)](https://doi.org/10.5281/zenodo.21361351)

---

## Start here

Open **`reproduction.ipynb`** and run it top to bottom. It is self-contained: it loads
every definition it needs from `Improved_Code_new.ipynb`, reads the checkpoints in
`sweep_data/`, and recomputes every figure and every headline number in the manuscript,
printing each one next to the value the paper reports.

```
pip install numpy scipy matplotlib joblib pybdm
jupyter notebook reproduction.ipynb
```

`pybdm` supplies the Block Decomposition Method estimator. If it is not installed, the
notebook says so and continues with the other two estimators (CLZ, zlib), which is
sufficient for every section except the BDM column of each table.

Runtime on the full deposit: about three minutes for Sections 1 through 10. Two sections
regenerate data the checkpoints do not store (surrogate cells for the Figure 2 baseline,
and the continuous density field for the Figure 3 threshold sweep); both cache what they
build on the first run, so a second run is under a minute. Section 11 is optional,
switched off by default, and regenerates a single optimization from a random seed.

| section | what it reproduces |
|---|---|
| 1 | environment and package versions |
| 2 | loads the required functions from `Improved_Code_new.ipynb` |
| 3 | four correctness checks on the homogenization solver |
| 4 | structure and contents of the primary checkpoint |
| 5 | **Figure 1** — δ = −1.00 for both objectives, three estimators, with margins |
| 6 | **Figure 2** — builds the correlation-matched surrogates and recomputes δ |
| 7 | **Figure 3** — regenerates optima with the density field and sweeps the threshold |
| 8 | **Figure 4** — heavy-tailed neutral-set distribution; the 22 / 253 / ~1.4×10⁴ counts |
| 9 | **Figure 5** — δ across filter radius, mesh resolution, base Poisson ratio |
| 10 | every abstract-level claim next to the recomputed value |
| 11 | optional: regenerate one shear optimum from seed 0 |

## What else is in this deposit

| file | purpose |
|---|---|
| `reproduction.ipynb` | run this first; reproduces the whole paper |
| `Improved_Code_new.ipynb` | the analysis notebook; defines every function `reproduction.ipynb` calls and is also where the original sweeps were generated |
| `sweep_data/` | checkpointed results (see below) |
| `requirements.txt` | pinned package versions |
| `*.py` | standalone scripts for individual figures, kept for reference. Not required to run `reproduction.ipynb` |

### `sweep_data/`

```
sweep_data/
├── data_rmin_1.5.pkl     primary condition — every figure, unless the caption says otherwise
├── data_rmin_2.0.pkl     filter radius sweep
├── data_rmin_2.5.pkl
├── data_rmin_3.0.pkl
├── data_res_N16.pkl      mesh resolution sweep
├── data_res_N32.pkl
├── data_res_N64.pkl
└── data_nu_*.pkl         base Poisson ratio sweep, 7 files
```

Each file is a pickled `dict` with three keys: `designs` (a `dict` keyed `"shear"` and
`"dirx"`, each a list of optimization runs), `background` (filtered-random cells matched
to the optima on solid fraction and feature scale), and `candidate_pool` (15 000 random
cells drawn independently of the optimizer, used only for the neutral-set analysis of
Figure 4).

Each run is a `dict` with fields `seed`, `xb` (the binarized 32×32 cell — this is what
gets compressed), `C` (the 3×3 effective stiffness tensor, normalized to a solid phase
with E₀ = 1), `conv` (whether the run's final continuation stage converged), and `iters`.

**If `sweep_data/` is missing from your copy**, `reproduction.ipynb` will say so and stop
at Section 4 rather than fail silently later. The original sweeps can be regenerated from
`Improved_Code_new.ipynb`, though the primary condition alone takes on the order of hours.

## Reproducibility

Every random draw is seeded. Optimization run *s* uses seed *s*; the filtered-random
baseline uses seed 12345; the independent candidate pool uses seed 777. Re-running on the
same package versions reproduces the deposited checkpoints bit for bit; different BLAS
builds may shift the last few digits of an effective tensor, which does not change any
reported conclusion.

## Known limitations of this deposit

- **Topology counts depend on how two cells are judged equivalent.** The `key_of`
  function in `Improved_Code_new.ipynb` quotients by the unit cell's rotation and
  reflection symmetries but not by translation. Under periodic boundary conditions a
  translated cell is the same material, so the neutral-set counts in Section 8 of
  `reproduction.ipynb` — and in Figure 4 of the manuscript — are upper bounds, most
  visibly for the directional objective, whose optima are laminates differing largely by
  vertical offset.
- **The continuous density field is not stored in `sweep_data/`.** Only the thresholded
  `xb` is kept. Section 7 of `reproduction.ipynb` regenerates a small batch of runs with
  the field retained in order to reproduce the Figure 3 threshold sweep; it does not use
  the deposited checkpoints for this one section.
- **Complexity values are not comparable across estimators.** zlib returns bytes, CLZ a
  dimensionless phrase count, BDM a value in bits. Compare within one estimator only.
- **The base material is dimensionless.** E₀ = 1 is a normalization: every reported
  stiffness is a ratio to the solid phase. Multiply by a real E₀ to obtain values for a
  specific material; results scale exactly because the governing equations are linear.
  The base Poisson ratio does not scale this way, which is why it is swept separately
  (Figure 5(c)).
- **Shear converges on a small fraction of runs** at the primary filter radius. `conv` is
  strict — a run counts as converged only if its last continuation stage settles below
  the stated tolerance — and unconverged runs are kept in the deposit rather than
  discarded. Every δ reported in the manuscript and reproduced here is computed over
  converged, distinct optima only (see Section 4 output for exact counts).

## License

Code: [fill in — MIT, BSD-3-Clause, or Apache-2.0 are common for research code]
Data: [fill in — CC-BY-4.0 is standard for Zenodo research data]



