# Install modelzoo

Note: Must run it on Linux, when I ran on MacOS, I got the following errors:
        Obtaining file:///Users/weiping/tmp/modelzoo
          Installing build dependencies ... done
          Checking if build backend supports build_editable ... done
          Getting requirements to build editable ... done
          Preparing editable metadata (pyproject.toml) ... done
        INFO: pip is looking at multiple versions of cerebras-modelzoo to determine which version is compatible with other requirements. This could take a while.
        ERROR: Could not find a version that satisfies the requirement cerebras_pytorch==2.10.0 (from cerebras-modelzoo) (from versions: none)
        ERROR: No matching distribution found for cerebras_pytorch==2.10.0

## Clean up disk
MacBook Pro 14 is small, clean up disk:
Google "clean up disk linux"

```shell
sudo journalctl --vacuum-time=3d
sudo apt autoclean
sudo apt clean
sudo apt autoremove
rm -rf ~/.cache/*
```

## Install modelzoo
https://training-docs.cerebras.ai/rel-2.10.0/getting-started/setup-and-installation

```shell
# or the parent directory of modelzoo repository
cd $HOME/Cerebras

rm -fr .venv
uv venv --python 3.11
uv pip install --upgrade pip
source .venv/bin/activate

uv pip install --editable ./modelzoo
```

## macOS: manually install cerebras_install_check

On macOS, `cerebras_pytorch` is installed as an editable source install (see `pyproject.toml`),
so its wheel data scripts are never extracted into `.venv/bin/`. Copy the script manually:

```shell
if [[ "$(uname)" == "Darwin" ]]; then
    cp -a cerebras-pytorch-src/cerebras_pytorch-2.10.0/cerebras_pytorch-2.10.0.data/scripts/cerebras_install_check \
        .venv/bin/
    sed -i '' "1s|#!python|#!$(pwd)/.venv/bin/python|" .venv/bin/cerebras_install_check
    chmod +x .venv/bin/cerebras_install_check
fi
```

## Verify installation

```shell
# The command was created by the cerebras_pytorch package (version 2.10.0),
# which is a dependency that got installed when you ran
#     uv pip install --editable ./modelzoo
# on line 37.
# Source is here: https://github.com/dj707chen/cerebras-pytorch-src/blob/e7b7e2b497c689a36fb242b972cc8c5113918acb/cerebras_pytorch-2.10.0/cerebras_pytorch-2.10.0.data/scripts/cerebras_install_check
# uv then placed it in .venv/bin to make it visible.
cerebras_install_check \
    --mgmt_namespace default \
    --mgmt_address localhost:9000
```
