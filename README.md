# pytorch_conda_jupyterhub
Building a JupyterHub Server.

__*The info in this repo is current/updated as of December 19th, 2018.*__

## Hardware
* Housing: be quiet! - Pure Base 600
* CPU: Intel Core i3-8100, 4 x 3.60 GHz, 6MB L3-Cache
* GPU: NVIDIA GEFORCE RTX 2080
* Mainboard: MSI Z390-A Pro, Intel Z390
* RAM: 32GB DDR4-2400, 2 x 16GB
* SSD (M.2 / PCIe): 1TB Samsung 970 EVO
* Power: 850W - Corsair HXi Platinum Series, digital
* CPU-Cooler: be quiet! Dark Rock Pro 4 


## Versions
* Ubuntu 18.04
* CUDA10
* NVIDIA driver is installed as part of the CUDA installation.
* cuDNN v7.4.2 (Dec 14, 2018), for CUDA 10.0
* Anaconda3
* PyTorch 1.0
* Jupyterhub 0.9.4 (spawns jupyterlab-hub)

## Nvidia-drivers, CUDA 10, cuDNN

[Installing the Nvidia-driver and CUDA toolkit on Ubuntu 18.04](https://tikoehle.github.io/pytorch_conda_jupyterhub/nvidia_cuda)

[Installing cuDNN Libs on Ubuntu 18.04](https://tikoehle.github.io/pytorch_conda_jupyterhub/nvidia_cuDNN)


## Up and running with Anaconda3 + PyTorch 1.0 + OpenAI Gym + others to serve a JupyterHub
[Installing the Deep Learning environment](https://tikoehle.github.io/pytorch_conda_jupyterhub/anaconda3_pytorch)

## Up and running with JupyterHub 0.9.4 spawning a Jupyter-LabHub
[Installing and running JupyterHub 0.9.4](https://tikoehle.github.io/pytorch_conda_jupyterhub/jupyter_labhub)
