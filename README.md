# Nusawave Labs

Open-source knowledge hub for metocean analytics — project-first Python tutorials using ERA5, Copernicus, NOAA, and other open data.

**Live site:** [nusawave-labs.github.io](https://nusawave-labs.github.io)

## Two-repo pattern

| Repo | Role |
|------|------|
| [nusawave-labs.github.io](https://github.com/nusawave-labs/nusawave-labs.github.io) | Public website — landing pages, open-data reference |
| [extract-point](https://github.com/nusawave-labs/extract-point) | First tutorial — notebook, code, sample data |

LinkedIn (discovery) → Website (landing pages) → Tutorial repo (Colab + code).

Tutorial notebooks, sample data, and pinned dependencies live in **each tutorial repo**, not in this website repo.

## Quick Start (website)

```bash
pip install zensical
zensical serve          # preview at http://127.0.0.1:8000
zensical build --clean  # output to site/
```

## Repository structure

```
docs/
  index.md              Home page
  tutorials/            Thin landing pages → link to tutorial repos
  open-data/            Open data stack reference
  community/            Contributing
DATA_SOURCES.md         Open-data policy and access notes
```

## Monthly publish checklist

When a new tutorial repo ships:

1. Create `docs/tutorials/<slug>/index.md` (landing page with Colab + GitHub links)
2. Add nav entry in `zensical.toml` under Tutorials
3. Add a row to `docs/tutorials/index.md`
4. Optionally update the previous tutorial's "Next step" link

Do not add nav entries or stub pages before the tutorial repo exists.

## Published tutorials

| Tutorial | Repo |
|----------|------|
| Point Extraction | [extract-point](https://github.com/nusawave-labs/extract-point) |

## Contributing

1. Fork and branch from `main`
2. Website changes: Colab-ready links, open data only, accurate to what exists in tutorial repos
3. Open a PR — site repo code under [MIT](LICENSE), documentation under CC-BY 4.0

See [DATA_SOURCES.md](DATA_SOURCES.md) for open-data access notes.

## License

- **Code:** [MIT](LICENSE)
- **Documentation:** [CC-BY 4.0](docs/LICENSE-CONTENT.md)
