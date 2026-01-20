### 激活 `conda`（Anaconda/Miniconda）创建的虚拟环境

conda 提供了统一的激活命令，无需手动找路径，操作更简洁。

#### 1. 基础激活步骤

运行

```bash
# 步骤1：若终端提示 conda 命令不存在，先初始化 conda（仅首次需要）
source ~/miniconda3/bin/activate  # Miniconda 路径
# 或 source ~/anaconda3/bin/activate  # Anaconda 路径

# 步骤2：激活指定环境（格式：conda activate 环境名）
conda activate tensorflow_env
```

- 替换说明：把 `tensorflow_env` 换成你的 conda 环境名（可通过 `conda env list` 查看）；
- 执行成功后，终端提示符会显示：`(tensorflow_env) your_username@ubuntu:~$`。

#### 验证激活成功（可选）

查看 conda 当前激活的环境：

```
conda info --envs
# 输出中带 * 号的就是当前激活的环境，示例：
# tensorflow_env  *  /home/your_username/miniconda3/envs/tensorflow_env
```

### 三、退出虚拟环境（通用操作）

无论哪种工具创建的环境，退出命令都很简单：

```
# venv/virtualenv / conda 通用退出命令
deactivate
```

执行后终端提示符的环境名会消失，回到系统默认的 Python 环境。

### 总结

1. **venv/virtualenv 激活**：核心是 `source 环境目录/bin/activate`，需先找到环境存储路径；
2. **conda 激活**：先初始化（首次），再执行 `conda activate 环境名`，无需找路径；
3. **验证 / 退出**：激活成功后终端会显示环境名，退出均用 `deactivate` 命令。