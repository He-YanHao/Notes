# Termux

## 基础

是一种基于安卓模拟Linux环境的软件。

## 配置

| 章节                    | 内容说明                                                     |
| :---------------------- | :----------------------------------------------------------- |
| 一、Termux 安装与初始化 | 介绍如何获取、安装 Termux，以及进行最初的软件包更新和存储权限设置。 |
| 二、基础配置与美化      | 包括更换国内源、安装基础软件包（如 Python、Node.js）、ZSH 美化等。 |
| 三、常用开发环境搭建    | 涵盖 Python、Node.js、C/C++、Java、Web 服务器（Nginx）及数据库（MariaDB）的安装。 |
| 四、高级用法与远程连接  | 介绍如何通过 SSH 远程连接 Termux，以及安装完整 Linux 子系统。 |
| 五、常见问题与解决      | 汇总了中文乱码、存储空间不足、进程被杀死等常见问题的解决方法。 |
| 六、安全与维护建议      | 提供了一些保持 Termux 安全、稳定运行的建议。                 |

### 更新

```shell
pkg update      #
pkg upgrade -y  #
```

### 获取内存访问权限

```shell
termux-setup-storage
```

### 换国内源

```shell
termux-change-repo
```

在图形界面中，用空格键选中 `Mirrors in China`（例如清华源或中科大源），然后按回车确认。完成后再次运行 `pkg update` 更新列表。

### 安装软件

```shell
pkg install python nodejs clang git vim openssh -y
```

### 终端美化（安装 ZSH + Oh My Zsh）

1. **安装 ZSH**：

    ```bash
    pkg install zsh -y
    ```

2. **安装 Oh My Zsh**：

    ```bash
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    ```

    安装过程中如果询问是否将默认 shell 切换为 zsh，选择 `是`。

3. **（可选）安装 Powerlevel10k 主题**：
    这是一个非常流行且强大的 ZSH 主题。Oh My Zsh 安装后，可以继续安装 Powerlevel10k：

    ```bash
    git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
    ```

    然后编辑 `~/.zshrc` 文件，将 `ZSH_THEME` 的值改为 `ZSH_THEME="powerlevel10k/powerlevel10k"`，然后执行 `source ~/.zshrc` 重新加载配置，会进入主题的配置向导。



## 安装完整Linux

1. **安装 proot-distro**：

    ```bash
    pkg install proot-distro -y
    ```

2. **查看可安装的系统列表**：

    ```bash
    proot-distro list
    ```

3. **安装 Debian**：

    ```bash
    proot-distro install debian
    ```

4. **登录到 Debian**：

    ```bash
    proot-distro login debian
    ```

    之后你就可以在这个 Debian 环境里使用 `apt` 安装更多软件了。





