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

pip install --editable ./modelzoo
```

## Verify installation

```shell
cerebras_install_check \
    --mgmt_namespace default \
    --mgmt_address localhost:9000
```
