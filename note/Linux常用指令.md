# Linux常用指令

## `wget`

### `wget`的核心特点（新手必知）

1. **多协议支持**：能下载 HTTP/HTTPS（网页 / 文件链接）、FTP（文件服务器）协议的资源；
2. **断点续传**：下载大文件时如果中断（比如网络断了），重新执行命令能从断点继续下载，不用从头再来；
3. **后台下载**：可以让下载任务在后台运行，不占用当前终端窗口；
4. **递归下载**：能批量下载整个网站 / 目录下的所有文件；
5. **限速下载**：可限制下载速度，避免占用全部带宽影响其他操作。

### 新手常用的`wget`用法（附示例）

#### 1. 基础用法：下载单个文件到当前目录

bash

```
# 格式：wget + 资源链接
wget https://example.com/test.zip
```

执行后，文件会直接下载到你当前的终端工作目录下，终端会显示下载进度、速度、剩余时间。

#### 2. 断点续传（最实用！）

下载大文件中断后，加`-c`参数继续：

bash

```
wget -c https://example.com/large_file.iso
```

#### 3. 指定保存文件名

默认文件名是链接里的文件名，用`-O`（大写 O）可自定义：

bash

```
# 把下载的文件命名为my_file.zip
wget -O my_file.zip https://example.com/test.zip
```

#### 4. 后台下载

加`-b`参数，下载任务会转到后台，终端可继续执行其他命令，下载日志会保存到`wget-log`文件：

bash

```
wget -b https://example.com/big_file.tar.gz
```

#### 5. 限速下载

比如限制下载速度为 100KB/s（避免占满带宽）：

bash

```
wget --limit-rate=100k https://example.com/large_file.iso
```

#### 6. 跳过 HTTPS 证书验证（仅测试用）

如果下载 HTTPS 链接时提示证书错误，可临时跳过（生产环境不建议）：

bash

```
wget --no-check-certificate https://example.com/secure_file.zip
```

### 总结

1. `wget`是 Linux 终端的命令行下载工具，支持 HTTP/HTTPS/FTP 等协议，无需图形界面；
2. 核心优势是**断点续传**和**后台下载**，特别适合服务器 / 远程终端场景；
3. 新手必记参数：`-c`（断点续传）、`-O`（重命名）、`-b`（后台下载）、`--limit-rate`（限速）。



bash

bash 是什么？

bash（全称 Bourne-Again Shell）是**Linux/Unix 系统默认的命令行解释器（Shell）**，可以把它理解成 “终端的大脑”—— 你在终端输入的所有命令（比如`ls`、`sudo`、`wget`），都是由 bash 接收、解析，再转交给操作系统内核去执行的 “中间人”。

### bash 的核心作用

1. **解释执行终端命令**：你输入的`cd /etc`（切换目录）、`sudo apt install wget`（安装软件）等命令，都靠 bash 翻译成系统能理解的指令；
2. **运行 Shell 脚本**：`.sh`后缀的 bash 脚本（比如自动化部署、批量操作的脚本），本质是一系列 bash 命令的集合，bash 能逐行执行这些脚本；
3. **自定义终端环境**：bash 有专属配置文件（如`~/.bashrc`、`~/.bash_profile`），可以设置终端提示符样式、命令别名（比如让`ll`等价于`ls -l`）、环境变量（如`PATH`）；
4. **增强命令行体验**：提供 Tab 补全、命令历史记录、快捷键操作等，大幅提升终端操作效率。

### 三、新手必知的 bash 基础操作

#### 1. 验证当前 Shell 是否为 bash

登录终端后，执行以下命令可确认：

bash

```
echo $SHELL
# 输出 /bin/bash 表示当前是bash环境（Ubuntu默认就是）
```