# Remote Sensing labs in Python

Remote Sensing labs by Daniel Morais, AI-ported into the form of Jupyter Notebooks. 

| Notebook | Lab | Nbviewer |
|---|---|---|
| `Lab1_python.ipynb` | [Lab 1](https://danielm09.github.io/remotesensing/labs/lab1/): image inspection, composites, export, true/false colour, NDVI/NDWI, burned-area exercise | [take a peek](https://nbviewer.org/github/katchinsky/geotech26/blob/main/remote-sensing/Lab1_python.ipynb) |
| `Lab2_python.ipynb` | [Lab 2](https://danielm09.github.io/remotesensing/labs/lab2/): Sentinel-2 download from CDSE, SCL cloud masking, gap filling, Baseline/NDVI/temporal composites | [take a peek](https://nbviewer.org/github/katchinsky/geotech26/blob/main/remote-sensing/Lab2_python.ipynb) |
| `Lab3_python.ipynb` | [Lab 3](https://danielm09.github.io/remotesensing/labs/lab3/): reference sampling, random/stratified train-test split, Random Forest classification, confusion matrix, NDVI/bi-temporal exercise | [take a peek](https://nbviewer.org/github/katchinsky/geotech26/blob/main/remote-sensing/Lab3_python.ipynb) |

Each notebook quotes the lab's ArcGIS steps word for word, followed by a short note on how the step is done in Python
and where Python offers a better way.

## Setup

Requires [uv](https://docs.astral.sh/uv/).

```bash
uv sync                 # creates .venv with Python ≥3.11 and all dependencies
uv run jupyter lab      # then open Lab1_python.ipynb
```

In VS Code, select `.venv` as the notebook kernel instead.

## Data layout

Lab materials (from the course SharePoint) go in `data/`, one folder per lab. Generated files go in `outputs/`, also
one folder per lab. Both folders stay out of git.

```
remote-sensing/
├── Lab1_python.ipynb  Lab2_python.ipynb  Lab3_python.ipynb
├── data/
│   ├── Lab_1/
│   │   ├── Images/      T29SMC_20260403T112109_B0{2,3,4,8}_10m.jp2   (Lisbon, 2026-04-03)
│   │   └── Exercise/    T29TNE_20250823T112131_B0{2,3,4,8}_10m.jp2
│   │                    T29TNE_20250823T112131_B12_20m.jp2           (central Portugal, 2025-08-23)
│   │                    burned_areas_2025.shp (+ .dbf .shx .prj)     (ICNF perimeters)
│   ├── Lab_2/           study_area.kml
│   │                    S2*_MSIL2A_20260627T…_T29TPE_….SAFE/         (cloudy date, ~1.1 GB unzipped)
│   │                    S2*_MSIL2A_20260629T…_T29TPE_….SAFE/         (gap-filling date)
│   │                    S2*_MSIL2A_20260222T…_T29TPE_….SAFE/         (exercise: winter image, optional)
│   └── Lab_3/           reference_sample.shp (+ .dbf .shx .prj .cpg)
│                        Composite_Baseline.tif  Composite_NDVI.tif   (the course's Lab 2 composites)
│                        Lab_3_Toolbox.atbx                           (ArcGIS only, not needed here)
└── outputs/
    ├── Lab_1/           composites, NDVI/NDWI, burned-area maps
    ├── Lab_2/           Composite_Baseline.tif  Composite_NDVI.tif  Composite_temporal.tif
    └── Lab_3/           class maps, train/test samples (.gpkg), trained model (.joblib)
```

`data/Lab_2` can be a symlink to wherever the `.SAFE` products live. The Lab 2 notebook downloads them from CDSE
into that folder.

The notebooks use this layout by default, with paths relative to the notebook. To keep the data somewhere else, set
these environment variables before starting Jupyter:

| Variable | Default | What it is |
|---|---|---|
| `LAB1_ROOT` | `data/Lab_1` | folder with `Images/` and `Exercise/` |
| `LAB1_OUT` | `outputs/Lab_1` | where Lab 1 writes its rasters |
| `LAB2_DATA` | `data/Lab_2` | folder with `study_area.kml` and the `.SAFE` products |
| `LAB2_OUT` | `outputs/Lab_2` | where Lab 2 writes its composites |
| `LAB3_DATA` | `data/Lab_3` | reference sample and the course's composites |
| `LAB3_COMPOSITES` | `LAB3_DATA` | composites to classify; set to `outputs/Lab_2` to use your own Lab 2 results |
| `LAB3_OUT` | `outputs/Lab_3` | where Lab 3 writes maps, samples and the model |

## Running Lab 1

- A full run takes a few minutes and peaks at about 3 GB of RAM (whole 10980 × 10980 tiles).
- `outputs/Lab_1` grows to about 2.8 GB of GeoTIFFs: the composites, NDVI/NDWI and the burned-area maps. Keep it out of git.
- Indices are computed on **reflectance** = (DN − 1000) / 10 000, i.e. with the L2A offset removed. Set
  `BOA_ADD_OFFSET = 0` in the first code cell to reproduce ArcGIS's raw-DN values.
- The basemap cell (§2) needs internet access for the OpenStreetMap tiles.
- `.gitignore` keeps the data, `outputs/` and `.venv/` out of git.
- Exercise settings you are meant to change: `THRESHOLDS`, `APPLY_MASKS`, `EXCLUDE_LATER_FIRES`.

## Running Lab 3

- Put the Lab 3 materials (`reference_sample.shp`, `Composite_Baseline.tif`, `Composite_NDVI.tif`) in `data/Lab_3`,
  or set `LAB3_DATA`. Set `LAB3_COMPOSITES=outputs/Lab_2` to classify your own Lab 2 composites instead.
- A full run takes about a minute; outputs (class maps, samples, model) go to `outputs/Lab_3`.
- The bi-temporal part of the exercise runs once `Composite_temporal.tif` from the Lab 2 exercise exists.
