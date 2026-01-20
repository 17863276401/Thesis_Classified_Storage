# 在Linux中用conda建一个新的虚拟环境

前置准备：确认 conda 已安装并初始化

首先要确保你的 Linux 系统中已经安装了 Anaconda/Miniconda，且 conda 命令能正常使用：

检查 conda 版本（验证安装）：

```
conda --version
```

若输出`conda 23.x.x`等版本号，说明安装成功；若提示`conda: command not found`，需先初始化 conda（刚安装完 Anaconda/Miniconda 可能需要）：

```
# 初始化conda（bash Shell）
source ~/.bashrc
# 或重新加载conda配置
conda init bash
```

执行后关闭终端重新打开，再执行`conda --version`即可正常识别。

### 步骤 1：创建新的 conda 虚拟环境

#### 基础创建命令（最常用）

格式：`conda create -n 环境名 python=Python版本`

示例（创建名为`my_env`、Python 版本为 3.9 的环境）：

```
conda create -n my_env python=3.9
```

### 步骤 2：激活 / 退出 conda 虚拟环境

创建完成后，必须激活环境才能使用（否则默认用系统 Python）：

1. **激活环境**（核心操作）：

```
conda activate my_env
```

激活成功后，终端提示符开头会显示`(my_env)`，表示已进入该虚拟环境（此时安装的包都会保存在这个环境中，不影响系统 / 其他环境）。

**退出环境**（回到系统默认环境）：

```
conda deactivate
```

执行后提示符的`(my_env)`会消失，回到基础环境。

步骤 3：conda 虚拟环境常用管理命令

查看所有已创建的 conda 环境

```
conda info --envs
# 或简写
conda env list
```

输出会列出所有环境的名称和路径，比如：

```
base                  /home/ubuntu2204/miniconda3
my_env              * /home/ubuntu2204/miniconda3/envs/my_env
```

`*`表示当前激活的环境。

删除不需要的环境

格式：`conda remove -n 环境名 --all`

示例（删除`my_env`环境）：

```
conda remove -n my_env --all
```

执行后输入`y`确认即可删除（删除后无法恢复，需谨慎）。

克隆现有环境（快速复制环境）

如果想基于已有环境创建副本（比如复制`my_env`为`my_env_copy`）：

```
conda create -n my_env_copy --clone my_env
```

### 新手必看：优化 conda 下载速度（解决安装慢）

默认 conda 源在国外，下载包会很慢，建议配置国内清华镜像源：

```
nano ~/.condarc
```

粘贴以下内容（替换默认源）：

```
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

保存退出（Ctrl+O → Enter → Ctrl+X），之后创建环境 / 安装包会快很多。