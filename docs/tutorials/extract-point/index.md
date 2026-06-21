---
icon: lucide/crosshair
---

# Point Extraction

Extract Hs, Tp, and wind speed at any location from ERA5 — reading 0.0001% of the data, not 100%.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/nusawave-labs/extract-point/blob/main/notebooks/extract_point.ipynb)
&nbsp;
[View on GitHub :fontawesome-brands-github:](https://github.com/nusawave-labs/extract-point){ .md-button }

---

## The problem

You need Hs, Tp, and wind speed at one offshore platform.
**4°N, 108°E. Natuna Sea. 10 years of hourly data.**

ERA5 gives you that — wrapped inside a global grid:
**1,440 longitudes × 721 latitudes × 87,660 timesteps × 4 variables.**

You want **one longitude. One latitude. All timesteps.**

That's 0.0001% of the data.

The question isn't *"how do I open this file."*
The question is: **does your code read 0.0001% or 100%?**

Most scripts — including the ones you'll find on Stack Overflow — read 100%.
Not because the files are too big. Because the **pattern** is wrong.

This tutorial teaches you the pattern that's right.

---

## What you'll learn

| Topic | What it covers |
|---|---|
| NetCDF data model | Why a grid file has a 3D structure and what that means for memory |
| Lazy vs eager loading | The single concept that separates fast from slow workflows |
| `sel` vs `isel` vs `interp` | When to use each — and how to pick indices correctly |
| Dask chunking | When it helps, when it adds overhead, and how to decide |
| Post-processing | Wind speed, wind direction, export to CSV |
| Benchmarks | Time **and** RAM measured across all four methods |

---

## Prerequisites

None — this is the first published Nusawave Labs tutorial.
Basic Python familiarity is helpful but not required.

---

## Run the tutorial

Click the Colab badge above, then **Runtime → Run all**.

The notebook bootstraps itself: clones the repo, installs dependencies from
`requirements-notebook.txt`, and downloads the sample ERA5 file (~120 MB) from
[GitHub Releases](https://github.com/nusawave-labs/extract-point/releases/tag/v0.1-data).
No terminal. No CDS account. No local setup.

**Local alternative:**

```bash
git clone https://github.com/nusawave-labs/extract-point.git
cd extract-point
mkdir -p data
wget -q -O data/era5_sample.nc \
  https://github.com/nusawave-labs/extract-point/releases/download/v0.1-data/era5_sample.nc
pip install -r requirements-notebook.txt
jupyter notebook notebooks/extract_point.ipynb
```

Run the **Step 0** bootstrap cell if the sample file is missing.

---

## Tutorial walkthrough

The notebook is self-contained, but here is what each section covers so you
know what to expect before you open it.

### Step 0 — Environment setup

A single bootstrap cell handles everything on Colab:

- Clones the repo (if needed)
- Installs tutorial dependencies from `requirements-notebook.txt`
- Downloads `era5_sample.nc` from GitHub Releases if missing
- Adds `src/` to the Python path so `extract_utils` is importable

```python
# On Colab this runs git clone, pip install, and wget automatically.
# Locally it just resolves DATA_PATH and confirms the file is present.
print(f"✅ Environment ready | repo: {REPO_ROOT.name} | data: {DATA_PATH.name}")
```

```
Sample data OK: era5_sample.nc (118 MB)
✅ Environment ready | repo: extract-point | data: era5_sample.nc
```

---

### Section 1 — How gridded data actually works

#### 1.1 The data model

ERA5 is not a table. It is a labeled N-dimensional array stored on disk:

```
        time  (8,760 steps for 1 year)
       /
      /
     /__________ latitude  (721 points on the global ERA5 grid)
     |
     |__________ longitude (1440 points on the global ERA5 grid)
```

!!! note "Global vs sample file"
    The **tutorial sample** is a 20 × 20 slice at **0.5°** around Natuna Sea (~120 MB).
    Full global ERA5 wave fields use a coarser grid than 0.25° atmospheric variables.
    The diagram above describes the conceptual global layout — not the sample file dimensions.

Each variable — `swh`, `tp`, `u10`, `v10` — is one such cube.
You want a single vertical "spike" through the time axis at your coordinates.
Loading the entire cube to get that spike is the mistake most scripts make.

#### 1.2 Lazy vs eager loading

| Method | Loads to RAM on open? | When it reads data |
|---|---|---|
| `pandas.read_csv` | ✅ Eager — yes | Immediately |
| `xarray.open_dataset` | 🔵 Lazy | Only when `.compute()` / `.values` is called |
| `xarray` + Dask | 🔵 Lazy + chunked | In parallel blocks, on demand |
| `netCDF4` raw | ✅ Eager | Immediately |

> **Rule:** For files larger than ~500 MB, always open lazily first — then select,
> *then* compute.

The library analogy:

- Opening the dataset = reading the catalog (instant, a few KB)
- `.sel()` = bookmarking a page (still lazy — nothing read)
- `.values` or `.compute()` = physically pulling the book off the shelf

#### 1.3 `sel` vs `isel` vs `interp`

Three ways to pick a point — each with a different use case:

```python
# Setup: detect the grid indices properly — never hardcode them
import numpy as np, xarray as xr
ds = xr.open_dataset(DATA_PATH)          # lazy — instant
lat_idx = int(np.abs(ds.latitude.values  - TARGET_LAT).argmin())
lon_idx = int(np.abs(ds.longitude.values - TARGET_LON).argmin())
```

```
Nearest grid point:  lat[19] = 4.00°N
                     lon[19] = 108.00°E
```

```python
# METHOD 1 — sel: label-based, snaps to nearest grid cell
ts_sel = ds["swh"].sel(latitude=TARGET_LAT, longitude=TARGET_LON,
                       method="nearest")

# METHOD 2 — isel: index-based (use the derived indices above, not magic numbers)
ts_isel = ds["swh"].isel(latitude=lat_idx, longitude=lon_idx)

# METHOD 3 — interp: bilinear, for points that sit between grid cells
ts_interp = ds["swh"].interp(latitude=TARGET_LAT, longitude=TARGET_LON)

# None of these have read a single byte yet — all still lazy
```

---

### Section 2 — Data

The tutorial uses `era5_sample.nc`, a pre-sliced ERA5 file:

| Property | Value |
|---|---|
| Location | Natuna Sea, 4°N 108°E ± 5° box |
| Grid | 20 × 20 cells at 0.5° resolution |
| Period | 2024, hourly |
| Variables | `swh` (Hs), `tp`, `u10`, `v10`, `mwd`, `shts`, `mdts`, `mpts` |
| Size | ~120 MB |

The file is hosted on [GitHub Releases](https://github.com/nusawave-labs/extract-point/releases/tag/v0.1-data)
and downloaded automatically by the Step 0 bootstrap cell.
No Copernicus CDS account is needed to run the tutorial.

---

### Section 3 — Four extraction methods

All four methods extract the same point: **4°N, 108°E**.
The difference is how much data they read and how efficiently they do it.

#### Method 1 — Naive (the wrong pattern)

Shown intentionally so you can see what *not* to do:

```python
ds_eager = xr.open_dataset(DATA_PATH, chunks=None)  # forces eager
hs_wrong = ds_eager["swh"].values                   # loads EVERYTHING into RAM
point    = hs_wrong[:, lat_idx, lon_idx]            # then slices
```

```
Loaded full array: 53.4 MB
Time: 0.15s
❌ Don't do this on a 2 GB file — you'd load 2 GB for one point
```

On the 120 MB sample this seems tolerable.
On a 10-year, 4-variable ERA5 download (> 5 GB), this crashes most laptops.

#### Method 2 — xarray lazy + `.sel()` ✅ recommended default

```python
ds       = xr.open_dataset(DATA_PATH)          # lazy — reads only the header
hs_point = ds["swh"].sel(
    latitude=TARGET_LAT, longitude=TARGET_LON,
    method="nearest",
).compute()                                    # reads only this point's data
```

```
Extracted 8784 timesteps
Memory used: 35.1 KB
Time: 0.06s ✅
```

**35 KB instead of 53 MB.** Same result.

#### Method 3 — xarray + Dask (for very large files)

```python
ds_dask = xr.open_dataset(DATA_PATH, chunks={"time": 500})  # chunked
hs_dask = ds_dask["swh"].sel(
    latitude=TARGET_LAT, longitude=TARGET_LON, method="nearest"
).compute()
```

```
Dask extraction: 8784 timesteps in 0.16s
```

Dask adds scheduler overhead that shows on small files. On multi-GB files
across multiple years, it pays off by processing chunks in parallel.

!!! tip "When to use Dask"
    - File size > 1 GB
    - Multi-year or multi-variable analysis
    - Machine with multiple CPU cores available

#### Method 4 — Bilinear interpolation (when precision matters)

ERA5 wave fields on the global grid are **0.5°** (~55 km).
If your platform sits between grid cells, interpolation gives a better estimate:

```python
# Platform at 4.12°N, 108.37°E — between ERA5 grid points
hs_interp = ds["swh"].interp(
    latitude=TARGET_LAT + 0.12,
    longitude=TARGET_LON + 0.37,
    method="linear",
).compute()
```

```
Mean difference (nearest vs interp): 0.0082 m
→ For offshore engineering, use interp for site-specific reports
```

---

### Section 4 — Post-processing and export

The utility module `src/extract_utils.py` wraps the full extraction pipeline
into three importable functions:

```python
from extract_utils import extract_point, add_wind_speed, quick_stats

df = extract_point(DATA_PATH, TARGET_LAT, TARGET_LON)
df = add_wind_speed(df)   # adds wind_speed_ms and wind_dir_deg
```

`extract_point` handles variable renaming, dimension detection, and
the `.compute()` call. The result is a standard `pandas.DataFrame`
indexed by UTC datetime.

#### Available columns after extraction

| Column | Source variable | Unit |
|---|---|---|
| `hs_m` | `swh` | m |
| `tp_s` | `tp` | s |
| `wave_dir_deg` | `mwd` | ° |
| `swell_hs_m` | `shts` | m |
| `swell_dir_deg` | `mdts` | ° |
| `swell_tp_s` | `mpts` | s |
| `u10_ms` | `u10` | m/s |
| `v10_ms` | `v10` | m/s |
| `wind_speed_ms` | computed | m/s |
| `wind_dir_deg` | computed | ° (met convention) |

#### Output summary (Natuna Sea, 2024)

```
         u10_ms    v10_ms      hs_m      tp_s  wind_speed_ms  wind_dir_deg
count  8784.000  8784.000  8784.000  8784.000       8784.000      8784.000
mean     -0.273    -0.243     0.588     5.221          4.249        144.682
std       3.021     3.691     0.304     2.104          2.197         99.198
min      -7.640   -10.104     0.127     1.998          0.063          0.088
50%      -0.446    -0.377     0.522     4.586          4.094        156.546
95%       4.621     5.972     1.113     8.740          8.091        330.124
max      11.642     9.937     2.612    11.098         12.972        359.997

Missing values: 0 across all columns ✅
```

The DataFrame is exported to `natuna_timeseries.csv` — the input file for
upcoming tutorials on wave statistics and extreme value analysis.

#### Quick-look time series plot

The notebook produces a **four-panel** plot (Hs, Tp, swell Hs, wind speed / wave direction)
for the first three months of 2024 as a visual sanity check.

---

### Section 5 — Benchmark: speed and memory

The benchmark measures **both time and peak RAM** across all four methods — because
the whole point of lazy loading is saving memory, not just saving time.

**5.1 — Quick live demo** on the 120 MB teaching file (differences are modest on this size).

**5.2 — Pre-computed charts** in `data/benchmark/` from maintainer runs on larger ERA5
files — learners view PNG/CSV only, no multi-GB download.

Representative results on the teaching sample:

| Method | Time | Peak RAM |
|---|---|---|
| Naive full load | ~0.5 s | ~110 MB |
| xarray lazy + sel | ~0.2 s | under 1 MB |
| xarray + Dask | ~2 s | ~10 MB |
| xarray interp | ~1 s | ~7 MB |

Exact numbers vary by machine; the notebook prints live timings in Section 5.1.

!!! note "Why the naive method isn't much slower on the sample"
    The sample file is only 120 MB — it fits in RAM easily.
    On a real 5 GB multi-year ERA5 file, naive loading takes **60–80×** longer
    and often kills the process entirely. The lazy pattern scales.
    See the pre-computed charts in `data/benchmark/` (Section 5.2).

---

### Section 7 — Site statistics

Percentile summary (`mean`, P90, P99, max) for Hs, Tp, swell, and wind — using
`quick_stats()` from `extract_utils`. Useful as a first-pass design-data sanity check
before formal extreme-value analysis.

---

## Repo structure

```
extract-point/
├── notebooks/
│   └── extract_point.ipynb        ← main tutorial (Colab-ready)
├── src/
│   └── extract_utils.py           ← extract_point(), add_wind_speed(), quick_stats()
├── scripts/
│   ├── download_era5.py           ← CDS downloader (maintainers)
│   └── plot_download_area.py      ← domain visualizer
├── benchmarks/
│   ├── benchmark.py               ← standalone benchmark CLI
│   └── generate_assets.py         ← build data/benchmark/ charts (maintainers)
├── data/
│   ├── benchmark/                 ← pre-computed PNG + CSV for Section 5.2
│   └── README.md                  ← sample file spec and regen instructions
├── requirements-notebook.txt      ← tutorial deps (Colab + local notebook)
└── requirements.txt               ← full deps including cartopy / cdsapi
```

The sample NetCDF (`era5_sample.nc`, ~120 MB) is **not** in git — it is hosted on
[GitHub Releases](https://github.com/nusawave-labs/extract-point/releases/tag/v0.1-data)
and downloaded by the Step 0 cell.

---

## Key functions in `extract_utils.py`

```python
extract_point(nc_path, lat, lon, variables=None, method="nearest",
              chunks=None, rename=DEFAULT_RENAME,
              lat_dim=None, lon_dim=None)
```
Opens a NetCDF file lazily, auto-detects lat/lon dimension names, selects or
interpolates at `(lat, lon)`, computes only the required data, renames columns
to human-readable names, and returns a `pandas.DataFrame` indexed by UTC datetime.

```python
add_wind_speed(df, u_col="u10_ms", v_col="v10_ms")
```
Adds `wind_speed_ms` (magnitude) and `wind_dir_deg` (meteorological convention,
clockwise from north) to the DataFrame.

```python
quick_stats(df, var)
```
Returns mean, std, p50, p90, p95, p99, max, and count for any column —
the starting point for wave statistics and exceedance analysis.

---

## Open data

This tutorial uses [ERA5](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)
from ECMWF via the Copernicus Climate Data Store.
The pre-sliced sample file requires no account — it is hosted directly on GitHub Releases.
To download your own ERA5 slice, see
[`scripts/download_era5.py`](https://github.com/nusawave-labs/extract-point/blob/main/scripts/download_era5.py).
See the [Open Data Stack](../../open-data/index.md) for full access notes.

---

## What's next

In the next release, we will cover the following topics:

| Tutorial | Target | Topic |
|---|---|---|
| Ocean Map | Jul 2026 | Plot SST and wave height with Cartopy |
| Time Series Analysis | Aug 2026 | Resample, trend, seasonal decomposition |
| Waves Basics | Sep 2026 | Hs, Tp, Dir, scatter diagrams, wave rose |

Follow [Nusawave on LinkedIn](https://www.linkedin.com/company/109905023)
for release announcements.

---

## Disclaimer

For **education only**. Output is not certified for engineering design,
regulatory submission, or operational decision-making.
