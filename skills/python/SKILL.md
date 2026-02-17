---
name: python
description: Guidelines for Python development, including environment setup and dependency management.
---

# Python

## Virtual Environment
Ensure you are working within a virtual environment.

Check if `.venv/bin/activate` exists. If not, initialize it:

```bash
python3 -m venv .venv
```

Activate the environment:

```bash
source .venv/bin/activate
```

## Dependencies
If the project uses `pip` for dependency management (indicated by `setup.py` or `pyproject.toml`), install the package in editable mode to test changes:

```bash
pip install -e .
```
