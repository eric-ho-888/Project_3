# Setup — Environment Guide

**Supported environments:**
- **macOS** (Apple Silicon — M1 or later)
- **Windows 10/11 with WSL2** (Ubuntu 22.04 recommended)
- **Google Colab** (handy fallback if local install gives trouble)

**Supported IDE / runtime:**
- **VS Code + Jupyter extension** (recommended)
- **Google Colab** (browser-based fallback)

You do **not** need Jupyter Notebook or JupyterLab installed separately — VS Code handles everything.

---

## TL;DR — fastest path

```bash
# 1. Clone the repo
git clone https://github.com/eric-ho-888/Project_3.git
cd Project_3

# 2. Create the conda environment
conda env create -f environment.yml
conda activate m3t3

# 3. Install the L01 extras (Hugging Face transformers + torch)
pip install transformers torch

# 4. Launch VS Code in the repo root
code .
```

Then in VS Code:
1. Open `notebooks/01_check_data.ipynb`
2. Top-right → **Select Kernel** → `m3t3` (Python 3.11)
3. **Run All**

If a notebook hangs or errors, scroll down to **Troubleshooting**.

---

## 1. Install Python via Miniconda

We use conda to manage a self-contained Python environment for the course. This avoids polluting your system Python.

### macOS (Apple Silicon)

```bash
curl -L https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh -o miniconda.sh
bash miniconda.sh -b -p $HOME/miniconda3
$HOME/miniconda3/bin/conda init "$(basename $SHELL)"

# Restart your terminal, then verify
conda --version
```

### Windows WSL (Ubuntu)

Open Ubuntu in WSL, then:

```bash
curl -L https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -o miniconda.sh
bash miniconda.sh -b -p $HOME/miniconda3
$HOME/miniconda3/bin/conda init bash

# Restart your terminal, then verify
conda --version
```

> **Windows users — do NOT install Miniconda on Windows directly.** Use WSL. Notebooks that use PyTorch are much more reliable on Linux (WSL is Linux) than native Windows.

---

## 2. Clone the repo

### macOS

```bash
mkdir -p ~/repos
cd ~/repos
git clone https://github.com/eric-ho-888/Project_3.git
cd Project_3
```

### Windows WSL

Clone **inside WSL**, not in the Windows file system. Notebooks run *much* faster on the WSL filesystem (`~/repos/...`) than on `/mnt/c/...`.

```bash
mkdir -p ~/repos
cd ~/repos
git clone https://github.com/eric-ho-888/Project_3.git
cd Project_3
```

---

## 3. Create the conda environment

From the cloned repo root:

```bash
conda env create -f environment.yml
conda activate m3t3
```

This creates an environment named `dsai-m3` with Python 3.11 and the L01 baseline packages.

### Install extras

We may use pretrained sentiment / NLP models via Hugging Face's `transformers` library:

```bash
pip install transformers torch
```

Versions known to work:
- `torch >= 2.2`
- `transformers >= 4.40`

### Re-create the environment

Use this when the environment is broken (import errors, version conflicts, half-finished install), when you moved to a new machine, or when `environment.yml` changed after a `git pull`.

**Option A — update in place (try this first, keeps what already works):**

```bash
cd ~/repos/Project_3
conda activate m3t3
conda env update -f environment.yml --prune
```

`--prune` removes packages no longer listed in `environment.yml`.

**Option B — full rebuild (clean slate):**

```bash
# 1. Leave the env, then delete it
conda deactivate
conda env remove -n m3t3

# 2. Re-create it from the repo root
cd ~/repos/Project_3
conda env create -f environment.yml
conda activate m3t3

# 3. Re-install the L01 extras (safe to repeat)
pip install transformers torch
```

**Then check it worked:**

```bash
conda env list                      # m3t3 should appear
python --version                    # Python 3.11.x
python -c "import torch, transformers, sklearn; print('ok')"
```

**Afterwards in VS Code:**
1. **Cmd/Ctrl+Shift+P → "Developer: Reload Window"**
2. Open a notebook → **Select Kernel** → `dsai-m3`. If it's missing, register it (see Troubleshooting).

> **Notes**
> - Removing the env does **not** delete the Hugging Face model cache (`~/.cache/huggingface/`), so you won't re-download the ~268 MB model.
> - Your notebooks and files in the repo are untouched. Only the `dsai-m3` environment is deleted.
> - Don't hand-edit packages with `pip install` and expect them to survive a rebuild. Add anything you need to `environment.yml`, then rebuild.


## 4. Verify your setup

Open `notebooks/01_check_data.ipynb`.

1. **Top-right of the notebook → "Select Kernel"** → pick `dsai-m3` (Python 3.11)
2. Run the **first code cell** (`import pandas as pd...`)
3. You should see `✅ Libraries loaded — you're ready to go!`

## Quick reference

| Task | Command |
|---|---|
| Activate the env | `conda activate dsai-m3` |
| Update deps | `pip install -U transformers torch` |
| Sync env with `environment.yml` | `conda env update -f environment.yml --prune` |
| Delete env (before rebuild) | `conda deactivate && conda env remove -n dsai-m3` |
| Re-create env | `conda env create -f environment.yml` |
| Launch VS Code in repo | `code .` (from the repo root) |
| Force-kill stuck kernel | `pkill -9 -f ipykernel` |
| Reload VS Code | `Cmd/Ctrl+Shift+P → "Developer: Reload Window"` |
