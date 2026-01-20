# 服务器上虚拟机的ubuntu系统如何使用服务器显卡

在服务器的虚拟机中使用显卡（GPU），最常见的技术是 **GPU 直通（GPU Passthrough）**。这可以让虚拟机像在物理机上一样直接访问显卡硬件，从而获得近乎原生的性能。

具体的实现步骤取决于你使用的**宿主机系统（Hypervisor）**，以下是几种主流场景的操作指南：

## 1. 核心前提（必读）

无论哪种方案，都必须先满足以下硬件条件：

- **BIOS 设置**：进入服务器 BIOS，开启 **VT-d** (Intel) 或 **AMD-Vi** (AMD) 以及 **IOMMU**。
- **硬件支持**：主板和 CPU 必须支持 IOMMU。
- **显卡需求**：建议服务器有两张显卡（一张用于宿主机显示，一张用于直通给虚拟机）。如果是单显卡直通，宿主机将失去图形输出。

## 2. 常见服务器环境下的设置

### 场景 A：宿主机是 Proxmox VE (PVE)

这是目前个人和小型企业最常用的方案。

1. **开启 IOMMU**：编辑 `/etc/default/grub`，在 `GRUB_CMDLINE_LINUX_DEFAULT` 中添加 `intel_iommu=on`（Intel）或 `amd_iommu=on`（AMD）。
2. **屏蔽驱动**：在 PVE 中将显卡驱动黑名单化（blacklist），防止宿主机占用显卡。
3. **添加设备**：在 PVE 管理后台，选择虚拟机 -> **硬件** -> **添加** -> **PCI 设备** -> 选择显卡。
4. **勾选选项**：勾选 "All Functions"、"ROM-Bar" 和 "PCI-Express"。

### 场景 B：宿主机是 VMware ESXi

1. **激活直通**：登录 ESXi 后台，点击 **管理** -> **硬件** -> **PCI 设备**。
2. **切换状态**：找到你的 NVIDIA/AMD 显卡，点击“切换直通”，然后重启服务器。
3. **虚拟机设置**：右键虚拟机 -> 编辑设置 -> **添加新设备** -> **PCI 设备**，选择显卡。
4. **预留内存**：在“内存”设置中，必须勾选“预留所有客体内存（全部锁定）”。

### 场景 C：宿主机是 Ubuntu (KVM/QEMU)

如果你是在 Ubuntu 物理机上跑 KVM 虚拟机：

1. 使用 `vfio-pci` 驱动绑定显卡的 PCI ID。
2. 通过 `virt-manager` 界面添加 "PCI Host Device"。

------

## 3. 在虚拟机（Guest Ubuntu）中安装驱动

显卡成功挂载到虚拟机后，你需要在虚拟机的 Ubuntu 系统里执行以下操作：

1. **验证显卡是否识别**：

```
lspci | grep -i nvidia
```

如果能看到显卡型号，说明直通成功。

**安装推荐驱动**：

```
sudo ubuntu-drivers devices  # 查看推荐驱动版本
sudo apt install nvidia-driver-535  # 安装对应版本（以535为例）
```

2.驱动安装后未重启系统

执行重启命令：

```
sudo reboot
```

3.**验证运行状态**： 重启虚拟机后，运行：

```
nvidia-smi
```

sudo apt install nvidia-driver-550  nvidia-smi出现 NVIDIA-SMI 580.95.05              Driver Version: 580.95.05      CUDA Version: 13.0

这个情况是**正常的**，核心原因是：Ubuntu 软件源中`nvidia-driver-550`这个 “软件包名称” 对应的**实际驱动程序版本**是`580.95.05`—— 软件包的命名版本（如`550`）和驱动程序的内部版本号（如`580.95.05`）并非完全一致，这是 NVIDIA 驱动在 Ubuntu 软件源中的常见命名方式（软件包按系列版本命名，实际包含的是该系列下的最新驱动版本）。