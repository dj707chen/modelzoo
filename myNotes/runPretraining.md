# Run Pretraining tutorials

`modelzoo` should have been already [installed](file://home/weiping/Cerebras/modelzoo/myNotes/install_modelzoo.md),
but need to activate the venv into the current shell.

```shell
# Refer to option1 in /home/weiping/Cerebras/tutorials/huggingface_auth_token.md
export HF_TOKEN=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

cd $HOME/Cerebras
source .venv/bin/activate # activate the venv
export MODELZOO_PARENT=$(pwd)

# gh repo clone dj707chen/cerebras-tutorials
# cp -a modelzoo/src/cerebras/modelzoo/tutorials/pretraining cerebras-tutorials/pretraining_tutorial
cd cerebras-tutorials/pretraining_tutorial
# cp -a ../train_data_config-my.yaml train_data_config.yaml
ls -l
#        -rw-rw-r-- 1 weiping weiping 1663 May 11 19:54 eeh_config.yaml
#        -rw-rw-r-- 1 weiping weiping 2251 May 11 19:54 model_config.yaml
#        -rw-rw-r-- 1 weiping weiping  812 May 11 19:54 train_data_config.yaml
#        -rw-rw-r-- 1 weiping weiping  813 May 11 19:54 valid_data_config.yaml

# For command cszoo,              refer to https://github.com/dj707chen/modelzoo/blob/aa77afee2baca72b97efe113fbbe44e11a9e460a/myNotes/install_modelzoo.md
# For subcommand data_preprocess, refer to https://github.com/dj707chen/modelzoo/blob/ccbe5f766b5ae02a645b619dd7f01efe0cdd8626/src/cerebras/modelzoo/cli/main.py#L177
cszoo --help
cszoo data_preprocess run --config train_data_config.yaml
cszoo data_preprocess run --config valid_data_config.yaml
```