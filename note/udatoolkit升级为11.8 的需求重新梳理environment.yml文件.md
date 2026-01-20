根据你将 `cudatoolkit` 升级为 **11.8** 的需求，我为你重新梳理了 `environment.yml` 文件。

### 核心修改逻辑

1. **PyTorch 升级**：原有的 `1.12.1` 官方正式版并不直接原生支持 CUDA 11.8（1.12 主要针对 11.3/11.6）。为了完美匹配 CUDA 11.8，建议升级到 **PyTorch 2.0.1**，这是目前公认最稳定的 3DGS 开发版本。
2. **移除 `cudatoolkit` (Conda版)**：在编译 Gaussian Splatting 插件时，Conda 的 `cudatoolkit` 往往缺少编译器（nvcc）。建议通过 `nvidia` 频道安装完整的 **`cuda-toolkit`**，或者直接链接你系统的 CUDA。
3. **版本对齐**：同步更新了 `torchvision` 和 `torchaudio` 以匹配新的 Torch 版本。

---

### 更新后的 `environment.yml`

```yaml
name: FSGS
channels:
  - pytorch
  - nvidia
  - conda-forge
  - defaults
dependencies:
  - python=3.8.13
  - pip=22.3.1
  # 升级至支持 CUDA 11.8 的 PyTorch 2.0.1
  - pytorch=2.0.1
  - torchvision=0.15.2
  - torchaudio=2.0.2
  - pytorch-cuda=11.8
  # 基础依赖
  - plyfile=0.8.1
  - tqdm
  - pip:
      - matplotlib==3.5.3
      - torchmetrics==1.2.0
      - timm==0.9.12
      - opencv-python==4.8.1.78
      - imageio==2.31.2
      - open3d==0.17.0
      # 建议先安装完环境，再手动进入子目录执行 pip install .
      # 或者保留在这里，但必须确保系统中环境变量 CUDA_HOME 指向 11.8
      - ./submodules/diff-gaussian-rasterization-confidence
      - ./submodules/simple-knn

```

---

### ⚠️ 安装时的关键步骤（必读）

为了避免你之前遇到的 `TypeError` 和 `No CUDA runtime` 错误，请严格按以下步骤操作：

#### 1. 设置环境变量

在创建环境**之前**，确保你的系统知道 CUDA 11.8 的位置：

```bash
export CUDA_HOME=/usr/local/cuda-11.8
export PATH=$CUDA_HOME/bin:$PATH
export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH

```

#### 2. 创建环境

```bash
conda env create -f environment.yml

```

#### 3. 验证 (如果编译依然失败)

如果 `pip` 在创建环境过程中编译子模块报错，请先注释掉 `environment.yml` 中 `pip` 部分的最后两行（submodules），创建完环境后手动安装：

```bash
conda activate FSGS
# 手动安装子模块，这样能看到详细的错误日志
cd submodules/diff-gaussian-rasterization-confidence
pip install .
cd ../simple-knn
pip install .

```

### 为什么这样改能解决问题？

* **`pytorch-cuda=11.8`**：这是 Conda 现在推荐的安装方式，它会自动关联 PyTorch 和正确的 CUDA 运行时库。
* **Python 3.8.13**：稍微提升了 3.8 的补丁版本，增加了稳定性，同时保持了对旧代码的兼容。
* **版本兼容矩阵**：
| 组件 | 版本 |
| :--- | :--- |
| CUDA | 11.8 |
| PyTorch | 2.0.1 |
| TorchVision | 0.15.2 |

**如果你在手动安装子模块时还遇到问题，可以把最新的报错贴给我，通常是缺少 `g++` 或显卡驱动不匹配。需要我帮你写一个一键安装的 Bash 脚本吗？**

