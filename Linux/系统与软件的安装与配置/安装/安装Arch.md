# Arch

## 在IOS

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



### 更新密钥

```bash
pacman -Sy archlinu-keyring
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

>   -K：意思是复制密钥

下载重要工具：

```bash
pacstrap /mnt vim sudo amd-ucode iwd
```



### 配置启动文件系统

要在启动时安装所需的文件系统（例如用于引导目录的文件系统），请生成一个 fstab 文件。

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

>   -U：代表使用UUID

可以检查一下有无问题。



### 进入系统

```
arch-chroot /mnt
```

就可以开始再次配置系统了。



## 配置系统

### 设置时区

```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

运行 `hwclock ` 以生成：`/etc/adjtime`

```
hwclock --systohc
```



### 地方化

要使用正确的区域和语言特定格式（如日期、货币、小数分隔符），需要编辑将使用的 UTF-8 区域设置并取消注释。

通过运行以下命令生成区域设置：``

```bash
vim /etc/locale.gen
```

取消掉

```bash
# en_US.UTF-8
# zh_CN.UTF-8
```

的注释。

使用

```bash
locale-gen
```

创建 `locale.conf` 文件，并编辑，添上下面一行设置 LANG 变量：

```
LANG=en_US.UTF-8
```



### 创建主机名文件

```
echo "主机名称" > /etc/hostname
```



### 密码

需要使用 `passwd` 命令设置root密码。





### 引导

下载必须的软件包

```bash
pacman -S grub efibootmgr os-prober
```

**安装 GRUB 到 EFI 分区**：

```bash
grub-install --target=x86_64-efi --efi-directory=<EFI系统分区挂载目录> --bootloader-id=<UEFI启动菜单名称>
```



**修改GRUB配置材料**

使用

```bash
vim /etc/default/grub

# 更改
loglevel=3 xxxxx
# 删除注释
最后一行
```



**生成 GRUB 配置文件**：

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```



### 退出

配置完毕之后使用

```bash
exit
```

退出 `arch-chroot` 环境，关机，拔出安装介质。



## 安装桌面


### 创建普通用户



### 驱动

```bash
pacman -S nvidia
```

重启


### 包


```bash
sudo pacman -S plasma-meta
sudo pacman -S plasma-mobile
sudo pacman -S qt5-wayland
sudo pacman -S kde-applications-meta
sudo pacman -S kde-applications
```


要从控制台启动 Plasma on Wayland 会话，请运行 /usr/lib/plasma-dbus-run-session-if-needed /usr/bin/startplasma-wayland

