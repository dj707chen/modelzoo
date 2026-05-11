# Python Project Setup Guide

This guide explains how to use `PYTHON-SETUP.md`, `setup.py`, and `requirements.txt` in a Python project — what each file does, when to use it, and how they work together.

---

## 1. `requirements.txt`

**Purpose:** Pin exact dependency versions so the environment is reproducible across machines.

### Format

```
torch==2.4.0
transformers==4.45.2
tqdm>=4.66.0
```

- `==` pins an exact version
- `>=` / `<=` sets a minimum or maximum
- No operator means "any version" (avoid in production)

### Common commands

```bash
# Install all dependencies
pip install -r requirements.txt

# Freeze your current environment into a requirements file
pip freeze > requirements.txt

# Install into a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate          # macOS/Linux
.venv\Scripts\activate             # Windows
pip install -r requirements.txt
```

### When to use it

- Scripting projects, research code, or any project that is **not** distributed as a library.
- CI/CD pipelines that need a fast, repeatable install.
- Works alongside `setup.py` — `requirements.txt` handles dev/runtime pins, `setup.py` declares abstract dependencies for the package itself.

---

## 2. `setup.py`

**Purpose:** Package your project so it can be installed, distributed (to PyPI), and imported as a module from anywhere.

### Minimal example

```python
from setuptools import setup, find_packages

setup(
    name="my_project",
    version="0.1.0",
    packages=find_packages(),
    install_requires=[
        "torch>=2.4.0",
        "transformers>=4.45.0",
    ],
    python_requires=">=3.9",
)
```

### Common commands

```bash
# Install the project in editable mode (changes to source are reflected immediately)
pip install -e .

# Build a distributable wheel/tarball
pip install build
python -m build

# Upload to PyPI
pip install twine
twine upload dist/*
```

### When to use it

- You want to `import my_project` from anywhere without modifying `PYTHONPATH`.
- You are distributing the package to others (PyPI, internal registry).
- You have multiple sub-packages that `find_packages()` should discover automatically.

> **Note:** Modern projects prefer `pyproject.toml` (PEP 517/518) over `setup.py`. A `pyproject.toml` with `[build-system]` and `[project]` sections replaces `setup.py` entirely. Both approaches work; `setup.py` remains widely supported.

---

## 3. `PYTHON-SETUP.md`

**Purpose:** Human-readable onboarding document that walks contributors through environment setup end-to-end.

### What to include

```markdown
## Prerequisites
- Python 3.9+
- CUDA 12.x (if using GPU)

## Quickstart
1. Clone the repo: `git clone ...`
2. Create a virtual environment: `python -m venv .venv && source .venv/bin/activate`
3. Install dependencies: `pip install -r requirements.txt`
4. (Optional) Install package in editable mode: `pip install -e .`
5. Run a quick sanity check: `python sample.py`

## Environment variables
| Variable | Default | Description |
|----------|---------|-------------|
| `MODEL_PATH` | `./checkpoints` | Where model weights are saved |
```

### When to use it

- Onboarding new contributors or collaborators.
- Documenting GPU/CUDA requirements, environment variables, and non-pip setup steps that cannot go into `requirements.txt` or `setup.py`.
- Providing project-specific instructions that differ from a generic `pip install`.

---

## 4. How to run `setup.py` (this repo)

The `setup.py` in this repo has an `ErrorOnInstall` guard that blocks the normal `python setup.py install` and non-editable `pip install` paths. The **only supported invocation** is:

```bash
pip install -e .
# or
pip install -e /path/to/modelzoo
```

**Why not `python setup.py install`?**
Running it directly (or `pip install` without `-e`) triggers the `ErrorOnInstall.run()` override, which immediately raises:

```
RuntimeError: The package `cerebras_modelzoo` is not a mountable package on the
appliance since it is not available in PyPI. It must be installed as an
editable package using:
  $ pip install -e /path/to/cerebras/modelzoo
```

**What `-e` (editable) does:**
- `pip` uses the `develop` setuptools command, which bypasses `install` entirely — so `ErrorOnInstall` is never triggered.
- The `src/` directory is linked directly into your Python environment (via a `.pth` file), so edits to source files take effect immediately without reinstalling.
- The `cszoo` CLI entry point is registered in your environment's `bin/`.

---

## 5. Where does `setuptools` come from?

`setuptools` is a third-party Python package (not part of the standard library), but it comes **pre-installed with pip and most Python distributions**.

**Where it physically lives:**

```bash
python -c "import setuptools; print(setuptools.__file__)"
# e.g. /usr/local/lib/python3.10/site-packages/setuptools/__init__.py
```

**How it gets there:**
- **Bundled with pip** — when you install Python via `python.org`, `conda`, `homebrew`, or most OS package managers, `pip` and `setuptools` are included by default.
- **PyPI** — if missing, you can install it explicitly: `pip install setuptools`.

`setuptools` is a build-time dependency required to run `setup.py`, but it is intentionally absent from `requirements.txt` and `install_requires` — it is expected to already be present in any environment capable of running `pip install -e .`.

---

## How they work together

```
PYTHON-SETUP.md       ← tells humans what to do
      |
      v
requirements.txt      ← pip installs exact versions for reproducibility
      +
setup.py              ← makes the project importable / distributable
```

Typical developer workflow:

```bash
git clone <repo>
cd my_project
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt   # exact runtime deps
pip install -e .                  # make the package importable (if setup.py exists)
python train.py                   # start working
```

---

## Quick reference

| File | Audience | Key command |
|------|----------|-------------|
| `requirements.txt` | pip / CI | `pip install -r requirements.txt` |
| `setup.py` | pip / PyPI | `pip install -e .` |
| `PYTHON-SETUP.md` | Humans | Read it |
