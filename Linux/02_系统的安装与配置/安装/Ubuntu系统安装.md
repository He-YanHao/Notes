# Ubuntu系统安装





## 更新系统(指常规的软件全面更新)

| 功能描述           | `apt-get` / `apt-cache` 命令 | `apt` 命令            | 说明                                                   |
| ------------------ | ---------------------------- | --------------------- | ------------------------------------------------------ |
| **更新软件包列表** | sudo apt-get update          | sudo apt update       | `apt` 的输出更有信息量，会显示可升级的软件包数量。     |
| **升级所有软件包** | sudo apt-get upgrade         | sudo apt upgrade      | `apt` 会添加一个漂亮的进度条，并询问确认。             |
| **全面升级**       | sudo apt-get dist-upgrade    | sudo apt full-upgrade | `apt` 的命令名 `full-upgrade` 更直观地反映了它的行为。 |





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



