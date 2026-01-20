# 查看是否安装CUDA SDK

想在 Ubuntu 系统中检查是否安装了 CUDA SDK（即 CUDA Toolkit，CUDA 开发工具包），我会给出**4 种层层递进的验证方法**，从核心命令到路径、环境变量验证，覆盖不同场景，帮你精准确认安装状态。

### 最核心：检查 CUDA 编译器（nvcc）

`nvcc`是 CUDA SDK 的核心组件（CUDA C/C++ 编译器），只要能查到它的版本，就说明 CUDA SDK 已安装；若提示命令不存在，则未安装（或环境变量未配置）。

```
# 查看CUDA SDK版本（最直接的验证）
nvcc --version
# 或简写
nvcc -V
```

#### 结果解读：

- ✅ 安装成功（示例输出）：

  ```
  nvcc: NVIDIA (R) Cuda compiler driver
  Copyright (c) 2005-2023 NVIDIA Corporation
  Built on Tue_Aug_15_22:02:13_PDT_2023
  Cuda compilation tools, release 12.2, V12.2.140
  Build cuda_12.2.r12.2/compiler.33191640_0
  ```

  - 其中`release 12.2`就是已安装的 CUDA SDK 版本。

  - ❌ 未安装 / 环境变量未配置（示例输出）：

    ```
    Command 'nvcc' not found, but can be installed with:
    sudo apt install nvidia-cuda-toolkit
    ```

    

### 二、检查 CUDA SDK 安装路径（系统默认位置）

CUDA SDK 默认安装在`/usr/local/`目录下，会生成`cuda`软链接（指向实际版本目录，如`cuda-12.2`）。

#### 操作命令：

```
# 列出/usr/local下的CUDA相关目录
ls /usr/local/ | grep cuda
```

#### 结果解读：

- ✅ 安装成功（示例输出）：

  ```
  cuda  cuda-11.8  cuda-12.2
  ```

  `cuda`是软链接，`cuda-11.8`/`cuda-12.2`是具体版本的 SDK 安装目录。

- ❌ 未安装：无任何输出。

  

### 三、验证 CUDA 运行时（区分驱动与 SDK）

`nvidia-smi`命令能查看 NVIDIA 驱动版本，以及驱动**支持的最高 CUDA 版本**（注意：这不是已安装的 SDK 版本，仅代表驱动兼容性）。

```
nvidia-smi
```

#### 结果解读（关键行）：

```
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.104.05             Driver Version: 535.104.05   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
```

- `CUDA Version: 12.2`：驱动支持的最高 CUDA 版本，若你安装的 SDK 版本≤这个数值，就能正常使用；
- 若提示`nvidia-smi: command not found`，说明连 NVIDIA 驱动都没装，CUDA SDK 必然未安装。

### 四、检查 CUDA 环境变量（确认 SDK 是否全局生效）

即使安装了 CUDA SDK，若未配置环境变量，`nvcc`命令也可能找不到，需验证环境变量是否配置。

#### 操作命令：

```
# 检查PATH中是否包含CUDA的bin目录
echo $PATH | grep cuda
# 检查LD_LIBRARY_PATH中是否包含CUDA的lib64目录
echo $LD_LIBRARY_PATH | grep cuda
```

#### 结果解读：

- ✅ 配置成功（示例输出）：

```
/usr/local/cuda/bin:/usr/local/sbin:...  # PATH包含CUDA的bin目录
/usr/local/cuda/lib64:/usr/lib/x86_64-linux-gnu:...  # LD_LIBRARY_PATH包含CUDA的lib64目录
```

- ❌ 未配置：无输出，需手动配置（可在`~/.bashrc`中添加）。

### 补充：检查 conda 虚拟环境中的 CUDA SDK

若你在 conda 虚拟环境中安装了 CUDA Toolkit（而非系统级安装），需先激活环境再验证：

```
# 激活conda环境
conda activate your_env_name
# 查看环境内的CUDA Toolkit
conda list | grep cudatoolkit
```

- 输出示例：`cudatoolkit 11.8.0 py310h7e8668a_0`，说明虚拟环境中安装了对应版本的 CUDA SDK。

### 总结

1. **核心验证**：`nvcc --version`是判断 CUDA SDK 是否安装的最直接依据；
2. **版本区分**：`nvidia-smi`显示的是驱动支持的 CUDA 版本，≠已安装的 SDK 版本；
3. **生效验证**：检查`/usr/local/cuda`路径和环境变量，确认 SDK 是否能全局调用。

如果`nvcc`命令找不到但路径存在，只需配置环境变量即可让 SDK 生效；若路径也不存在，则需重新安装 CUDA SDK。