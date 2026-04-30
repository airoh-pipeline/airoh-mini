# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the `airoh-mini` template — a starting point for structuring a reproducible data analysis. It is built on the [`invoke`](https://www.pyinvoke.org/) task runner and [datalad](https://www.datalad.org/) for data management. The `airoh` pip package provides reusable invoke tasks; this repo customizes them via `tasks.py` and `invoke.yaml`.

## Setup

```bash
pip install -r requirements.txt
# or with conda:
conda env create -n airoh_env -f environment.yml && conda activate airoh_env
```

## Common Commands

```bash
invoke fetch              # Download source data (configured in invoke.yaml under files:)
invoke run                # Full pipeline: run_simulation → run_figure
invoke run-simulation     # Generate simulation_output.csv in output_data/
invoke run-figure         # Execute notebooks, save figures to output_data/
invoke clean              # Remove *.png and *.csv from output_data/
invoke --list             # Show all available tasks
```

## Architecture

**Execution flow:** `invoke run` triggers `run_simulation` → `run_figure` (declared as `pre=` dependencies in `tasks.py`).

- `invoke.yaml` — all path and data config (`output_data_dir`, `source_data_dir`, `notebooks_dir`, `files:` for downloads)
- `tasks.py` — project-specific invoke tasks; imports reusable tasks from `airoh.utils`
- `code/simulation.py` — pure Python analysis logic; called by `run_simulation` task
- `notebooks/` — Jupyter notebooks executed by `run_figure` via `airoh.utils.run_figures`; notebooks receive `OUTPUT_DATA_DIR` and `SOURCE_DATA_DIR` as environment variables
- `source_data/` and `output_data/` — excluded from Git by `.gitignore`; use DataLad to track large files

**Adding a new analysis step:** add a function to `code/`, create or extend a notebook in `notebooks/`, add an invoke task in `tasks.py`, and wire it into the `pre=` chain on `run`.

**DataLad** manages large binary files. The `.gitattributes` file controls which files go to git-annex.
