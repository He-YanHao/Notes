# GRUB

## 基础介绍

**GRUB**（**G**rand **U**nified **B**ootloader）是一个来自 GNU 项目的**启动引导程序**。它的主要职责是计算机启动过程中，在操作系统内核运行之前运行的第一段软件。

硬件（BIOS 或 UEFI）会先完成自检，然后它就把控制权交给 GRUB。GRUB 的任务是：
1.  **呈现一个菜单**：列出所有可用的操作系统（如不同的 Linux 发行版、Windows 等）。
2.  **加载内核**：根据用户的选择（或默认选项），找到对应操作系统的内核文件（如 `vmlinuz-linux`），并将其加载到内存中。
3.  **移交控制权**：将系统的控制权交给内核，从而启动完整的操作系统。

如果没有 GRUB（或类似的引导程序），计算机就无法知道从哪里去加载和启动你的操作系统。



## GRUB 的主要版本

GRUB 主要有两个版本：

1.  **GRUB Legacy（传统 GRUB）**：
    *   这是最初的版本，功能相对基础。
    *   其配置文件是 `/boot/grub/menu.lst` 或 `/boot/grub/grub.conf`。
    *   现在绝大多数主流发行版都已不再使用此版本。

2.  **GRUB 2**：
    *   这是目前**几乎所有现代 Linux 发行版（如 Ubuntu, Fedora, Debian, Arch Linux 等）默认使用的版本**。
    *   它是完全重写的版本，比 Legacy 版本更强大、更灵活。
    *   当我们今天说“GRUB”时，通常指的就是 **GRUB 2**。
    *   其配置文件主要是 `/boot/grub/grub.cfg`。

*注意：下文的所有介绍都基于 **GRUB 2**。*



## GRUB2的核心功能与特点

1.  **支持多系统启动**：
    *   这是它最广为人知的功能。它可以轻松引导 Linux、Windows、BSD 等多种操作系统，是实现双系统不可或缺的组件。

2.  **支持多种文件系统**：
    *   GRUB 2 可以直接从多种常见的文件系统（如 ext4, NTFS, FAT, Btrfs, XFS）中读取内核和配置文件，而不依赖于特定的文件系统驱动。

3.  **基于模块化设计**：
    *   功能以动态模块的形式加载，这使得 GRUB 2 的核心映像非常小，可以轻松嵌入到 BIOS 引导区或 EFI 系统分区中。

4.  **提供图形化界面和主题支持**：
    *   除了传统的文本菜单，GRUB 2 还支持图形化启动菜单，用户可以自定义背景图片、字体、颜色等。

5.  **强大的交互性**：
    *   在启动时，可以按下 `c` 键进入 GRUB 命令行，或者按 `e` 键编辑当前启动项的配置参数（例如，进入单用户模式修复系统）。

6.  **支持 BIOS 和 UEFI**：
    *   GRUB 2 完美兼容传统的 BIOS 主板和现代的 UEFI 主板。



## GRUB2的配置文件

GRUB 2 的配置机制是其强大之处，但也略有复杂：

*   **主配置文件**：`/boot/grub/grub.cfg`
    *   **⚠️ 重要警告**：**不要直接手动编辑这个文件！** 这个文件是由脚本自动生成的，你的修改会在下次更新时被覆盖。

*   **自定义配置文件**：`/etc/default/grub`
    *   这个文件用于设置全局变量，如默认启动项、超时时间、分辨率等。
    *   修改此文件后，需要运行 `sudo update-grub`（在 Debian/Ubuntu 等系统上）或 `sudo grub2-mkconfig -o /boot/grub/grub.cfg`（在 RHEL/Fedora 等系统上）来使更改生效。

*   **脚本目录**：`/etc/grub.d/`
    *   这个目录包含一系列脚本（`00_header`, `10_linux`, `30_os-prober` 等），`grub-mkconfig` 命令会执行这些脚本来生成最终的 `grub.cfg` 文件。
    *   例如，`10_linux` 脚本会查找本地的 Linux 内核，`30_os-prober` 会扫描硬盘上的其他操作系统（如 Windows）。
    *   如果你想完全自定义启动项，可以在这里创建或修改脚本。

**工作流程总结**：修改 `/etc/default/grub` 或 `/etc/grub.d/` 中的文件 -> 运行 `sudo update-grub` -> 生成新的 `/boot/grub/grub.cfg`。



## 常见的 GRUB 操作与命令

1.  **更新 GRUB**（在安装新内核或发现新系统后）：
    ```bash
    # Debian/Ubuntu 及其衍生版
    sudo update-grub
    
    # RHEL/Fedora/CentOS/Arch 等大多数其他发行版
    sudo grub2-mkconfig -o /boot/grub/grub.cfg
    # 或者（某些系统链接了 grub 到 grub2）
    sudo grub-mkconfig -o /boot/grub/grub.cfg
    ```

2.  **重新安装 GRUB**（当 GRUB 损坏或需要更换引导方式时）：
    *   假设你的根分区是 `/dev/sda`：
    ```bash
    # 对于 BIOS 系统
    sudo grub-install /dev/sda
    
    # 对于 UEFI 系统（需要先挂载 EFI 系统分区，例如到 /boot/efi）
    sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
    ```

3.  **启动时编辑菜单项**：
    *   在 GRUB 菜单出现时，选中一个条目并按 `e` 键，可以临时编辑本次启动的内核参数。例如，在参数末尾添加 `single` 或 `systemd.unit=rescue.target` 可以进入救援模式。按 `Ctrl+X` 或 `F10` 使用修改后的配置启动。



## 当GRUB出现问题（例如无法启动）时怎么办？

GRUB 问题通常表现为启动后直接进入 `grub>`  Rescue 命令行模式，或者找不到启动项。常见解决方法：

1.  **使用 Live USB**：用一个 Linux 安装 U 盘启动电脑。
2.  **挂载分区**：在 Live 环境中，挂载你的原系统根分区和 Boot 分区（如果有的话）。
3.  **重新安装 GRUB**：使用 `chroot` 命令切换到原系统环境，然后执行 `grub-install` 和 `update-grub`。

这是一个非常常见的系统修复操作，具体命令步骤可以在网上搜索“如何使用 Live CD 修复 GRUB”。

