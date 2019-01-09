# Up and running with Anaconda3 + PyTorch 1.0 + OpenAI Gym + others to serve a JupyterHub
## Installing Anaconda3
- Download the latest x86_64 Linux Anaconda3 installer for Linux https://repo.continuum.io/archive/
- As a sudo non-root user

```
cd ~/Downloads
curl -O https://repo.continuum.io/archive/Anaconda3-5.3.1-Linux-x86_64.sh
bash ~/Downloads/Anaconda3-5.3.1-Linux-x86_64.sh
==> Choose “Install Anaconda as a user”
Finished. logout/login
```

### Updating Anaconda3

```
conda update conda                                   # base conda installation
conda update anaconda                                # packages
conda list                                           # List all packages
```

## Create a new Python3.6 virtual env with the name 'pytorch'

```
conda create --name pytorch anaconda python=3.6
conda activate pytorch      
```

**NOTE: from here on execute conda cmds in the activated pytorch venv.**

```
conda update --all          # update all packages in the env
```

## Install PyTorch Conda package for CUDA 10
Determine the right conda install command for pytorch at [PyTorch](https://pytorch.org/).
For PyTorch_stable/Linux/Conda/Python3.6/CUDA10.0 the conda install command is
**(in the activated pytorch venv)**:

```
conda install pytorch torchvision cuda100 -c pytorch
```

### Test it

```
conda activate pytorch
python
>>> import torch
>>> print(torch.__version__)
1.0.0
>>> 
>>> torch.cuda.is_available()
True
>>> torch.cuda.get_device_name(0)
'GeForce RTX 2080'
>>> torch.cuda.device_count()
1
```

## Installation of OpenAI Gym (minimal version, Cart-Pole, etc.)

```
sudo apt install git
conda activate pytorch
git clone https://github.com/openai/gym
cd gym
pip install -e .
```

### Test

```
>>> import gym
>>> print(gym.__version__)
0.10.9
```

### Rendering on a server
If you're trying to render video on a server, i.e. Cart-Pole, you'll need to connect a fake display. 
The easiest way to do this is by running under xvfb-run.

```
sudo apt install xvfb
xvfb-run -s "-screen 0 1400x900x24" bash
```

## Add some other useful packages under the venv
### Jupyter Notebook Diff and Merge tool

```
pip install nbdime
```
See [jupyter/nbdime](https://github.com/jupyter/nbdime).

```
Usage:
nbdiff ~/tmp/FullyConnectedNets.ipynb FullyConnectedNets.ipynb

nbdiff           compare notebooks in a terminal-friendly way
nbmerge          three-way merge of notebooks with automatic conflict resolution
nbdiff-web       shows you a rich rendered diff of notebooks
nbmerge-web      gives you a web-based three-way merge tool for notebooks
nbshow           present a single notebook in a terminal-friendly way
```


### imread -  read image from disk, for one of the cs231n assignments.

```
sudo apt install libpng-dev libtiff5 libtiff5-dev
sudo apt install xcftools
conda activate pytorch
pip install imread
```

### Graphviz - print computational graph from PyTorch

```
pip install graphviz
```
