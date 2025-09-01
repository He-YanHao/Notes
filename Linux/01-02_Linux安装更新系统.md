# Linux安装更新系统

## 挂载点

首先需要`/boot/efi`，用于启动UEFI， 512MB即可，必须为FAT32格式以供兼容性。

接下来为`/`，系统根目录，若不挂载其他区域，系统会从中分配出其他区域空间，挂载好这个之后达到开机最低要求。

接下来为可选挂载点：

- `/boot`：用于存储和启动内核以及引导文件，2GB大小，若不独立分配系统会从`/`分区分配。

	过去`/boot`一般需要独立分区，因为传统BIOS不足以访问`/`分区，而现代UEFI可以访问`/`分区，并从中读取本因存储在`/boot`分区的数据，因此可以不独立分区。
	但若需要对`/boot`分区进行加密，则现代UEFI依旧没有能力访问加密后的`/boot`，此时需要独立分区。

- `swap`作为交换分区，推荐为设备内存大小+2GB，内存大于32GB则无需交换分区。挂载点无，或无法选择挂载点。

- `/usr`：一般不建议独立分区，系统会从`/`里自动分配，如果单独分区，需要分配**非常大的空间**（几十GB到数百GB），因为它包含了几乎所有软件。
- `/var`：建议独立分区，用于存放日志等高度变化的文件，独立分区后日志或缓存失控增长仅会拖垮这个分区而非整个系统。
- `/home`：存储软件配置和用户个人文件。

| 挂载点    | 名称         | 格式     | 作用                                                         | 大小要求                     |
| --------- | ------------ | -------- | ------------------------------------------------------------ | ---------------------------- |
| /boot/efi | EFI系统分区  | FAT32    | 存储UEFI启动文件 是硬件与操作系统之间的桥梁                  | 512MB                        |
| /boot     | 引导分区     | EXT4     | 存储 内核 初始内存盘 引导加载程序                            | 2GB                          |
| /         | 根目录       | EXT4/XFS | 系统根目录，核心所在，所有其他挂载点都依附于它。             | 不小于20GB                   |
| swap      | 虚拟内存     | 无格式   | 作为虚拟内存，在物理内存(RAM)不足时使用；支持休眠(Hibernate)到磁盘。 | 内存+2GB 内存>32GB可以不要   |
| /usr      | Unix系统资源 | EXT4/XFS | 包含绝大多数的系统应用程序和只读数据                         | 一般不需要独立               |
| /var      | 可变数据目录 | EXT4/XFS | 经常变化的（Variable）数据文件                               | 桌面：10-20GB；服务器：50GB+ |
| /home     | 用户数据     | EXT4/XFS | 存储所有用户的个人数据、配置文件、下载、桌面文件等。分离数据和系统，重装系统时可保留用户数据。 | 尽可能大                     |

## 系统格式

| 文件系统 | 特点                                     |      |
| -------- | ---------------------------------------- | ---- |
| ext4     | 最主流，均衡可靠，兼容性极佳。           |      |
| xfs      | 高性能，高扩展性，企业级稳定。           |      |
| btrfs    | 功能先进（快照、压缩、校验），现代架构。 |      |

## 更新系统(指常规的软件全面更新)

### Debian系列

| 功能描述           | `apt-get` / `apt-cache` 命令 | `apt` 命令            | 说明                                                   |
| ------------------ | ---------------------------- | --------------------- | ------------------------------------------------------ |
| **更新软件包列表** | sudo apt-get update          | sudo apt update       | `apt` 的输出更有信息量，会显示可升级的软件包数量。     |
| **升级所有软件包** | sudo apt-get upgrade         | sudo apt upgrade      | `apt` 会添加一个漂亮的进度条，并询问确认。             |
| **全面升级**       | sudo apt-get dist-upgrade    | sudo apt full-upgrade | `apt` 的命令名 `full-upgrade` 更直观地反映了它的行为。 |

## 重装Grub

**这套命令主要适用于已经过时的 GRUB Legacy。** 现代 Linux 发行版普遍使用 **GRUB 2**，其命令和操作方式有了很大变化。

```shell
sudo grub				#启动Grub
find /boot/grub/stage1	#查找/boot存放的硬盘分区
root (hdx,y)			#指示/boot分区的位置，将其替换为(hdx,y)
setup (hd0)				#在第一块硬盘上安装Grub
quit					#退出
```































## 关于残魂

使用`efibootmgr`命令获取系统引导

使用`sudo efibootmgr -b ? -B`			删除系统引导，其中？是系统引导的编号

`su root`			获取管理员权限

`cd /boot/efi`			进入引导目录

`rm -r deepin/ ubuntu/`			删掉引导以deepin和ubuntu为例



# arch

### arch换源

可以直接执行

`sudo pacman-mirrors -i -c China -m rank`

换国内源

## 关于汉化

安装vim

`sudo pacman -Sy vim`

首先`sudo vim /etc/locale.gen`去掉对应语言的注释

然后在xorg状态下`vim ~/.xprofile`创建新文件，并添加两句话

通过终端输入`echo $XDG_SESSION_TYPE`若输出`x11`（xorg11的简称）则为xorg模式

```
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN:en_US
```

再使用`sudo pacman -S adobe-source-han-sans-cn-fonts`，安装字体，就完成了汉化。可肯能有较大的延迟，会出现豆腐块。

#### 更多字体

```
sudo pacman -S ttf-dejavu ttf-liberation noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-emoji-blob noto-fonts-extra wqy-bitmapfont wqy-microhei wqy-microhei-lite wqy-zenhei ttf-arphic-extra ttf-arphic-ukai ttf-arphic-uming adobe-source-code-pro-fonts adobe-source-han-sans-jp-fonts adobe-source-han-sans-tw-fonts adobe-source-han-serif-jp-fonts adobe-source-han-serif-tw-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-kr-fonts adobe-source-han-serif-cn-fonts adobe-source-han-serif-kr-fonts adobe-source-sans-fonts adobe-source-han-sans-hk-fonts adobe-source-han-sans-otc-fonts adobe-source-han-serif-hk-fonts adobe-source-han-serif-otc-fonts adobe-source-serif-fonts
```

## 输入法

编辑`/etc/environment`这个文件，添加以下几行：

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
INPUT_METHOD=fcitx
GLFW_IM_MODULE=ibus
```

输入这些回车，安装fcitx5

```
sudo pacman -S fcitx5
sudo pacman -S fcitx5-configtool
sudo pacman -S fcitx5-qt
sudo pacman -S fcitx5-gtk
sudo pacman -S fcitx5-chinese-addons
sudo pacman -S fcitx5-material-color
sudo pacman -S kcm-fcitx5
sudo pacman -S fcitx5-lua
```

## 