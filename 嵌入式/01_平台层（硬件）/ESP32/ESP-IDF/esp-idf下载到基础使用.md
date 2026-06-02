# esp-idf下载到基础使用

## 下载

### Linux环境下载

首先安装必要工具

```bash
# 安装必要工具
sudo apt install git wget flex bison gperf python3 python3-pip python3-venv cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
```

克隆仓库：

```bash
# 在家目录下创建esp文件夹
mkdir -p ~/esp
cd ~/esp
# 克隆仓库
git clone -b <版本> --recursive https://github.com/espressif/esp-idf.git
# 例如：git clone -b v5.5.4 --recursive https://github.com/espressif/esp-idf.git
```

>   查询最新的版本：
>
>   - 在 bash shell ：
>
>     ```bash
>     git ls-remote --tags https://github.com/espressif/esp-idf.git | grep -E "v[0-9]+\.[0-9]+\.[0-9]+$" | cut -d/ -f3 | sort -V | tail -n1
>     ```
>
>   - 在 fish shell ：
>
>     ```fish
>     git ls-remote --tags https://github.com/espressif/esp-idf.git | grep -E 'v[0-9]+\.[0-9]+\.[0-9]+$' | cut -d/ -f3 | sort -V | tail -n1
>     ```
>
>   - 实际上，去GitHub上看一眼更方便。



设置下载工具

```bash
cd ~/esp/esp-idf
# 下载目标可以是单个型号 用逗号分隔 如 esp32,esp32c3
./install.sh all
```

如果遇到python问题导致下载失败，在 `~/.pip/pip.conf` 路径下创建文件，写入：

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com
```

写入环境变量解决下载过慢问题

```bash
cd ~/esp/esp-idf
export IDF_GITHUB_ASSETS="dl.espressif.com/github_assets"
./install.sh
```



### Window环境下载

直接使用 `eim-gui-windows-x64.exe` 。



## 激活环境脚本

### Linux 环境下

每次使用都要运行

```bash
. $HOME/esp/esp-idf/export.sh
```

激活环境脚本。

如果使用的终端是 `fish` 就使用：

```bash
. $HOME/esp/esp-idf/export.fish
```

不然，无法使用编译所需的虚拟环境。



### Windows 环境下

运行安装后自动创建的快捷方式。



## 编译下载

```bash
# 1. 进入项目目录
cd ~/esp/hello_world

# 2. 设置目标（如果还没设置）
idf.py set-target esp32c3

# 3. 重新编译（确保最新）
idf.py build

# 4. 烧录（根据实际设备名）
idf.py -p <串口名称> flash

# 5. 监听
idf.py -p <串口名称> monitor 

# 6. 烧录并且监听
idf.py -p <串口名称> flash monitor 

# 7. 仅仅编译app
idf.py -p <串口名称> app

# 8. 仅仅烧录app
idf.py -p <串口名称> app-flash
```

> **背景知识**
>
> 一个完整的 ESP32 项目通常包含三个部分：
>
> 1. **引导加载程序（Bootloader）**：芯片启动时最先运行的代码，负责加载应用程序。
> 2. **分区表（Partition Table）**：定义 Flash 中各个区域（如应用、文件系统、NVS）的布局。
> 3. **应用程序（App）**：自己的业务代码。
>
> 大部分之间修改App部分的代码，也只烧录App部分的代码，因此可以使用
>
> ```
> # 7. 仅仅烧录app
> idf.py -p <串口名称> flash monitor 
> ```
>
> 来进行加速。



## 监听串口

**退出监视器**：按 `Ctrl+]`



## 清除构建文件

```bash
idf.py fullclean
```



## 菜单

```
idf.py menuconfig
```



