# cerebras_pytorch

## What it is

`cerebras_pytorch` is a closed-source package built and distributed by **Cerebras Systems**. It is a component of the Cerebras Wafer-Scale Cluster (WSC) software stack that wraps and extends standard PyTorch to run on Cerebras CS-series hardware (wafer-scale chips) with near-perfect linear scaling across millions of cores — without requiring the user to manage distributed computing themselves.

It also provides general ML helpers that work on CPU/GPU, which is why `cerebras_modelzoo` (this repo) depends on it even for non-hardware runs.

- **PyPI page**: https://pypi.org/project/cerebras-pytorch/
- **Docs**: https://docs.cerebras.net/
- **Homepage**: https://cerebras.net/
- **Support**: support@cerebras.net
- **Discord**: https://discord.gg/ZqvYS2e2rY

## Source code

`cerebras_pytorch` is **not open source**. There is no public GitHub repository for it. The package is distributed as a prebuilt wheel through:

1. **PyPI** (public) — general-purpose CPU/GPU builds:
   ```bash
   pip install cerebras_pytorch
   ```
2. **Cerebras CSoft platform** (private, for WSC hardware users) — hardware-accelerated builds installed as part of the Cerebras software stack on the appliance. This is what the `PYTHON-SETUP.md` in this repo refers to when it says "After installing all the Cerebras packages distributed in the CSoft platform."

## Version pinning in this repo

`setup.py` and `requirements.txt` both pin:
```
cerebras_pytorch==2.10.0
```

This version matches `cerebras_modelzoo` version `2.10.0` — the two packages are always released together and must be the same version.

> **Note:** As of 2026-05-03, PyPI only has up to `2.9.0` publicly. Version `2.10.0` is distributed through the Cerebras CSoft platform / private channel.

## Install instructions

For the Cerebras Wafer-Scale Cluster (hardware):
- Follow: https://docs.cerebras.net/en/latest/wsc/getting-started/setup-environment.html#install-cerebras-pytorch

For CPU/GPU only (public PyPI):
```bash
pip install cerebras_pytorch==2.9.0   # latest public version
```

## How it is used in this repo

`cerebras_modelzoo` imports from two main sub-namespaces of `cerebras_pytorch`:

| Namespace | Purpose |
|-----------|---------|
| `cerebras.pytorch.*` | Core backend, metrics, distributed training, nn layers, experimental hooks |
| `cerebras.appliance.*` | Cluster client, logging, storage, environment, debug args |

Example imports found in `src/`:
```python
from cerebras.pytorch.backend import Backend, use_cs
from cerebras.pytorch.metrics import AccuracyMetric, PerplexityMetric
from cerebras.pytorch.distributed import get_worker_state
from cerebras.pytorch.nn import SmoothL1Loss
from cerebras.appliance.log import ClassLogger
from cerebras.appliance.errors import ApplianceNanError
```

There are 51 unique `cerebras.pytorch` / `cerebras.appliance` imports across the `src/` tree.
