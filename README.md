# Southern Ocean Fishing Activity — Data Products

> **State of the Antarctic Environment Report**  
> Spatiotemporal visualisations of commercial fishing intensity in the Southern Ocean, 2017–2024

---

## Overview

This repository contains a fully reproducible [Quarto](https://quarto.org/) notebook that queries the [Global Fishing Watch (GFW) API v3](https://globalfishingwatch.org/our-apis/) to produce publication-ready data products on fishing activity around the **Antarctic Peninsula** (Southern Ocean). It is intended as a contribution to the *State of the Antarctic Environment* reporting framework.

The notebook generates three categories of figures:

| Figure | Description |
|---|---|
| **Annual hotspot heatmaps** | 0.1° gridded maps of apparent fishing hours for each of the last 5 full years, on a shared log₁₀ colour scale |
| **Fishing intensity trend** | Annual total fishing hours (2017–present) with LOESS smoother, plus a year × month anomaly heatmap (z-scores vs long-term monthly mean) |
| **Gear-type composition** | Stacked bar chart of annual effort broken down by gear type (trawlers, longlines, squid jiggers, etc.) |

---

## Repository structure

```
.
├── southern_ocean_fishing.qmd   # Main Quarto notebook (single source file). This file write the .pdf files
├── cache_heatmap_data.rds       # API cache — annual gridded effort (git-ignored)
├── cache_trend_data.rds         # API cache — monthly effort time series (git-ignored)
└── README.md
```

> **Note:** The `.rds` cache files are listed in `.gitignore`. Each collaborator fetches their own copy on first render. See [Caching](#caching) below.

---

## Requirements

### R and Quarto

- **R** ≥ 4.2
- **Quarto** ≥ 1.4 — [install here](https://quarto.org/docs/get-started/)

### R packages

Install all dependencies in one go:

```r
install.packages(c(
  "gfwr",              # GFW API wrapper (v2+, targets API v3)
  "tidyverse",         # data wrangling + ggplot2
  "sf",                # spatial data
  "scico",             # colorblind-safe perceptual colour palettes
  "patchwork",         # multi-panel figure composition
  "ggrepel",           # non-overlapping text labels
  "rnaturalearth",     # land polygons for map backgrounds
  "rnaturalearthdata", # data dependency for rnaturalearth
  "glue"               # string interpolation
  "rlang"              # regex
))
```

### GFW API token

Data are fetched live from the GFW API. A **free token** is required:

1. Register at <https://globalfishingwatch.org/our-apis/>
2. Agree to the [Terms of Use](https://globalfishingwatch.org/our-apis/documentation#introduction) (non-commercial research use; attribution required)
3. Copy your token and add it to your `~/.Renviron` file:

```
GFW_TOKEN=your_token_here
```

4. Restart R. The notebook calls `gfw_auth()` which reads this variable automatically. **Never commit your token to version control.**

---

## Quickstart

```bash
# Clone the repository
git clone https://github.com/your-org/southern-ocean-fishing.git
cd southern-ocean-fishing

# Render the notebook to a self-contained HTML report
quarto render southern_ocean_fishing.qmd
```

The rendered file `southern_ocean_fishing.html` will appear in the working directory. It is fully self-contained (no external assets) and suitable for direct inclusion in a report or sharing with collaborators.

---

## Data source & study area

**Source:** Global Fishing Watch — [AIS-based apparent fishing effort dataset](https://globalfishingwatch.org/dataset-and-code-fishing-effort/), `public-global-fishing-effort:v3.0`. Coverage: 2017-01-01 to ~5 days before the query date.

**Study region:** The **Antarctic Peninsula** 

**Effort metric:** *Apparent fishing hours* — the cumulative time AIS-equipped vessels were algorithmically classified as fishing within each 0.1° × 0.1° grid cell, based on changes in vessel speed and heading.

**Key caveat:** AIS coverage is ~90% for vessels >24 m but substantially lower for smaller vessels. All figures represent a lower bound on total fishing activity. See the [GFW data caveats page](https://globalfishingwatch.org/dataset-and-code-fishing-effort/) for full details.

---

## Caching

The notebook manages API caching manually using `saveRDS` / `readRDS` to avoid redundant API calls across renders. Two cache files are written to the working directory:

| File | Contents | First render time (approx.) |
|---|---|---|
| `cache_heatmap_data.rds` | Annual gridded effort, last 5 years (GEARTYPE grouping) | 5–15 min |
| `cache_trend_data.rds` | Monthly effort, 2017–present (FLAG grouping) | 10–25 min |

The notebook validates the cache on load and **automatically deletes and re-fetches** if a file is empty or corrupt (e.g., from a previous timeout). To force a full refresh from the API, delete both files before rendering:

```r
file.remove("cache_heatmap_data.rds", "cache_trend_data.rds")
```

---

## Updating for a new year

1. Open `southern_ocean_fishing.qmd` and update the `CURRENT_YEAR` variable in the setup chunk:

```r
CURRENT_YEAR <- 2025   # change to the most recent complete calendar year
```

2. Delete the cache files to trigger a fresh API fetch.
3. Re-render.

---

## Attribution & licence

**Data:** All fishing effort data are © Global Fishing Watch. Use of GFW data requires attribution in any publication:

> *Global Fishing Watch. (2024). AIS-based apparent fishing effort (public-global-fishing-effort:v3.0). globalfishingwatch.org*

**Code:** [MIT Licence](LICENSE) — free to use, adapt, and redistribute with attribution.

**GFW Terms of Use:** Data are licensed for non-commercial research and educational use. See the [GFW Terms of Use](https://globalfishingwatch.org/our-apis/documentation#introduction) for full conditions.

---

## Contact

For questions about this repository, open an issue or contact the project maintainer. For questions about the GFW data or API, contact [apis@globalfishingwatch.org](mailto:apis@globalfishingwatch.org).
