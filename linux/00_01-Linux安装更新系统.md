# Linux安装更新系统

## 挂载点

首先需要`/`，

需要`/boot`分区挂载启动文件，选择`/boot/efi`，其中只有Linux系统的电脑可以直接`/boot`，但依旧推荐`/boot/efi`，大小选择在512MB左右。

`swap`作为交换分区，推荐为设备内存大小+2GB，内存大于64GB则无需交换分区。挂载点无，或无法选择挂载点。

`/usr`

`/ver`

`/home`，用户的家目录，

## 系统格式

ext4主流，没啥特点。

xfs比较稳定，且对大文件有优化。

btrfs潜力大，但现在尚不稳定。

## 更新系统

### Debian系列

```
update - 取回更新的软件包列表信息
upgrade - 进行一次升级


install - 安装新的软件包（注：软件包名称应当类似 libc6 而非 libc6.deb）
reinstall - 重新安装软件包（注：软件包名称应当类似 libc6 而非 libc6.deb）
  remove - 卸载软件包
  purge - 卸载并清除软件包的配置
  autoremove - 卸载所有自动安装且不再使用的软件包
  dist-upgrade - 发行版升级，见 apt-get(8)
  dselect-upgrade - 根据 dselect 的选择来进行升级
  build-dep - 为源码包配置所需的编译依赖关系
  satisfy - 使系统满足依赖关系字符串
  clean - 删除所有已下载的包文件
  autoclean - 删除已下载的旧包文件
  check - 核对以确认系统的依赖关系的完整性
  source - 下载源码包文件
  download - 下载指定的二进制包到当前目录
  changelog - 下载指定软件包，并显示其变更日志（changelog）

```



## 重装Grub

```
sudo grub				//启动Grub
find /boot/grub/stage1	//查找/boot存放的硬盘分区
root (hdx,y)			//指示/boot分区的位置，将其替换为(hdx,y)
setup (hd0)				//在第一块硬盘上安装Grub
quit					//退出
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