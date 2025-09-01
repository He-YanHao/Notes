# Debian系Linux软件管理基础

## Debian系成员

Debian系包括：

- Ubuntu乌邦图

## 软件仓库

每个发行版都会有自己可以的软件仓库，要么自己建立，要么与其他发行版共用一个。

使用软件仓库相关指令安装软件可自行解决依赖问题。

### apt 与 apt-get

`apt` 主要用于用户在终端环境中键入，提供良好的用户交互。

`apt-get` 主要用于 shell 脚本。

二者命令几乎一致。

### 软件仓库更新软件包列表

```shell
sudo apt update
sudo apt-get update
```

将需要更新的软件信息保存到本地，但不实际更新。

### 软件仓库升级所有软件包

```shell
sudo apt upgrade
sudo apt-get upgrade
```

根据本地软件信息更新系统。

可通过添加包名参数仅升级特定软件。

### 软件仓库全面升级

```shell
sudo apt full-upgrade       #apt-get与apt有所不同
sudo apt-get dist-upgrade   #apt-get与apt有所不同
```

### 软件仓库安装软件

```shell
#安装前先更新整个系统的软件包，避免依赖问题。
sudo apt update           #获取系统更新数据
sudo apt install          #更新系统
sudo apt install <包名>    #安装软件

sudo apt-get update           #获取系统更新数据
sudo apt-get install          #更新系统
sudo apt-get install <包名>    #安装软件
```

### 软件仓库查找软件

```shell
sudo apt search <包名>    #安装软件
sudo apt-get search <包名>    #安装软件
```



### 软件仓库删除软件包保留配置

```shell
sudo apt remove <包名>
sudo apt-get remove <包名>
```

### 软件仓库删除软件包及配置

```shell
sudo apt purge <包名>
sudo apt-get purge <包名>
```

### 软件仓库自动移除无用包

```shell
sudo apt autoremove
sudo apt-get autoremove
```



## snap包

一种软件格式。

### 列出全部已安装snap包

```shell
snap list
```

### 列出全部可更新snap包但不更新

```shell
sudo snap refresh --list
```



### 列出全部可更新snap包并更新

```shell
sudo snap refresh
```







## 打包系统

Debian系的打包系统为 ` .deb ` 

### 打包系统安装软件

如果软件仓库没有需要的软件，可以下载软件包后使用以下指令安装。

下载好后依旧可以使用包管理器进行安装。

```shell
sudo apt install ./*.deb
```

包管理器可以自行处理依赖问题。

或者使用底层安装工具 `dpkg` ，`apt` 其实也是在调用 `dpkg` 

```shell
sudo dpkg -i *.deb
```

但使用软件包安装软件无法自动解决依赖问题。

### 打包系统删除软件包保留配置

```shell
sudo dpkg -r <包名>
```

### 打包系统删除软件包及配置

```shell
sudo dpkg -P <包名>
```

### 打包系统列出安装过的软件包

```shell
sudo dpkg -l
```

