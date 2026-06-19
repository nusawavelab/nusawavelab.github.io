---
icon: lucide/crosshair
---

# Point Extraction

Extract metocean values at a point from large NetCDF files — without loading multi-GB grids into memory.

!!! note "Work in progress"
    This tutorial is being actively expanded. The notebook runs end-to-end — updates will add more depth.

## The problem

Reanalysis files (ERA5 and similar) are often too large to open naively on a laptop. This tutorial walks through **point extraction** workflows: slice what you need, export lightweight results, and keep analysis reproducible with open data.

## Prerequisites

None — this is the first published Nusawave Labs tutorial.

## Run the tutorial

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/nusawave-labs/extract-point/blob/main/notebooks/extract_point.ipynb)

| Resource | Link |
|----------|------|
| GitHub repo | [nusawave-labs/extract-point](https://github.com/nusawave-labs/extract-point) |
| Notebook | [notebooks/extract_point.ipynb](https://github.com/nusawave-labs/extract-point/blob/main/notebooks/extract_point.ipynb) |
| Utilities | [src/extract_utils.py](https://github.com/nusawave-labs/extract-point/blob/main/src/extract_utils.py) |
| Benchmarks | [benchmarks/](https://github.com/nusawave-labs/extract-point/tree/main/benchmarks) |

## Repo structure

```
extract-point/
├── notebooks/extract_point.ipynb   ← main Colab notebook
├── src/extract_utils.py            ← reusable functions
├── data/                           ← sample data (see repo README)
└── benchmarks/                     ← comparison scripts and figures
```

Sample data and pinned dependencies live in the **tutorial repo**, not this website. See the [extract-point README](https://github.com/nusawave-labs/extract-point#readme) for setup.

## Open data

Tutorials use open sources only (ERA5, Copernicus, NOAA, and similar). See the [Open Data Stack](../../open-data/index.md) for access notes.

## Disclaimer

For **education only**. Output is not certified for engineering design, regulatory submission, or operational decision-making.
