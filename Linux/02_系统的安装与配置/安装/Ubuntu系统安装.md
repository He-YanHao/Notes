# Ubuntu系统安装



## 汉化

确保安装了所有的语言包。

```bash
sudo apt update
sudo apt install language-pack-gnome-zh-hans language-pack-zh-hans
```

运行以下命令重新配置语言设置：

```bash
sudo dpkg-reconfigure locales
```



## 后期配置

安装核心设备驱动：

对于 NVIDIA 驱动，可以使用：

```bash
sudo ubuntu-drivers autoinstall
```



