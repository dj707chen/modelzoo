


```shell
cd $HOME/Cerebras # or the parent directory of modelzoo repository
rm -fr .venv
uv venv --python 3.11
pip install --upgrade pip

pip install --editable ./modelzoo
```
        Obtaining file:///Users/weiping/tmp/modelzoo
          Installing build dependencies ... done
          Checking if build backend supports build_editable ... done
          Getting requirements to build editable ... done
          Preparing editable metadata (pyproject.toml) ... done
        INFO: pip is looking at multiple versions of cerebras-modelzoo to determine which version is compatible with other requirements. This could take a while.
        ERROR: Could not find a version that satisfies the requirement cerebras_pytorch==2.10.0 (from cerebras-modelzoo) (from versions: none)
        ERROR: No matching distribution found for cerebras_pytorch==2.10.0
