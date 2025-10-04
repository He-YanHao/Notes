# Arch

## 在内存

### 确保连接网络

在Live环境中，需要连接互联网以下载软件包。

使用 iwctl 命令连接无线网络：

```shell
iwctl
```

在 iwd 提示符下，依次执行：

```shell
device list                           # 列出无线设备
station <设备名> scan                  # 扫描网络
station <设备名> get-networks          # 列出扫描到的网络
station <设备名> connect <Wi-Fi名称>    # 连接网络，输入密码
exit                                  # 退出iwctl
```

测试网络连接：使用 ping 命令测试网络是否通畅：

```shell
ping archlinux.org -c 5
```

### 时间

同步时间，有些软件对时间要求严格。

```bash
timedatectl set-ntp true
```



### 磁盘分区格式化与挂载

使用下面命令查看磁盘大小及名称：

```bash
fdisk -l
lsblk -pf
```

使用 `parted` 进行分区：

```bash
parted </dev/nvme0n1><替换为实际磁盘>
(parted) print free  # 查看未分配空间
(parted) unit MiB    # 改变单位 该操作可选
(parted) mklabel gpt # 创建gpt分区
(parted) mkpart primary <开始位置> <结束位置>
(parted) quit
```

一般先分配一个至少 `256MB` ~ `1GB` 的EFI系统分区。

格式化分区

```bash
mkfs.fat -F 32 </dev/nvme0n1p1><替换为实际磁盘>
mkfs.xfs -L <设置标签名> </dev/nvme0n1p2><替换为实际磁盘>
```

挂载分区

```bash
# 先挂载根分区
mount </dev/nvme0n1p2><替换为实际磁盘> /mnt/
# 再EFI系统分区
mount </dev/nvme0n1p1><替换为实际磁盘> /mnt/boot/EFI
# 最后是其他分区
```

挂载未创建目录需要先创建

```bash
makdir /mnt/boot/EFI
makdir /mnt/home/
```

```bash
mount </dev/nvme0n1p2><替换为实际磁盘> /mnt/home/
```



### 安装基本包

Arch默认的下载地址是根据最近全球下载数量的前20决定的，使用下面的命令生成仅针对中国下载数量排序的顺序：

```bash
reflector --country China --sort rate --save /etc/pacman.d/mirrorlist
```

然后下载基本包：

```bash
pacstrap -K /mnt base base-devel linux linux-firmware
```



### 配置系统

要在启动时安装所需的文件系统（例如用于引导目录的文件系统），请生成一个 fstab 文件。使用 或 分别通过 UUID 或标签进行定义：`/boot -U -L`

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

可以检查一下有无问题。



### 进入系统

```
arch-chroot /mnt
```

就可以开始再次配置系统了。



## 配置启动

### 引导（必须）

下载必须的软件包

```bash
pacman -S grub efibootmgr
```

**安装 GRUB 到 EFI 分区**：

```bash
grub-install --efi-directory=<EFI系统分区挂载目录> --bootloader-id=<UEFI启动菜单名称>
```

**生成 GRUB 配置文件**：

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```



### 时间（可选）

设置时区

```
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```

运行 `hwclock ` 以生成：`/etc/adjtime`

```
hwclock --systohc
```



### 地方化（可选）

要使用正确的区域和语言特定格式（如日期、货币、小数分隔符），需要编辑将使用的 UTF-8 区域设置并取消注释。通过运行以下命令生成区域设置：`/etc/locale.gen`

```
locale-gen
```

创建 `locale.conf` 文件，并相应地设置 LANG 变量：

```
/etc/locale.conf
LANG=en_US.UTF-8
```



### 创建主机名文件（可选）

```
echo "主机名称" > /etc/hostname
```



















配置完毕之后使用

```bash
exit
```

退出 `arch-chroot` 环境，关机，拔出安装介质。





