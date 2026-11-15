# Remote Sensing labs in Python

The NOVA IMS Remote Sensing labs, redone in Python instead of ArcGIS Pro.

| Notebook | Lab |
|---|---|
| `Lab1_python.ipynb` | [Lab 1](https://danielm09.github.io/remotesensing/labs/lab1/): image inspection, composites, export, true/false colour, NDVI/NDWI, burned-area exercise |

## Setup

Requires [uv](https://docs.astral.sh/uv/).

```bash
uv sync                 # creates .venv with Python ≥3.11 and all dependencies
uv run jupyter lab      # then open Lab1_python.ipynb
```

In VS Code, select `.venv` as the notebook kernel instead.

## Data layout

The lab data (from the course SharePoint) sits next to the notebook:

```
rs/
├── Lab1_python.ipynb
├── Images/      T29SMC_20260403T112109_B0{2,3,4,8}_10m.jp2   (Lisbon, 2026-04-03)
├── Exercise/    T29TNE_20250823T112131_B0{2,3,4,8}_10m.jp2
│                T29TNE_20250823T112131_B12_20m.jp2           (central Portugal, 2025-08-23)
│                burned_areas_2025.shp (+ .dbf .shx .prj)     (ICNF perimeters)
└── outputs/     written by the notebook
```

Use a different location by setting `LAB1_ROOT=/path/to/data` before starting Jupyter.

## Running Lab 1

- A full run takes a few minutes and peaks at about 3 GB of RAM (whole 10980 × 10980 tiles).
- `outputs/` grows to about 2.8 GB of GeoTIFFs: the composites, NDVI/NDWI and the burned-area maps. Keep it out of git.
- Indices are computed on **reflectance** = (DN − 1000) / 10 000, i.e. with the L2A offset removed. Set
  `BOA_ADD_OFFSET = 0` in the first code cell to reproduce ArcGIS's raw-DN values.
- The basemap cell (§2) needs internet access for the OpenStreetMap tiles.
- `.gitignore` keeps the data, `outputs/` and `.venv/` out of git.
- Exercise settings you are meant to change: `THRESHOLDS`, `APPLY_MASKS`, `EXCLUDE_LATER_FIRES`.
