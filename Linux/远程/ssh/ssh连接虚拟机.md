# ssh连接虚拟机

## 目标

配置：

-   桥接模式网卡上网
-   仅主机模式网卡进行ssh连接
-   桥接模式连接开发板

## 准备工作

在 编辑 -> 虚拟网络编辑器 配置 VMnet0(桥接模式) VMnet1(仅主机模式) VMnet8(桥接模式)

配置VMnet1(仅主机模式)的网段和子网掩码，并记录下来。

添加这三张网卡。

进入设置

分别配置这三张网卡的IP地址，要求不在同一个网段，并且VMnet1(仅主机模式)和设置中的一致。



## 上网网卡

添加 `VMnet0` 网卡，使用 `ip a` ，输出如下：

```bash

```

`ens33` 为 Ubuntu 为 VMnet0 分配的？？，测试，可以上网。

## ssh连接网卡

### ssh远程连接必备前置条件

需要 `openssh-server` 组件，检查是否安装。

```shell
sudo apt install openssh-server
```

检查是否启动

```shell
sudo systemctl status ssh
```

如果没有启动，使用：

```shell
sudo systemctl enable --now ssh
```

启动，并设置为开机自启动。

### 配置网卡

添加 `VMnet1` 

使用：

```bash
nmcli connection show
```

列出当前的网络配置。

```bash
he@he-books:/etc/netplan$ nmcli connection show
NAME           UUID                                  TYPE      DEVICE 
netplan-ens37  c5c3b18d-e2d7-3b94-9c87-573e6c0c29bc  ethernet  ens37  
lo             4d90d657-3a2b-4a7b-8831-2deaa5711254  loopback  lo    
```

获得 `ens37` 的名称。

使用这个名称自动生成配置文件，避免冲突。

```bash
sudo nmcli con mod "netplan-ens37" ipv4.method manual
sudo nmcli con mod "netplan-ens37" ipv4.addresses 192.168.246.127/24
sudo nmcli con mod "netplan-ens37" ipv4.gateway 192.168.246.1
sudo nmcli con mod "netplan-ens37" ipv4.dns "8.8.8.8 8.8.4.4"
```

需要注意：

存在两种相互冲突的配置方式，分别是老式的 `networkd` 和新式的 `NetworkManager` 

配置文件都在

```
/etc/netplan/
```

目录下

```bash
01-network-manager-all.yaml
```

是 `networkd` 使用的手动配置文件

配置 `NetworkManager` 后会出现

```bash
90-NM-c5c3b18d-e2d7-3b94-9c87-573e6c0c29bc.yaml
```

需要把旧式的配置文件删除掉。



