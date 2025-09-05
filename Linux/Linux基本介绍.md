# Linux基本介绍

## 基本介绍

Linux 本质上是一个类 Unix 的操作系统内核（Kernel），由 Linus Torvalds 首创。我们通常所说的 “Linux操作系统” 更准确的称呼是 “GNU/Linux 发行版”，因为它包含了 Linux 内核和庞大的 GNU 用户空间工具集。

## **核心架构与机制**

1.  **宏内核  （monolithic Kernel）：** 与微内核相对，Linux 采用宏内核设计。其核心服务（如进程调度、内存管理、文件系统、设备驱动、网络协议栈）均运行在特权级别最高的内核空间。这种设计带来了极高的性能和直接的硬件访问能力，但也在复杂性和稳定性上提出了更高要求。
2.  **虚拟文件系统（VFS - Virtual File System）：** VFS 是内核中的一个抽象层，它为上层应用程序提供了统一的文件操作接口（如 `open`, `read`, `write`, `close`），从而屏蔽了下层具体文件系统（如 ext4, XFS, Btrfs, NFS）的实现差异。这是 Linux “一切皆文件” 哲学的技术基石。
3.  **进程管理（Process Management）与调度器（Scheduler）：**
	*   **进程与线程：**  Linux 使用轻量级进程（LWP）模型来实现线程，线程与进程在内核中都被称为任务（Task），通过 `clone()` 系统调用并共享不同的资源来区分。
	*   **Completely Fair Scheduler）：** 现代 Linux 默认的进程调度器。其核心是“完全公平”算法，通过红黑树数据结构跟踪进程的虚拟运行时间（vruntime），确保所有可运行进程公平地分享 CPU 时间片，同时对交互式进程提供良好的响应性。
4.  **内存管理（Memory Management）：**
	*   **虚拟内存与分页：**  Linux 为每个进程提供独立的虚拟地址空间，通过分页机制映射到物理内存。内核负责管理页表（Page Table）。
	*   **Buddy System）：** 负责管理物理内存页的分配与回收，解决外部碎片问题。
	*   **Slab/Slub Allocator）：** 建立在伙伴系统之上，用于高效分配内核中常见的小数据结构对象（如 task_struct, inode），解决内部碎片问题。
5.  **设备驱动与 `/dev`：** 硬件设备在 Linux 中同样被抽象为文件。设备驱动程序是内核模块，它们为特定硬件提供接口。这些接口通过设备文件（如 `/dev/sda`, `/dev/random`）暴露给用户空间。设备分为：
	*   **块设备（Block Devices）：** 支持随机访问，数据读写以块为单位（如硬盘）。
	*   **字符设备（Character Devices）：** 以字节流形式顺序访问（如键盘、鼠标）。
	*   **网络设备（Network Devices）：** 不映射到 `/dev`，而是通过套接字接口访问。

## **系统与服务管理**

1.  **Init 系统演进：**
    *   **SysVinit：** 传统的基于运行级别（Runlevel）的初始化系统，启动过程线性且缓慢。
    *   **Upstart：** 事件驱动的 init 系统，改善了启动速度和依赖管理。
    *   **systemd：** **现代主流发行版的默认 init 系统**。它不仅仅是一个 init 程序，更是一个庞大的系统和服务管理器套件。其核心特性包括：
        *   并行启动服务，极大提升启动速度。
        *   基于单元文件（Unit Files，如 `.service`, `.socket`, `.mount`）声明式地管理服务、挂载点、套接字等。
        *   提供强大的日志系统 `journald`，支持结构化日志记录。
        *   集成管理用户会话、网络、定时任务（替代 `cron`）等诸多功能。

2.  **服务管理：** 在 `systemd` 环境下，使用 `systemctl` 命令对服务进行精细化管理（`start|stop|restart|enable|disable|status`），并使用 `journalctl` 进行日志查询和过滤。

## **存储与文件系统**

1.  **高级文件系统：**
    *   **ext4 (Fourth Extended Filesystem)：** 最广泛使用的稳定文件系统，是 ext3 的扩展，支持大文件和日志功能。
    *   **XFS (XFS Filesystem)：** 高性能的 64 位日志文件系统，特别擅长处理大文件和高并发 I/O，常用于企业级存储和数据库。
    *   **Btrfs (B-Tree Filesystem)：** 具有高级特性的现代写时复制（CoW）文件系统。支持快照（Snapshot）、子卷（Subvolume）、透明压缩、数据校验和 RAID 功能，是下一代 Linux 文件系统的有力竞争者。
    *   **ZFS (OpenZFS)：** 虽非 Linux 原生，但通过第三方端口广泛使用。提供无与伦比的数据完整性保证（端到端校验和）、极高的可扩展性以及强大的存储池（Pool）和卷管理功能。

2.  **逻辑卷管理（LVM - Logical Volume Manager）：** LVM 在物理磁盘和文件系统之间增加了一个抽象层，实现了**动态磁盘管理**。
    *   **物理卷（PV）：** 实际的硬盘或分区。
    *   **卷组（VG）：** 由一个或多个 PV 组成的存储池。
    *   **逻辑卷（LV）：** 从 VG 中划分出的逻辑块设备，其上可创建文件系统。
    *   **优势：** 支持在线动态调整 LV 大小、快照、条带化、镜像等，极大增强了存储的灵活性。

3.  **RAID（Redundant Array of Independent Disks）：** 通过磁盘阵列提供性能提升或数据冗余。Linux 内核的 `md`（Multiple Devices）驱动支持软件 RAID 级别（如 RAID 0, 1, 5, 6, 10）。

## **网络与安全**

1.  **网络栈与工具：**
    *   **TCP/IP 协议栈：** Linux 拥有一个高效且可高度调优的网络协议栈实现。
    *   **防火墙：** `netfilter/iptables` 是传统的包过滤框架。现代替代方案是 `nftables`，它提供了更简洁的语法和更好的性能。
    *   **网络命名空间（Network Namespace）：** 容器技术的网络隔离基础，允许每个容器拥有自己独立的网络设备、IP 地址、路由表和防火墙规则。
    *   **高级工具：** `ss`（替代 `netstat`）、`ip`（替代 `ifconfig` 和 `route`）、`tcpdump`、` traceroute` 等用于高级网络诊断和配置。

2.  **Linux 安全模块（LSM - Linux Security Modules）：**
    *   **SELinux (Security-Enhanced Linux)：** 由 NSA 开发，提供强制访问控制（MAC）。基于安全策略（Policy）为每个进程和文件对象赋予上下文标签，并严格定义其访问权限，遵循“默认拒绝”原则。
    *   **AppArmor：** 另一种主流的 MAC 系统，基于路径的配置文件（Profile）来限制程序的能力，通常被认为比 SELinux 更易于配置和管理。

3.  **认证与授权：**
    *   **PAM (Pluggable Authentication Modules)：** 一套可插拔的认证框架，允许管理员灵活配置应用程序的认证方式（如密码、双因子、生物识别），而无需修改应用程序本身。
    *   **sudo：** 精细化的权限委托工具，允许普通用户以其他用户（通常是 root）的身份执行命令，并通过 `/etc/sudoers` 文件进行细粒度控制。

## **性能分析与调优**

1.  **观测性工具（Observability Tools）：**
    *   **`perf`：** 强大的性能分析工具，可用于进行 CPU 性能计数器采样、跟踪点分析、火焰图生成等。
    *   **`ftrace`：** 内核内置的跟踪器，用于深入分析内核行为和性能瓶颈。
    *   **`eBPF (Extended Berkeley Packet Filter)`：** **革命性的内核技术**。允许用户在不修改内核源码、不加载内核模块的情况下，安全高效地在内核中运行沙盒程序，用于实现前所未有的高级性能分析、网络监控和安全功能。`BCC` 和 `bpftrace` 是基于 eBPF 的上层工具。
    *   **传统工具：** `top/htop`, `vmstat`, `iostat`, `netstat`, `sar`（系统活动报告器）等。

2.  **调优参数：** 通过 `/proc` 和 `/sys` 虚拟文件系统可以动态调整大量内核参数，如 TCP 缓冲区大小、文件描述符限制、虚拟内存行为等。

## **容器化与自动化**

1.  **容器核心技术：**
    *   **Namespaces：** 提供隔离（PID、网络、挂载、用户等）。
    *   **Cgroups (Control Groups)：** 提供资源限制和核算（CPU、内存、磁盘 I/O、网络等）。
    *   **Union Filesystems：** 提供分层镜像构建（如 OverlayFS）。
    *   **容器运行时（runc, containerd）：** 利用上述技术创建和运行容器。

2.  **自动化运维：**
    *   **Shell 脚本：** Bash 是系统管理和自动化的基石。
    *   **配置管理工具：** **Ansible**（无代理，基于 YAML）、**Puppet**、**Chef**、**SaltStack** 等，用于实现基础设施即代码（IaC），自动化部署和配置大批量服务器。
    *   **CI/CD (Continuous Integration/Continuous Deployment)：** 与 Git、Jenkins、GitLab CI/CD 等工具集成，实现自动化构建、测试和部署。
