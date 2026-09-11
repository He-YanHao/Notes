# rockdevLinux系统SDK编译实战

## 以嘉立创泰山派为例

下载SDK包，并解压。



## SDK包内容

-   app： 存放上层应⽤ app，主要是 qcamera/qfm/qplayer/settings 等⼀些应⽤程序。 
-   buildroot： 基于 buildroot (2018.02-rc3) 开发的根⽂件系统。 
-   debian： 基于debian 10 开发的根⽂件系统，⽀持部分芯⽚。
-   device/rockchip： 存放各芯⽚板级配置和Parameter⽂件，以及⼀些编译与打包固件的脚本和预备⽂件。 
-   IMAGE： 存放每次⽣成编译时间、XML、补丁和固件⽬录。
-   external： 存放第三⽅相关仓库,包括⾳频、视频、⽹络、recovery 等。
-   kernel：存放 kernel 4.4 或 4.19 开发的代码。
-   prebuilts： 存放交叉编译⼯具链。
-   rkbin：存放 Rockchip 相关的 Binary 和⼯具。
-   rockdev： 存放编译输出固件。
-   tools：存放 Linux 和 Windows 操作系统环境下常⽤⼯具。
-   u-boot：存放基于 v2017.09 版本进⾏开发的 uboot 代码。
-   yocto：基于 yocto gatesgarth 3.2 开发的根⽂件系统，⽀持部分芯⽚。



## 编译环境配置

假设为Ubuntu

```shell
sudo apt install -y bc binfmt-support bison build-essential ccache chrpath clang cmake cpio curl debootstrap device-tree-compiler diffstat expect fakeroot file flex g++ g++-multilib gawk gcc gcc-multilib gettext git git-core gnupg gperf imagemagick lib32ncurses5-dev lib32z1-dev libc6-dev-i386 libdrm-dev libgl1-mesa-dev libglib2.0-dev libgmp-dev liblz4-tool libmpc-dev libmpfr-dev libncurses5-dev libssl-dev libudev-dev libx11-dev libxml2-utils live-build lzop make ncurses-dev openjdk-8-jdk patchelf python-pip python2.7 qemu-user-static repo rsync schedtool ssh sudo texinfo time unzip vim-common wget x11proto-core-dev xsltproc zip zlib1g-dev python-is-python3
```



## 板级配置

运行

```shell
./build.sh lunch
```



查看配置

```shell
./build.sh -h kernel
```



## 编译SDK

首先指定编译系统

```shell
export RK_ROOTFS_SYSTEM=buildroot
```



### 全编译

```shell
./build.sh all
```

之后打包

```shell
./mkfirmware.sh
```

打包成功后，固件会输出到 `rockdev` 目录。



#### u-boot编译

```shell
./build.sh uboot
```

-   **U-Boot 是什么**：引导加载程序，负责硬件初始化、加载内核
-   **编译内容**：针对 Rockchip 芯片的定制化 U-Boot
-   **使用场景**：修改了启动参数、设备树或引导相关代码后



#### kernel编译

```shell
./build.sh kernel
```

-   **Kernel 是什么**：Linux 内核，操作系统的核心
-   **编译内容**：Rockchip 的 Linux 内核，包含芯片特定驱动
-   **使用场景**：修改了驱动程序、内核配置或设备树后



#### Recovery编译命令

```shell
./build.sh recovery
```

-   **Recovery 是什么**：独立的恢复系统，用于系统更新、恢复出厂设置
-   **编译内容**：基于 RAMDisk 的最小化 Linux 系统
-   **使用场景**：制作系统升级包或修改恢复模式功能





#### buildroot编译

```shell
./build.sh rootfs
```

-   **Rootfs 是什么**：用户空间的根文件系统，包含应用程序、库文件等
-   **编译内容**：使用 Buildroot 构建的完整根文件系统
-   **使用场景**：添加/删除应用程序、修改系统配置后







