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
