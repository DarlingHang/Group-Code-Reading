# Installation of Instant NGP on Ubuntu 20.04

**Update date: 2022-02-24**

## Installation of C++ Compiler

```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt update
sudo apt install gcc-11 g++-11
gcc-11 --version
```

You may change the soft link of `gcc` to `gcc-11` and `g++` to `g++-11` as follow:

```bash
cd /usr/bin
## if there exists old `gcc` and `g++` linkage, do this
# sudo rm gcc
# sudo rm g++
sudo ln -s gcc-11 gcc
sudo ln -s g++-11 g++
```

## Installation of CMake

Using

```bash
sudo apt install cmake
```

**will not** get the newest version of CMake, so we can install by source code:

```bash
wget https://github.com/Kitware/CMake/releases/download/v3.22.2/cmake-3.22.2.tar.gz
tar -xzf cmake-3.22.2.tar.gz

cd cmake-3.22.2/
sudo apt-get install libssl-dev
./bootstrap

make -j12
sudo make install
## check cmake path and version
# which cmake
# cmake --version
```

## Installation of CUDA ( CUDA 11.6 Required! )

First check if the NVidia driver is downloaded.

Second, see [NVIDIA CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

```bash
vim ~/.bashrc
> export PATH=/usr/local/cuda-11.6/bin:$PATH
> export LD_LIBRARY_PATH=/usr/local/cuda-11.6/lib64:$LD_LIBRARY_PATH
# check if success
# nvcc -V
```

## Installation of Anaconda

```bash
wget https://repo.anaconda.com/archive/Anaconda3-2021.11-Linux-x86_64.sh
sudo sh Anaconda3-2021.11-Linux-x86_64.sh
## for specifying the installation path, use:
# sudo ./Anaconda3-2021.11-Linux-x86_64.sh

## add path to environment
vim ~/.bashrc
> export PATH=<YOUR_CONDA_ROOT_PATH>/anaconda3/bin:$PATH
source ~/.bashrc
```

## Installation of Instant NGP

```bash
git clone https://github.com/NVlabs/instant-ngp.git # --depth=1
cd instant-ngp
git submodule update --init --recursive
```

Then follow the instruction in its readme.md.
