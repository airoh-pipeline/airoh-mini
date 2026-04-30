# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the `airoh-mini` template — a starting point for structuring a reproducible data analysis. It is built on the [`invoke`](https://www.pyinvoke.org/) task runner. The `airoh` pip package provides reusable invoke tasks; this repo customizes them via `tasks.py` and `invoke.yaml`.

## Setup

```bash
# uv (recommended):
uv sync

# pip:
pip install -r requirements.txt

# conda:
conda env create -n airoh_env -f environment.yml && conda activate airoh_env
```

## Common Commands

With `uv`:
```bash
uv run invoke fetch           # Download source data
uv run invoke run             # Full pipeline (project-specific pre= chain)
uv run invoke run-notebooks   # Execute notebooks, save figures to output_data/
uv run invoke clean           # Remove output_data/ contents
uv run invoke --list          # Show all available tasks
```

Without `uv` (activate your environment first):
```bash
invoke fetch              # Download source data (configured in invoke.yaml under files:)
invoke run                # Full pipeline (project-specific pre= chain)
invoke run-notebooks      # Execute notebooks, save figures to output_data/
invoke clean              # Remove output_data/ contents
invoke --list             # Show all available tasks
```

## Architecture

**Execution flow:** `invoke run` triggers the project's analysis pipeline via `pre=` dependencies declared in `tasks.py`. The three permanent tasks — `fetch`, `run`, `clean` — are always present; intermediate steps are project-specific.

- `invoke.yaml` — all path and data config (`output_data_dir`, `source_data_dir`, `notebooks_dir`, `files:` for downloads)
- `tasks.py` — project-specific invoke tasks; imports reusable tasks from `airoh.utils`
- `analysis/` — pure Python analysis logic, called by tasks in `tasks.py`
- `notebooks/` — Jupyter notebooks executed by `run_notebooks` via `airoh.utils.run_notebooks`; notebooks receive `OUTPUT_DATA_DIR` and `SOURCE_DATA_DIR` as environment variables
- `source_data/CONTENT.md` and `output_data/CONTENT.md` — authoritative docs for what each data folder contains; update these when data assets change, do not duplicate their content elsewhere

**Template cleanup:** When starting a new project from this template, remove the demo code before adding project-specific work:
- Delete `run_simulation` from `tasks.py` and remove it from the `pre=` chains on `run_notebooks` and `run`
- Delete `analysis/simulation.py` (and the `analysis/` folder if it stays empty)
- Clear or replace `source_data/CONTENT.md` and `output_data/CONTENT.md` with project-specific descriptions
- Update `invoke.yaml` (`files:`, paths) for the new project's data sources

**Adding a new analysis step:** add a function to `analysis/`, create or extend a notebook in `notebooks/`, add an invoke task in `tasks.py`, and wire it into the `pre=` chain on `run`.

**README.md** is the user-facing documentation for this project. Any structural or workflow changes — new tasks, renamed folders, updated commands, new dependencies — must be reflected there. For data folder contents, point to `source_data/CONTENT.md` and `output_data/CONTENT.md` rather than duplicating their content inline.
