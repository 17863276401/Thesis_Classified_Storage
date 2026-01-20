# Ubuntu 22.04 计算机视觉环境配置

在 Ubuntu 22.04 上配置计算机视觉（CV）、图形学（CG）和三维重建（3D Reconstruction）的环境，是一个涉及底层驱动、加速库及高层框架的系统工程。

以下是为您整理的详细配置步骤，分为**基础环境**、**核心加速层**、**三维重建与图形学专用库**以及**开发工具**四个部分。

------

## 一、 基础系统配置

在开始前，建议先更新系统组件。

Bash

```
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential cmake git pkg-config curl wget -y
```

------

## 二、 NVIDIA 驱动与 CUDA 加速层 (核心)

对于 CV 和三维重建，GPU 加速是必须的。

### 1. 安装 NVIDIA 驱动

在“软件和更新” (Software & Updates) -> “附加驱动” (Additional Drivers) 中选择推荐的稳定版（如 `nvidia-driver-535`），或者通过命令行：

Bash

```
sudo ubuntu-drivers autoinstall
# 重启生效
sudo reboot
# 检查是否成功
nvidia-smi
```

### 2. 安装 CUDA Toolkit (推荐 11.8 或 12.1)

三维重建工具（如 COLMAP）和深度学习框架（如 PyTorch）对 CUDA 版本有依赖。建议安装 **CUDA 11.8**，它的兼容性目前最广。

- 访问 [NVIDIA CUDA Archive](https://developer.nvidia.com/cuda-toolkit-archive) 下载对应版本。
- **注意**：安装时不要勾选安装驱动（如果上一步已经装好了）。

### 3. 安装 cuDNN

下载与 CUDA 版本匹配的 cuDNN Debian 包并安装：

Bash

```
sudo dpkg -i cudnn-local-repo-ubuntu2204-*.deb
sudo cp /var/cudnn-local-repo-*/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt update
sudo apt install libcudnn8 libcudnn8-dev
```

------

## 三、 三维重建与计算机视觉库

### 1. COLMAP (SfM 三维重建必备)

COLMAP 是目前最流行的通用三维重建工具。

Bash

```
sudo apt install colmap
```

*注：如果需要最新功能，建议从 [GitHub 源码](https://github.com/colmap/colmap) 编译以支持最新的 CUDA 加速。*

### 2. OpenCV (计算机视觉)

推荐使用 Python 虚拟环境安装，或者直接安装开发版本：

Bash

```
sudo apt install libopencv-dev python3-opencv
```

### 3. Open3D (点云处理与 3D 可视化)

Open3D 是三维数据处理的“瑞士军刀”，支持点云、网格、体素等。

Bash

```
pip install open3d
```

### 4. 图形学基础: OpenGL / GLFW

做图形学实验通常需要底层接口：

Bash

```
sudo apt install libgl1-mesa-dev libglu1-mesa-dev freeglut3-dev libglfw3-dev libglew-dev
```

------

## 四、 深度学习与 Python 环境管理

### 1. 安装 Miniconda

建议使用 Conda 管理不同的实验环境，避免版本冲突。

Bash

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

### 2. 创建 CV/重建 专用环境

Bash

```
conda create -n vision_env python=3.10
conda activate vision_env

# 安装 PyTorch (根据你的 CUDA 版本选择)
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

------

## 五、 推荐工具链对比

为了方便您选择，下表对比了三维重建中常用的库：

| **工具/库**   | **主要用途**       | **推荐理由**                              |
| ------------- | ------------------ | ----------------------------------------- |
| **COLMAP**    | 运动恢复结构 (SfM) | 工业级精度，学术界标杆。                  |
| **Open3D**    | 点云/几何处理      | API 友好，Python 支持极佳。               |
| **PyTorch3D** | 深度学习+3D        | Facebook 出品，适合神经渲染 (NeRF) 实验。 |
| **MeshLab**   | 模型处理/查看      | GUI 强大，用于手工处理重建后的网格。      |

------

### 💡 避坑小提示

1. **GCC 版本**：Ubuntu 22.04 默认使用 GCC 11，但某些老旧的 CUDA 库或三维重建项目可能需要 GCC 10。如果编译报错，可用 `sudo apt install gcc-10 g++-10` 并通过 `update-alternatives` 切换。

2. **环境变量**：安装 CUDA 后，一定要在 `~/.bashrc` 中添加：

   Bash

   ```
   export PATH=/usr/local/cuda/bin:$PATH
   export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
   ```

您是打算先从传统的 SfM 三维重建（如 COLMAP）开始，还是想直接跑最近很火的 NeRF 或 3DGS（3D Gaussian Splatting）实验？我可以为您针对性地提供特定算法的部署建议。