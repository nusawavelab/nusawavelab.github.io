---
icon: lucide/database
---

# Open Data Stack

Nusawave Labs uses **open data only** — reproducible tutorials with ERA5, Copernicus Marine, NOAA, GLOSS, and IOWAGA.

## Primary Sources

| Source | Access | Used for |
|--------|--------|----------|
| [ERA5](https://cds.climate.copernicus.eu/) | `cdsapi` | Wind, waves, SST, pressure |
| [Copernicus Marine](https://marine.copernicus.eu/) | `copernicusmarine` | Currents, SSH, SST, waves |
| [Open-Meteo Marine](https://open-meteo.com/en/docs/marine-weather-api) | REST API | SST, wave height |
| [NOAA CO-OPS](https://tidesandcurrents.noaa.gov/) | Download / API | Tide gauge, sea level |
| [GLOSS](https://www.gloss-sealevel.org/) | Station catalogs | Sea level |
| [IOWAGA WW3](https://polar.ncep.noaa.gov/waves/hindcasts/nopp-phase1.php) | ERDDAP | Hs, Tp, direction |
| Satellite altimetry | Copernicus / AVISO | SSH validation |

## Core Libraries

| Library | Role |
|---------|------|
| `xarray` + `netCDF4` | NetCDF I/O, lazy loading |
| `pandas` | CSV export, time indexing |
| `cartopy` + `matplotlib` | Maps and figures |
| `utide` / `pytides` | Tidal analysis |
| `erddapy` | ERDDAP / WW3 access |

## Sample Data

Pre-sliced files ship with each tutorial repo — for example, [extract-point/data/](https://github.com/nusawave-labs/extract-point/tree/main/data).

Full access instructions and attribution requirements are in [DATA_SOURCES.md](https://github.com/nusawave-labs/nusawave-labs.github.io/blob/main/DATA_SOURCES.md) on GitHub.

## Dependencies

Each tutorial repo pins its own `requirements.txt` or `environment.yml`. Check the tutorial repo before running locally or in Colab.
