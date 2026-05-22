# Python Project Setup & Dependency Management Guide

This document explains how to use the three primary files for managing a Python project's environment and installation.

---

## Quick reference

| File | Audience | Key command |
|------|----------|-------------|
| `requirements.txt` | pip / CI | `pip install -r requirements.txt` |
| `setup.py` | pip / PyPI | `pip install -e .` |
| `PYTHON-SETUP.md` | Humans | Read it |

---

## 1. `requirements.txt`
**Purpose:** Environment Reproducibility.

This file is a flat list of dependencies used by `pip`. It is best for "locking" a specific environment so that every developer and server runs the exact same code versions.

*   **When to use:** Deploying to production, setting up CI/CD, or sharing a specific working state with teammates.
*   **How to create:** 
    ```bash
    pip freeze > requirements.txt
    ```
*   **How to use:**
    ```bash
    pip install -r requirements.txt
    ```
*   **Best Practice:** Pin your versions (e.g., `torch==2.4.0`) to avoid "it works on my machine" bugs when libraries update.

---

## 2. `setup.py`
**Purpose:** Packaging and Local Installation.

This is the build script for `setuptools`. It defines your project as a library or application that can be "installed" into the Python site-packages.

*   **When to use:** When you want to be able to run `import my_project_module` from any directory, or when you intend to publish your project to PyPI.
*   **Key Feature:** The "Editable" install. Instead of copying files, it creates a link to your source code.
*   **How to use:**
    ```bash
    # Install the project and its dependencies in editable mode
    pip install -e .
    ```
*   **Note:** Modern Python development is shifting towards `pyproject.toml` (which this project already uses via `uv`), but `setup.py` remains common for legacy support or complex build logic.

---

## 3. `PYTHON-SETUP.md`
**Purpose:** Human-Centric Documentation.

While the other files are for tools, this file is for people. It bridges the gap for steps that cannot be automated easily.

*   **What to include:**
    *   **Python Version:** Which version is required (e.g., 3.8.10).
    *   **Virtual Environments:** Commands to create a `venv` or `conda` env.
    *   **System Dependencies:** Non-Python requirements (e.g., `apt-get install build-essential` or CUDA drivers).
    *   **Hardware Specifics:** For this project, instructions on setting up Cerebras CSX or GPU drivers.
    *   **Verification:** A simple command to verify the setup worked (e.g., `python -c "import torch; print(torch.cuda.is_available())"`).

---

## Summary Table

| File | Target | Primary Command |
| :--- | :--- | :--- |
| `requirements.txt` | Environment | `pip install -r requirements.txt` |
| `setup.py` | Package | `pip install -e .` |
| `PYTHON-SETUP.md` | Human | Read it! |

*Note: In this specific project (`gigaGPT`), we are using `uv`. While these standard files exist, you should prefer using `uv sync` as it uses the `pyproject.toml` and `uv.lock` for faster, more reliable management.*