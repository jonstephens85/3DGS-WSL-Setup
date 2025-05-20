# Run Linux-based 3D Gaussian Splatting Projects on Windows with WSL

This guide walks through setting up a WSL2-based development environment for 3D Gaussian Splatting (3DGS) projects on Windows. It includes instructions for installing dependencies, managing CUDA versions, optimizing WSL configuration, and handling common compatibility issues.

## Contents

- [Why WSL for 3DGS](#why-wsl-for-3dgs)
- [System Requirements](#system-requirements)
- [Step 1: Install WSL and Ubuntu](#step-1-install-wsl-and-ubuntu)
- [Step 2: Configure WSL for Performance](#step-2-configure-wsl-for-performance)
- [Step 3: Install Required Dependencies](#step-3-install-required-dependencies)
- [Step 4: Set Up CUDA Toolkit](#step-4-set-up-cuda-toolkit)
- [Step 5: Clone and Run a 3DGS Project](#step-5-clone-and-run-a-3dgs-project)
- [Step 6: Handling Older CUDA Versions](#step-6-handling-older-cuda-versions)
- [Tips & Troubleshooting](#tips--troubleshooting)

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

## Step 1: Install WSL and Ubuntu

Install WSL with Ubuntu from Command Prompt or PowerShell:

```bash
wsl --install
```

Or install Ubuntu manually:

```bash
wsl --install -d Ubuntu-22.04
```

## Step 2: Configure WSL for Performance

Edit or create a `.wslconfig` file at `C:\Users\<YourUsername>\.wslconfig:`   # This still needs editing

```bash
[wsl2]
memory=32GB
processors=8
swap=64GB
```

Restart WSL after making changes:

```bash
wsl --shutdown
```

## Step 3: Install Required Dependencies

Inside WSL, download Git and other essential utilities:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install git build-essential cmake curl unzip wget -y
```

Install Conda:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

For the initialization options, choose `YES`

## Step 4: Install CUDA Toolkit

For this tutorial, we suggest installed v12.6. You can have multiple installs. See Cheat Sheet for how to manage multiple toolkits.

For additional toolkit version, check out [NVIDIA's Archive](https://developer.nvidia.com/cuda-toolkit-archive)

```
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.6.0/local_installers/cuda-repo-wsl-ubuntu-12-6-local_12.6.0-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-6-local_12.6.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-6-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-6
```

After installing the CUDA Toolkit in WSL2, you need to add the CUDA binaries to your system's PATH and the libraries to your LD_LIBRARY_PATH. Here's how to do that for CUDA 12.6:

```
export PATH=/usr/local/cuda-12.6/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.6/lib64:$LD_LIBRARY_PATH
source ~/.bashrc
```

Now confirm that CUDA Toolkit is installed:
```
nvcc --version
```

## Tips and Troubleshooting

### Handling Multiple CUDA Versions (e.g. 11.8 and 12.6)
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

## Additional Tips:
- Store project files in WSL (`/home`) not `/mnt/c` for better performance.
- Use `nvidia-smi` in WSL to verify GPU is recognized
- Check CUDA Toolkit version within an environment with `nvcc --version`




