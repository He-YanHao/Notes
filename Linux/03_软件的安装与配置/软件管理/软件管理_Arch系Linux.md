# Arch系Linux软件管理基础

## 软件仓库

Arch系Linux使用 `pacman` 软件仓库



## 更新所以软件

### 仅更新软件包数据库

仅更新软件包数据库（不更新系统）

```shell
sudo pacman -Sy
```



### 更新包数据库上的所有

这是 **推荐的做法**。`-Syu` 是 `-S`（同步）、`-y`（刷新）和 `-u`（升级）的组合

```shell
sudo pacman -Syu
```

这条命令会先从配置的软件仓库同步最新的软件包数据库（确保本地数据库知道远程有哪些新版本），然后升级系统中所有有可用更新的软件包。

还分为

```shell
sudo pacman -Syyu
# 强制更新包数据库和系统上的所有。
```

和

```shell
sudo pacman -Syyuu
# 强制更新包数据库和系统上的所有并允许降级。
```



## 安装软件

### 安装pacman仓库里的软件

```shell
sudo pacman -S <包名>
# 普通的安装
sudo pacman -S extra/包名
# 选择不同软件仓库的安装
```



### 安装本地软件包

```shell
sudo pacman -U /路径/包.pkg.tar.zst
```



### 从互联网上安装软件

```shell
sudo pacman -U URL
```







## 删除软件

### 删除软件包留依赖

```shell
sudo pacman -R <包名>
```



### 删除软件包清除依赖

```shell
sudo pacman -Rs <包名>
```



### 删除软件包清除依赖以及配置文件

```shell
sudo pacman -Rns <包名>
```



### 清理孤立包

有时在卸载软件包后，一些依赖可能会残留。你可以手动查找并删除所有未被任何已安装软件包需要的依赖包（孤立包）。

```shell
# 首先列出孤儿包
pacman -Qtdq

# 确认无误后删除它们
sudo pacman -Rns $(pacman -Qtdq)

# 如果上述命令没有返回任何包，则会显示 "no orphans to remove":cite[10]。
```



## 搜索软件

### 搜索pacman仓库里的软件

```shell
sudo pacman -Ss 搜索内容
```



### 搜索本地的软件

```shell
sudo pacman -Qs 搜索内容
```



## 列出本地软件

```shell
sudo pacman -Ql
```



## 清理包缓存

`pacman` 会将下载的软件包（`.pkg.tar.zst` 文件）保存在 `/var/cache/pacman/pkg/` 目录下，并不会自动删除旧版本或已卸载的包。

这样设计的好处是允许你降级软件包或直接重新安装缓存中的包，但久而久之会占用大量磁盘空间。

### 清理未安装的包缓存

清理缓存中当前未被系统安装的任何版本的软件包。

```shell
sudo pacman -Sc
```

这是最常用的清理方式，相对安全。



### 清理所有缓存

清理缓存目录中的所有软件包，包括那些当前已安装的软件包的旧版本和当前版本。

```shell
sudo pacman -Scc
```

一般只在磁盘空间极度不足时考虑使用。



## yay

首先确定安装了必要的工具

```shell
$ sudo pacman -S git base-devel
```

国内克隆`yay-bin`源码

```shell
$ git clone https://aur.archlinux.org/yay-bin.git
$ cd yay-bin
```

进入`yay`

```shell
$ cd yay
```

构建

```shell
$ makepkg -si
```



## 安装deb包

需要先安装debtap

```bash
yay -S debtap
```

首次使用需要更新debtap的包信息映射表

```
sudo debtap -u
```

```bash
debtap your_package.deb
```






