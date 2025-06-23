# Run Linux-based 3D Gaussian Splatting Projects on Windows with WSL

This guide walks through setting up a WSL2-based development environment for 3D Gaussian Splatting (3DGS) projects on Windows. It includes instructions for installing dependencies, managing CUDA versions, optimizing WSL configuration, and handling common compatibility issues.

## Contents

- [Why WSL for 3DGS](#why-wsl-for-3dgs)
- [System Requirements](#system-requirements)
- [Installation Steps](#installation-steps)
  - [1. Install WSL and Ubuntu](#1-install-wsl-and-ubuntu)
  - [2. Configure WSL for Performance](#2-configure-wsl-for-performance)
  - [3. Install Required Dependencies](#3-install-required-dependencies)
  - [4. Install CUDA Toolkit](#4-install-cuda-toolkit)
- [Tips and Troubleshooting](#tips-and-troubleshooting)
  - [Handling Multiple CUDA Versions](#handling-multiple-cuda-versions)
  - [Determine Which PyTorch + CUDA Version to Install](#determine-which-pytorch--cuda-version-to-install)
  - [Additional Tips](#additional-tips)

---

## Why WSL for 3DGS

Many 3DGS projects rely on Linux-based tooling such as COLMAP and PyTorch with GPU acceleration. WSL2 allows you to run a full Linux environment on Windows, using your NVIDIA GPU through CUDA for training and rendering, without setting up dual boot or a separate Linux machine.

---

## System Requirements

- Windows 10 (Build 19044+) or Windows 11
- NVIDIA GPU with CUDA support
- WSL2 with Ubuntu 20.04 or 22.04
- At least 32 GB RAM recommended

---

## Installation Steps

### 1. Install WSL and Ubuntu

Install WSL with Ubuntu from Command Prompt or PowerShell:

```bash
wsl --install
```

Or install Ubuntu manually:

```bash
wsl --install -d Ubuntu-22.04
```

### 2. Configure WSL for Performance

Edit or create a `.wslconfig` file at `C:\Users\<YourUsername>\.wslconfig:`   # This still needs editing

```bash
[wsl2]
memory=32GB
swap=64GB
```
_Note: choose your system memory total for `memory=`. FOr example, if you have 128 GB of RAM installed on your PC, input `memory=128GB`._

Restart WSL after making changes:

Close your command prompt window and open a new Windows command prompt window. Then type:

```
wsl --shutdown
```

### 3. Install Required Dependencies

Inside WSL, download Git and other essential utilities:

```
sudo apt update && sudo apt upgrade -y
sudo apt install git build-essential cmake curl unzip wget -y
```

Install Conda:

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

For the initialization options, choose `YES`

## 4. Install CUDA Toolkit

For this tutorial, we suggest installed v12.6. You can have multiple installs. See Cheat Sheet for how to manage multiple toolkits.

For additional toolkit version, check out [NVIDIA's Archive](https://developer.nvidia.com/cuda-toolkit-archive)

```
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.6.0/local_installers/cuda-repo-wsl-ubuntu-12-6-local_12.6.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-6-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo dpkg -i cuda-repo-wsl-ubuntu-12-6-local_12.6.0-1_amd64.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-6
```

After installing the CUDA Toolkit 12.6 in WSL2, you need to configure your system to recognize and use it. This involves creating a symbolic link to set CUDA 12.6 as the default version and updating your environment variables so your shell can locate the CUDA tools and libraries.

By exporting the PATH and LD_LIBRARY_PATH variables in your ~/.bashrc, you ensure that these settings are applied automatically every time you open a terminal, making CUDA 12.6 available in all future sessions without needing to reconfigure it manually.

Here’s how to do that:
```
sudo ln -sfn /usr/local/cuda-12.6 /usr/local/cuda
echo -e '\nexport PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

Now confirm that CUDA Toolkit is installed:
```
nvcc --version
```
or 
```
/usr/local/cuda/bin/nvcc --version
```
You should see output like:
```
V12.6.0
```
This confirms that CUDA Toolkit 12.6 is installed and available for use.

---

## Tips and Troubleshooting

### Handling Multiple CUDA Versions
Some projects require older CUDA versions. You can install multiple versions and switch manually.

Example: Install CUDA 11.8 inside WSL
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-ubuntu2004-11-8-local_11.8.0-520.61.05-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2004-11-8-local_11.8.0-520.61.05-1_amd64.deb
sudo apt-key add /var/cuda-repo-ubuntu2004-11-8-local/7fa2af80.pub
sudo apt update
sudo apt install cuda-toolkit-11-8
```

Next, ensure your project uses itL
```
export PATH=/usr/local/cuda-11.8/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64:$LD_LIBRARY_PATH
```

### Determine Which PyTorch + CUDA Version to Install
Some repositories require a specific combination of **PyTorch** and **CUDA Toolkit**. You can usually find this in the README or installation instructions, often in a line like:

> "Tested with PyTorch 1.13.1+cu117 and 2.5.0+cu124"

To choose the right PyTorch version for your setup:

#### 1. Check the PyTorch + CUDA compatibility matrix
- Visit the official [PyTorch previous versions page](https://pytorch.org/get-started/previous-versions/)
- Match the listed **PyTorch version** with your desired **CUDA version**
- Use `pip` commands from the matrix

#### 2. If the project specifies a tested PyTorch version
Install PyTorch **before other dependencies** to avoid version conflicts:

```bash
# Example: PyTorch 2.5.0 with CUDA 12.4 via pip
pip install torch==2.5.0 torchvision==0.20.0+cu124 --index-url https://download.pytorch.org/whl/cu124
```
Or
```
# Example: PyTorch 1.13.1 with CUDA 11.7 via pip
pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 --index-url https://download.pytorch.org/whl/cu117
```

#### 3. Use `torch.version.cuda` to verify
After installation, run the following command to confirm the PyTorch and CUDA versions:

```
python -c "import torch; print(torch.__version__, torch.version.cuda, torch.cuda.is_available())"
```
You should see output like:
```
2.5.0 12.4 True
```
This confirms that PyTorch is installed correctly and that it can access the GPU.


### Additional Tips
- Store project files in WSL (`/home`) not `/mnt/c` for better performance.
- Use `nvidia-smi` in WSL to verify GPU is recognized
- Check CUDA Toolkit version within an environment with `nvcc --version`




