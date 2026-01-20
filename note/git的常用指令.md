# git 常用指令

`git clone` 是 Git 最基础的命令之一，作用是**将远程服务器上的 Git 仓库完整复制到本地计算机**，包括仓库的所有文件、提交历史、分支等。简单说就是 “下载仓库到本地”.

#### `--recursive`:这是这条命令的核心参数，含义是**递归克隆**。确保克隆主仓库的同时，完整下载所有嵌套的子模块，避免项目缺少依赖。

- Git 仓库中可能包含 “子模块（submodule）”：也就是主仓库里嵌套了其他的 Git 仓库（比如项目依赖的第三方库、工具代码等）。
- 默认情况下，`git clone` 只会克隆主仓库的内容，**不会下载子模块的实际代码**（只会保留子模块的引用信息）；加上 `--recursive` 后，Git 会自动初始化并克隆所有子模块的内容，确保整个项目（包括依赖的子模块）都完整下载到本地。

补救方案：如果克隆时忘记加 `--recursive`，可以进入克隆后的仓库目录，手动补全子模块：

```
cd gaussian-splatting  # 进入仓库目录
git submodule update --init --recursive  # 初始化并克隆所有子模块
```

如：克隆仓库，仓库包含子模块

```
# SSH
git clone git@github.com:graphdeco-inria/gaussian-splatting.git --recursive
```

