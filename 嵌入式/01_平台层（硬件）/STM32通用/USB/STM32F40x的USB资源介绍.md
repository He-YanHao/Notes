# STM32F40x的USB资源介绍

## 基础名词解释

FS：全速

LS：低速

MAC：介质访问控制器

OTG：On-the-go

PFC：数据包FIFO 控制器

PHY：物理层



## 基础数据介绍



在主机模式下，OTG_FS 支持全速（FS，12 Mb/s）和低速（LS，1.5 Mb/s）收发器，而从机模式下则仅支持全速（FS，12 Mb/s）收发器。

在主机模式中，OTG_HS 支持高速（HS，480 Mbits/s）、全速（FS、12 Mbits/s）和低速（LS，1.5 Mbits/s）传输，而在从机模式中，仅支持高速（HS，480 Mbits/s）和全速（FS、12 Mbits/s）传输。



## FIFO

用于临时存储 USB 传输中的数据

### 接收 FIFO（Rx FIFO）

-   **共享式**：所有 OUT 端点共用一个 Rx FIFO。
-   存储从主机接收的数据包及其状态信息（端点号、字节数、PID 等）。
-   大小通过 `OTG_FS_GRXFSIZ` 寄存器配置。





### 发送 FIFO（Tx FIFO）

