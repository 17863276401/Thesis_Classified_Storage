# Git LFS如何配置

鉴于你正在研究 **3DGS (3D Gaussian Splatting)**，你未来一定会遇到巨大的文件（例如 `.ply` 点云文件、`.pth` 模型权重文件、或高分辨率 `.mp4` 渲染视频）。这些文件通常都会超过 100MB。

Git LFS (Large File Storage) 就是用来解决这个问题的：它把大文件的“指针”存在 Git 里，而把实际的大文件存在另外的服务器上。

以下是配置 Git LFS 的详细步骤（**Windows Git Bash 环境**）：

### 第一步：检查是否已安装 LFS

现在的 Git for Windows 通常自带 LFS。在终端输入：

Bash

```
git lfs version
```

- 如果显示版本号（如 `git-lfs/3.x.x`），说明已安装，直接跳到第二步。
- 如果报错说找不到命令，你需要先去 [git-lfs.com](https://git-lfs.com/) 下载并安装。

### 第二步：在你的仓库中初始化 LFS

进入你的项目文件夹（如果你还没在里面），执行：

Bash

```
git lfs install
```

看到 `Git LFS initialized.` 即表示成功。这步只需要做一次。

### 第三步：告诉 LFS 要追踪哪些类型的文件

**这是最关键的一步。** 你需要指定哪些后缀的文件交给 LFS 管理。

针对你的 3DGS 项目，我建议你执行以下命令（根据你实际有的文件类型）：

Bash

```
# 追踪模型权重文件 (通常很大)
git lfs track "*.pth"
git lfs track "*.ckpt"

# 追踪 3DGS 的点云/Splat 文件 (通常几百MB到几GB)
git lfs track "*.ply"

# 如果你有大的视频演示
git lfs track "*.mp4"

# 如果你想把刚才报警的大 PDF 也以后续交给 LFS (可选)
git lfs track "*.pdf"
```

### 第四步：保存配置

执行完上面的命令后，你会发现文件夹里多了一个名为 `.gitattributes` 的文件（或者它被修改了）。这个文件记录了 LFS 的规则，**必须**把它提交到 GitHub。

Bash

```
git add .gitattributes
git commit -m "配置 LFS 以追踪大文件"
```

### 第五步：像往常一样操作

配置完成后，以后你再添加大的 `.ply` 或 `.pth` 文件时，操作和以前**完全一样**，不需要记新命令：

Bash

```
git add .
git commit -m "添加训练好的模型"
git push origin main
```

*(Git 会自动在后台帮你把大文件通过 LFS 通道上传，而不会报错了)*

------

### ⚠️ 两个非常重要的注意事项

#### 1. 关于 GitHub 的免费额度

GitHub 对 LFS 的免费账户有限制：

- **存储空间**：1 GB
- **带宽**：1 GB/月
- **这意味着**：如果你上传了一个 2GB 的 `.ply` 文件，GitHub 会提示空间不足（或者如果不小心传上去了，别人下载一次就会耗尽你的带宽）。
- **建议**：对于超大的 3DGS 数据集或模型（几个GB那种），建议传到 **Hugging Face**（专门存模型的，空间大）或者 **百度网盘/Google Drive**，然后在 README 里放下载链接，而不要强行塞进 GitHub。

#### 2. 关于“已经存在”的大文件

如果你有一个文件（比如 `model.pth`）**已经**被你用普通方式 commit 过了（即使还没 push），然后再运行 `git lfs track "*.pth"`，LFS **不会**自动把旧的历史记录转换过来。

- **对于新文件**：配置好后直接生效。
- **对于历史文件**：如果之前误传了大文件导致 push 失败，需要用复杂的命令清洗历史。**你目前是新仓库，只要现在配置好，以后添加新模型就不会有问题。**