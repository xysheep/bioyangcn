---
layout: post
title: Set up deep learning system for Linux with Nvidia GPU
excerpt_separator: <!--more-->
---
To prepare deep learning system in Linux with Nvidia GPU, we need to install the following prerequisite.
- [Nvidia driver](#install-nvidia-driver)
- [Cuda toolkit](#install-cuda-toolkit)
- [CuDNN](#install-cudnn)

Then, we can [set up deep learning environments](#set-up-deep-learning-environment):
- [Install conda: Anaconda/miniconda](#install-conda)
- [Create a conda environment](#create-a-conda-environment)
+ [Install deep learning platforms](#install-deep-learning-platforms)
  - [Tensorflow](#tensorflow)
  - [Keras](#keras)
  - [pytorch](#pytorch)
- [cntk (used as backend of Keras)](#cntk)
+ [Install frequently used packages that cannot directly installed from conda](#install-frequently-used-packages)
  - [opencv](#opencv)
<!--more-->


## Install Prerequisite
### Install Nvidia driver
To download GPU driver, we can go to this [link](http://www.nvidia.com/Download/index.aspx "link"), choose our GPU/operation, and click serach (like shown in the following figure). Then we can download the correspond driver. For a linux with GTX Titan X, the newest driver name should be **NVIDIA-Linux-x86_64-384.90.run** as date of 2017-10-09.
Move to the folder that the driver downloaded and run the following script to install it.
```bash
chmod +x NVIDIA-Linux-x86_64-384.90.run
# We need root to install the driver ("sudo" can give us root right, need to type password).
sudo ./NVIDIA-Linux-x86_64-384.90.run
```
### Install Cuda toolkit
Cuda 9.0 is recently released, unfortunately however,** most dl platform currently only support cuda 8.0**. Therefore, we need to download the archived release from [here](https://developer.nvidia.com/cuda-toolkit-archive). On this website, we can download the installer after picking up the operation system (usually choose x86_64 as Architecture). The "**deb(local)**" isntaller is recommanded. To install it, simply type the following script:
```bash
#  file name might vary depends on version of operation system and cuda
sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
sudo apt-key add /var/cuda-repo-8.0.61-1/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda
```
More details about installing cuda is avaliable [here](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
### Install cuDNN
After downloaded Nvidia driver and Cuda toolkit, we can install cnDNN. Newest version of cuDNN can be downloaded from [here](https://developer.nvidia.com/cudnn), people need to register before downloading them. We can download the **cuDNN v7.0 Library for CUDA 8.0 for Linux**, this link will give you file named "**cudnn-8.0-linux-x64-v7.tgz**". Then we can use the following scripts to install cuDNN.
```bash
tar -xzvf cudnn-9.0-linux-x64-v7.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```
More details about installing cuDNN is avaliable [here](http://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html). 

## Set up deep learning environment
Usually, to avoid any package conflictions between different platforms or users, we expect to establish independent environment for different users, platforms, or projects. Conda is a **package**, **dependency**, and **environment** management platform that can easily achieve this goal. We also can use it to easily manage and use different version of softwares.
### Install Conda
### Create a conda environment
To install conda, we have two options: anaconda and miniconda. Compare to miniconda, anaconda has more preinstalled packages. Here, take miniconda as an example, we can run the following command to install miniconda.
```bash
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```
This will install conda for the current user. Then, we can create a new conda environment for tensorflow. As an example, we create a environment named "**tf**"(any name is OK) using the following script.
``` bash
conda create -name tf python=3.6
```
After creating a env, you can enter this env by:
```bash
source activate tf
```
After entering the env, you can install packages (take installing numpy as an example) for this env by:
```bash
conda install numpy
```
Install through **conda** is usually **better** than from **pip** because of less conflict and more comprehensize dependencies.
### Install deep learning platforms
You might install any of the following platforms you like. I recommend you to create an independent conda env for each platform (except Keras).
#### Tensorflow
After creating a env named **tf** in [above section](#Create a conda environment), we can enter that environment by the following command:
```bash
source activate tf
```
And install the newest GPU version of tensorlfow by:
```bash
pip install --ignore-installed --upgradehttps://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.3.0-cp27-none-linux_x86_64.whl
```
Installing tensorflow without conda can be tricky, more details can be found [here](https://www.tensorflow.org/install/install_linux#the_url_of_the_tensorflow_python_package)
#### pytorch
Pytorch is very easy to install on Linux. After entering a conda env, simply type the following command:
```bash
conda install pytorch torchvision cuda80 -c soumith
```
#### Keras
Before installing Keras, you need to have at least one of [tensorflow](#Tensorflow) or [CNTK](#CNTK) installed as backend (Theano is not recommended anymore) in your conda env. At the same conda env, you can use the following command to install Keras.
```bash
pip install keras
```
If this doesn't work (for any reason), try the alternative command:
```bash
conda install -c conda-forge keras
```
After installing keras, we need to pick a backend. Keras use tensorflow by default. We can change it by edit the "~/.keras" file.
#### CNTK
CNTK, thought not very popular, is a great option for Keras backend because it runs much faster than current version of tensorflow. It can be downloaded and installed by the following commands (after entering a conda env).
```bash
sudo apt-get install openmpi-bin
pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.2-cp36-cp36m-linux_x86_64.whl
```
### Install frequently used packages
#### opencv
For python 3.6, opencv cannot be directly installed by using
```bash
conda install opencv
```
Instead, we need to the following script.
```bash
conda install -c menpo opencv3
```
However, this is still not guranteed to work, you might want to do some Google to search for new options.

