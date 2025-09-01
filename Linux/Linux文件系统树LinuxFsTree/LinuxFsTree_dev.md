# 设备文件目录 /dev

## 基础

`/dev` 目录中的`dev`是 **device** 的缩写，用于存放**设备文件（Device Files）**。

`/dev` 目录是用户空间（User Space）的程序与系统硬件、内核模块进行交互的桥梁和接口。

## tree输出

```shell
.
├── autofs
├── block
├── btrfs-control
├── bus
├── char
├── console
├── core -> /proc/kcore
├── cpu
├── cpu_dma_latency
├── cuse
├── dbc
├── disk
├── dma_heap
├── dri
├── drm_dp_aux-[NUM]
├── ecryptfs
├── fb0
├── fd -> /proc/self/fd
├── full
├── fuse
├── gpiochip0
├── hidraw0
├── hidraw1
├── hidraw2
├── hpet
├── hugepages
├── hwrng
├── i2c-[NUM]
├── initctl -> /run/initctl
├── input
├── kfd
├── kmsg
├── kvm
├── log -> /run/systemd/journal/dev-log
├── loop-[NUM]
├── loop-control
├── mapper
├── mcelog
├── media0
├── mem
├── mqueue
├── net
├── ng0n1
├── ng1n1
├── null
├── nvidia0
├── nvidiactl
├── nvidia-modeset
├── nvidia-uvm
├── nvidia-uvm-tools
├── nvme0
├── nvme0n-[NUM]
├── nvme0n1p-[NUM]
├── nvram
├── port
├── ppp
├── psaux
├── ptmx
├── pts
├── random
├── rfkill
├── rtc -> rtc0
├── rtc0
├── shm
├── snapshot
├── snd
├── stderr -> /proc/self/fd/2
├── stdin -> /proc/self/fd/0
├── stdout -> /proc/self/fd/1
├── tpm0
├── tpmrm0
├── tty-[NUM]
├── ttyprintk
├── ttyS-[NUM]
├── udmabuf
├── uhid
├── uinput
├── urandom
├── usb
├── userfaultfd
├── userio
├── v4l
├── vcs-[NUM]
├── vcsa-[NUM]
├── vfio
├── vcsu-[NUM]
├── vga_arbiter
├── vhci
├── vhost-net
├── vhost-vsock
├── video0
├── video1
├── zero
└── zfs
```



## 设备文件分类



### 块设备 Block Device -b

**代表什么**：**支持随机访问的存储设备**。数据以固定大小的“块”为单位进行读写（如 512B, 4KB）。

**特点**：

*   **随机访问**：可以快速跳转到设备的任意位置读取数据（类似用鼠标点击视频的不同时间点）。
*   通常有缓存机制，以提高读写效率。

**常见例子**：

*   硬盘：`/dev/sda`, `/dev/sdb` (SATA/SCSI/USB 硬盘)
*   硬盘分区：`/dev/sda1`, `/dev/sda2`
*   虚拟机硬盘：`/dev/vda`
*   光盘：`/dev/sr0`
*   软盘：`/dev/fd0`
*   内存盘：`/dev/ram0`

### 字符设备 Character Device -c

**代表什么**：**提供连续数据流的设备**，也称为“裸设备”。数据以字符流的形式顺序读写。

**特点**：

- **顺序访问**：数据像水流一样，必须按顺序读取，不能随意跳转（类似听磁带，要快进到某首歌必须经过前面的歌）。

- 通常没有缓存，直接与设备交互。

**常见例子**：

*   终端：`/dev/tty1`, `/dev/pts/0` (你正在使用的命令行界面就是字符设备)
*   串口：`/dev/ttyS0`, `/dev/ttyUSB0`
*   键盘：`/dev/input/event0` (向系统输入字符流)
*   鼠标：`/dev/input/mice`
*   打印机：`/dev/lp0`
*   空设备：`/dev/null` (写入它的所有数据都会消失)
*   零设备：`/dev/zero` (提供无限的空字符 `\0`)



### 管道设备 FIFO -p

**代表什么**：**进程间通信 (IPC) 的通道**，也称为“命名管道”。它不是一个硬件设备，而是一个软件机制。

**特点**：

*   数据是**单向流动**的。一端写入，另一端读取。
*   **必须先有读者，再有写者**。如果写者向没有读者的管道写入数据，它会被阻塞。
*   数据一旦被读取，就会从管道中消失。

**作用**：允许两个不相关的进程（比如一个 shell 脚本和一个 C 程序）进行数据交换。



### 套接字设备

**代表什么**：**网络进程间通信的端点**。它也是一种特殊的文件类型，用于在不同机器或同一机器上的进程之间进行双向网络通信。

**特点**：

*   支持**双向通信**（全双工）。
*   可以用于**网络通信**（如通过 TCP/IP）或**本地进程间通信**。

**常见例子**：

*   `/dev/log`：系统日志守护进程 `syslogd` 或 `rsyslogd` 创建的套接字。其他程序通过向这个套接字写入消息来记录日志。
*   X Window 系统用于 GUI 显示的套接字。
*   数据库（如 MySQL）可能会使用套接字进行本地连接。



### 总结与对比

| 类型           | 标识符 | 核心功能             | 访问方式           | 典型例子                            |
| :------------- | :----- | :------------------- | :----------------- | :---------------------------------- |
| **块设备**     | `b`    | 数据存储             | 随机访问（按块）   | 硬盘 (`/dev/sda`), SSD              |
| **字符设备**   | `c`    | 数据流传输           | 顺序访问（按字符） | 终端 (`/dev/tty`), 键盘, `null`     |
| **管道设备**   | `p`    | **进程间**通信 (IPC) | 单向流，FIFO       | 命名管道 (由 `mkfifo` 命令创建)     |
| **套接字设备** | `s`    | **网络/进程间**通信  | 双向流             | 日志套接字 (`/dev/log`), 数据库连接 |



## 文件分类

**硬件映射文件**

| 设备文件               | 类型                      | 说明                                                         |
| ---------------------- | ------------------------- | ------------------------------------------------------------ |
| **`/dev/nvme0n1`**     | NVMe固态硬盘              | `nvme0n1` 是第一块，`nvme1n1` 是第二块。                     |
| **`/dev/nvme0n1p1`**   | NVMe固态硬盘的分区        | `nvme0n1p1`为第一分区(并非从零开始)。                        |
| **`/dev/sda`**         | 块设备                    | 系统识别到的第一块 SATA/SCSI/USB 硬盘。                      |
| **`/dev/sda1`**        | 块设备                    | 第一块 SATA/SCSI/USB 硬盘上的第一个主分区。                  |
| **`tty0` - `tty63`**   | 虚拟控制台                | 可以通过 `Ctrl+Alt+F1` 到 `Ctrl+Alt+F63` 切换它们。`tty0` 代表当前活动的控制台。 |
| **`loop0` - `loop24`** | 回环设备                  | 用于将文件（如 ISO 镜像、Snap 包、Flatpak 应用）挂载为虚拟块设备。这么多 `loop` 设备表明你系统里可能安装了不少 Snap 或 Flatpak 应用。 |
| **`ttyS0` - `ttyS31`** | 串行端口 例如如 COM1 COM2 | 通常用于连接旧式调制解调器、串口控制台或某些硬件。           |
| **`i2c-0` - `i2c-27`** | I²C 总线接口              | 用于连接各种低速外围设备（如传感器、EEPROM）。               |
|                        |                           |                                                              |

**功能测试文件**

| 设备文件          | 类型     | 说明                                                         |
| ----------------- | -------- | ------------------------------------------------------------ |
| **`/dev/null`**   | 字符设备 | 数据黑洞，丢弃一切写入的数据。                               |
| **`/dev/full`**   | 满设备   | 写入它总会失败并返回磁盘已满的错误，用于测试程序处理错误的能力。 |
| **`/dev/zero`**   | 字符设备 | 提供无限的空字符（`\0`）。                                   |
| **`/dev/random`** | 字符设备 | 真随机数发生器（会阻塞，等待熵池积累）。                     |
|                   |          |                                                              |
|                   |          |                                                              |
|                   |          |                                                              |
|                   |          |                                                              |
|                   |          |                                                              |



## 储存设备接口文件

### NVMe固态硬盘 nvme0

第一块NVMe固态硬盘会构造出`nvme0n1`文件，之后每个同类设备会使后面数字递增1。

每块NVMe固态硬盘的分区也会被构造出文件，比如`nvme0n1p1`表示这块固态硬盘的第一个分区，之后每个分区会使后面数字递增1。

### CD和DVD sr

光学存储设备



### SATA

机械硬盘



## 终端 tty pts

`tty`早期是物理接口，后期演变为软件。

`pts`纯软件模拟，成对出现 (主/从)。

### 串行总线接口ttyS

在Windows上显示为COM1的接口在Linux里显示为ttyS1。



### 并行总线接口

在Windows上显示为LPT1的接口在Linux里显示为lpS1。



## vcsu



## drm_dp_aux

用于调试DP显示器。
