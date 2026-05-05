# Airoh Template: Reproducible Pipelines Made Simple

_why don't you have a cup of relaxing jasmine tea?_

This repository is a template for structuring a reproducible data analysis. Built on the [`invoke`](https://www.pyinvoke.org/) task runner, it lets you go from clean clone to output figures with just a few commands.

The logic is powered by [`airoh`](https://pypi.org/project/airoh/), a lightweight, pip-installable Python package of reusable `invoke` tasks. This repository runs a small demo analysis to show how the template works. It should be easy to adapt to a variety of projects.

⚠️ **Status**: This template is in its early days. Expect rapid iteration and changes.

---

## ✨ TL;DR:

This repository is a [GitHub template](https://github.com/airoh-pipeline/airoh-template/generate). Click **"Use this template"** to create your own analysis project.
```bash
uv sync
uv run invoke fetch
uv run invoke run
```
Voilà — from clone to full reproduction.

---

## 🚀 Quick Start

### **Step 1**: Install dependencies

Using `uv` (recommended):
```bash
uv sync
```
This creates a `.venv` and installs all dependencies from `pyproject.toml`.

Using `pip` (e.g. in a virtual environment):
```bash
pip install -r requirements.txt
```

Using `conda`:
```bash
conda env create -n airoh_env -f environment.yml
conda activate airoh_env
```

---

### **Step 2**: Fetch the source data

```bash
invoke fetch
```

Downloads the configured file(s) listed under `files:` in `invoke.yaml`.

---

### **Step 3**: Run the full pipeline

```bash
invoke run
```

Runs the full analysis pipeline in order. Steps that have already produced output are skipped automatically — only missing outputs are recomputed. To force a full rerun from scratch:

```bash
invoke clean
invoke run
```

---

### **Step 4**: Clean outputs

```bash
invoke clean          # remove all outputs
invoke clean-{name}   # remove outputs of one specific step
```

---

## 🧠 Design principles

Airoh projects follow a few conventions that keep analyses fast, reproducible, and easy to pick up:

- **Analysis in code, visualization in notebooks.** Heavy computation lives in `analysis/` Python modules and is run by `invoke` tasks. Notebooks only read results and produce figures — so they stay fast.
- **Idempotent steps.** Each `run-{name}` task checks whether its outputs already exist and skips if they do. You can call `invoke run` repeatedly while working on a later step without re-running earlier ones.
- **Mirrored clean tasks.** Every `run-{name}` has a matching `clean-{name}` that removes only its outputs. The top-level `clean` calls them all.
- **Smoke test.** Tasks support a `--smoke` flag for a fast minimal run to verify the pipeline end-to-end.

---

## 🧰 Task Overview

| Task             | Description                                              |
| ---------------- | -------------------------------------------------------- |
| `fetch`          | Downloads source data configured in `invoke.yaml`        |
| `run`            | Runs the full pipeline (all `run-{name}` steps in order) |
| `run-{name}`     | Runs one analysis step; skips if outputs already exist   |
| `run-notebooks`  | Executes notebooks and saves figures to `output_data/`   |
| `clean`          | Removes all generated outputs                            |
| `clean-{name}`   | Removes outputs of one specific step                     |

Use `invoke --list` or `invoke --help <task>` for descriptions and usage.

---

## 📁 Folder Structure

| Folder / File  | Description                              |
| -------------- | ---------------------------------------- |
| `analysis/`    | Pure Python analysis logic, called by invoke tasks |
| `notebooks/`   | Jupyter notebooks for visualization (one per figure) |
| `source_data/` | Raw source datasets — see [`source_data/CONTENT.md`](source_data/CONTENT.md) |
| `output_data/` | Generated results and figures — see [`output_data/CONTENT.md`](output_data/CONTENT.md) |
| `tasks.py`     | Project-specific invoke tasks            |
| `invoke.yaml`  | Config: paths, data sources, parameters  |

---

## 🧭 Tips

* Use `invoke --complete` for tab-completion support
* Configure paths and data sources in `invoke.yaml`
* To use this template for a new project, start from [`airoh-template`](https://github.com/airoh-pipeline/airoh-template) and customize `tasks.py` + `invoke.yaml`

---

## 🔁 Want to contribute?

Submit an issue or PR on [`airoh`](https://github.com/SIMEXP/airoh).

---

## Philosophy

Inspired by Uncle Iroh from *Avatar: The Last Airbender*, `airoh` aims to bring simplicity, reusability, and clarity to research infrastructure — one well-structured task at a time.
