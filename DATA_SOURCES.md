# Open Data Stack

Monsun Labs tutorials use **open data only**. Gated or proprietary datasets (e.g. restricted national archives) are not used in public Labs content.

## Policy

- Reproducible by anyone with internet access
- Sample slices (<10 MB) live in **tutorial repos**, not this website repo
- Full datasets linked with official access instructions

## Primary Sources

| Source | Library / Access | Variables |
|--------|-------------------|-----------|
| [ERA5](https://cds.climate.copernicus.eu/) | `cdsapi` | Wind, waves, SST, pressure |
| [Copernicus Marine](https://marine.copernicus.eu/) | `copernicusmarine` | Currents, SSH, SST, waves |
| [Open-Meteo Marine](https://open-meteo.com/en/docs/marine-weather-api) | REST API | SST, wave height |
| [NOAA CO-OPS](https://tidesandcurrents.noaa.gov/) | Direct download / API | Tide gauge, sea level |
| [GLOSS](https://www.gloss-sealevel.org/) | Station catalogs | Sea level |
| [IOWAGA WW3](https://polar.ncep.noaa.gov/waves/hindcasts/nopp-phase1.php) | ERDDAP / direct | Hs, Tp, Dir (40-yr hindcast) |
| [Satellite altimetry](https://www.aviso.altimetry.fr/) | Copernicus / AVISO | SSH validation |

## Core Python Libraries

| Library | Role |
|---------|------|
| `xarray` + `netCDF4` | NetCDF read/write, lazy loading |
| `pandas` | Tabular export, time indexing |
| `cartopy` + `matplotlib` | Maps and publication figures |
| `utide` / `pytides` | Tidal harmonic analysis |
| `erddapy` | ERDDAP access (WW3 global) |

## ERA5 Access (full datasets)

1. Register at [Copernicus CDS](https://cds.climate.copernicus.eu/)
2. Install `cdsapi` and configure `~/.cdsapirc`
3. Request subsets by area and time — never download global multi-year files for tutorials

## Sample Data

Pre-sliced tutorial data ships with each tutorial repo. For example, see [extract-point/data/](https://github.com/monsun-labs/extract-point/tree/main/data).

Sample files are for teaching only — not certified for engineering design.

## Attribution

When reusing Monsun Labs data or methods, cite the original data provider and Monsun Labs (CC-BY 4.0 for documentation content).
