# Linux软件管理

## 打包系统

打包系统一般分为Debian系的 ` .deb ` 和Red Hat系的 ` .rpm ` 。

- Debian系包括：Ubuntu乌邦图     Linux Mint。
- Red Hat系包括：Fedora  CentOS    OpenSUSE。

## 软件仓库

每个发行版都会有自己可以的软件仓库，要么自己建立，要么与其他发行版共用一个。

使用软件仓库相关指令安装软件可自行解决依赖问题。

## 软件管理

### Debian系

#### 软件仓库

**安装软件**

```c
//安装前先更新整个系统的软件包，避免依赖问题。
apt-get update	//获取系统更新数据
apt-get install	//更新系统
apt-get install 软件名	//安装软件
```

**删除软件**

`apt-get remove 软件包名`

**更新软件**

`  apt-get update 软件包名` 

#### 软件包

**安装软件**

如果软件仓库没有需要的软件，可以下载软件包后使用以下指令安装。

`  dpkg-i 软件包名`

但使用软件包安装软件无法自动解决依赖问题。

**删除软件**



**更新软件**



### Red Hat系

#### 软件仓库

**安装软件**



**删除软件**

用以下指令安删除。

`  yum erase 软件包名`

**更新软件**

使用以下指令更新。

`  yum update 软件包名`

若无软件包名，则是更新所以软件。

#### 软件包

**安装软件**

`  rpm-i 软件包名`

**删除软件**



**更新软件**



### arch系

通过 `sudo pacman -Syu` 更新包数据库和系统上的所有。

通过 `sudo pacman -Syyu` 强制更新包数据库和系统上的所有。

通过 `sudo pacman -Syyuu` 强制更新包数据库和系统上的所有并允许降级。

通过 `sudo pacman -Ss 搜索内容` 搜索可用包名。

 通过`sudo pacman -U 网址` 从互联网上获取软件包。

通过 `sudo pacman -R 包名` 删除软件包，但会留下依赖。

通过 `sudo pacman -Rsu 包名` 删除软件包，并清除依赖。

通过 `sudo pacman -Rc 包名` 删除软件包以及依赖于它的内容。













