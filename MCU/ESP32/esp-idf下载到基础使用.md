# esp-idf下载到基础使用

## 下载

```bash
# 在家目录下创建esp文件夹
mkdir -p ~/esp
cd ~/esp
# 使用git克隆所需版本的esp-idf
git clone -b v5.1.2 --recursive https://github.com/espressif/esp-idf.git
```

安装所需工具

```bash
# 在esp目录下创建esp-idf文件夹
mkdir -p ~/esp/esp-idf
cd ~/esp/esp-idf
# 下载所以
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



## 激活环境脚本

使用

```bash
. $HOME/esp/esp-idf/export.sh
```

激活环境脚本。





## 编译下载

```bash
# 1. 进入项目目录
cd ~/esp/hello_world

# 2. 设置目标（如果还没设置）
idf.py set-target esp32s3

# 3. 重新编译（确保最新）
idf.py build

# 4. 连接开发板并进入下载模式（按上面步骤）

# 5. 烧录（根据实际设备名）
idf.py -p /dev/ttyACM0 flash
# idf.py -p /dev/ttyUSB0 flash
```



## 监听串口

```bash
idf.py -p /dev/ttyACM0 monitor
```

- **退出监视器**：按 `Ctrl+]`
- **重启设备**：在监视器中按 `Ctrl+T` 然后按 `Ctrl+R`
- **查看帮助**：在监视器中按 `Ctrl+T` 然后按 `Ctrl+H`



## 清除构建文件

```bash
idf.py fullclean
```

