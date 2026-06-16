# Run Pretraining tutorials

`modelzoo` should have been already [installed](file://home/weiping/Cerebras/modelzoo/myNotes/install_modelzoo.md),
but need to activate the venv into the current shell.

```shell
# Refer to option1 in /home/weiping/Cerebras/cerebras-tutorials/huggingface_auth_token.md
export HF_TOKEN=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

cd $HOME/Cerebras
source .venv/bin/activate # activate the venv
export MODELZOO_PARENT=$(pwd)

```

### Run cerebras-cluster-server-mock
In a separate terminal:
```shell
cd ${HOME}/Cerebras/cerebras-cluster-server-mock

PYTHONPATH=${HOME}/Cerebras/cerebras-appliance-src/cerebras_appliance-2.10.0 \
uv run python server.py --port 9000
```

## 1. Create Model Directory & Copy Configs

```shell
cd ${HOME}/Cerebras

# One time
# gh repo clone dj707chen/cerebras-tutorials
# cp -a modelzoo/src/cerebras/modelzoo/tutorials/pretraining cerebras-tutorials/pretraining_tutorial

cd cerebras-tutorials/pretraining_tutorial
# One time
# cp -a ../train_data_config-my.yaml train_data_config.yaml
ls -l
#        -rw-rw-r-- 1 weiping weiping 1663 May 11 19:54 eeh_config.yaml
#        -rw-rw-r-- 1 weiping weiping 2251 May 11 19:54 model_config.yaml
#        -rw-rw-r-- 1 weiping weiping  812 May 11 19:54 train_data_config.yaml
#        -rw-rw-r-- 1 weiping weiping  813 May 11 19:54 valid_data_config.yaml
```

## 2. Inspect Configs

## 3. Preprocess Data

```shell
# For command cszoo,              refer to https://github.com/dj707chen/modelzoo/blob/aa77afee2baca72b97efe113fbbe44e11a9e460a/myNotes/install_modelzoo.md
# For subcommand data_preprocess, refer to https://github.com/dj707chen/modelzoo/blob/ccbe5f766b5ae02a645b619dd7f01efe0cdd8626/src/cerebras/modelzoo/cli/main.py#L177
cszoo --help

# data_preprocess command defined here:    https://github.com/dj707chen/modelzoo/blob/ccbe5f766b5ae02a645b619dd7f01efe0cdd8626/src/cerebras/modelzoo/cli/main.py#L178
cszoo data_preprocess run --config train_data_config.yaml
cszoo data_preprocess run --config valid_data_config.yaml
```

## 4. Train and Evaluate Model
Due to line 38, we are at ${MODELZOO_PARENT}/cerebras-tutorials/pretraining_tutorial

```shell
# Restore
cp -a model_config.3.CPU.yaml model_config.yaml

if [[ "$(uname)" == "Darwin" ]]; then
    echo "On macOS"
    # macOS only
    sed -i "" \
    "s|data_dir: train_data|data_dir: ${MODELZOO_PARENT}/cerebras-tutorials/pretraining_tutorial/pretraining_tutorial/train_data|" \
    model_config.yaml
    
    sed -i "" \
    "s|data_dir: valid_data|data_dir: ${MODELZOO_PARENT}/cerebras-tutorials/pretraining_tutorial/pretraining_tutorial/valid_data|" \
    model_config.yaml
fi
if [[ "$(uname)" == "Linux" ]]; then
    echo "On Linux"
    # Linux only
    sed -i \
    "s|data_dir: train_data|data_dir: ${MODELZOO_PARENT}/cerebras-tutorials/pretraining_tutorial/pretraining_tutorial/train_data|" \
    model_config.yaml
    
    sed -i \
    "s|data_dir: valid_data|data_dir: ${MODELZOO_PARENT}/cerebras-tutorials/pretraining_tutorial/pretraining_tutorial/valid_data|" \
    model_config.yaml
fi
```

Now you’re ready to launch training. Use the cszoo fit command to submit a job, passing in your updated model config.
This command automatically uses the locations and packages defined in your config.
Click [here](https://training-docs.cerebras.ai/rel-2.10.0/fundamentals/launch-your-job) for more information.
```shell
cszoo fit model_config.yaml --mgmt_namespace default
```