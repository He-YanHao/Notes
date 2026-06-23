# Linux

## 基础

好的，我们来全面介绍一下 Linux 环境中功能强大且使用广泛的网络下载工具——**`wget`**。

### 什么是 wget？

**`wget`**（**W**orld **W**ide **W**eb **get**）是一个命令行工具，用于从网络上下载文件。它支持 **HTTP**、**HTTPS** 和 **FTP** 协议，是 Linux、Unix 甚至 Windows（通过安装）环境下通过终端进行非交互式（无需用户干预）文件下载的**事实标准**。

它的核心特点是：
*   **简单直接**：只需一个命令和 URL 即可开始下载。
*   **非交互式**：可以在后台运行，无需用户登录或停留在终端前。这使得它非常适合用于脚本中。
*   **功能强大**：支持递归下载、断点续传、后台下载、限速等高级功能。
*   **健壮性强**：对不稳定的网络连接有很好的容错能力，会持续重试直到下载完成。

---

### 与 `curl` 的简单对比

很多人会问 `wget` 和 `curl` 的区别。简单来说：

| 特性         | `wget`                           | `curl`                                                       |
| :----------- | :------------------------------- | :----------------------------------------------------------- |
| **核心功能** | **专注于下载**                   | **专注于数据传输**（支持更多协议：SCP, SFTP, LDAP, DICT 等） |
| **模式**     | **非交互式**（更适合后台、脚本） | **交互式**（更适合API调用、测试、快速传输）                  |
| **下载行为** | 默认将文件**下载到磁盘**         | 默认将输出**显示到标准输出（stdout）**，需要 `-O` 选项来保存 |
| **递归**     | **原生支持**递归下载整个网站     | **不支持**递归下载                                           |
| **协议**     | 主要支持 HTTP, HTTPS, FTP        | 支持**极其广泛**的协议（~20种）                              |

**简单选择：**
*   **想下载文件？** -> 用 `wget`
*   **想和Web API交互、测试接口、快速传输数据？** -> 用 `curl`

---

### 安装 wget

在大多数 Linux 发行版上，`wget` 通常已经预装。如果没有，可以使用包管理器轻松安装：

*   **Debian/Ubuntu**:
    ```bash
    sudo apt update && sudo apt install wget
    ```
*   **RHEL/CentOS/Fedora**:
    ```bash
    sudo yum install wget    # RHEL/CentOS 7及以下
    sudo dnf install wget    # RHEL/CentOS 8/Fedora
    ```
*   **Arch Linux**:
    ```bash
    sudo pacman -S wget
    ```

---

### 基本用法与常用选项

#### 1. 最简单的下载
```bash
wget https://example.com/file.zip
```
这将把 `file.zip` 下载到**当前目录**。

#### 2. 指定下载文件的保存名称 (`-O`)
```bash
wget -O myfile.zip https://example.com/files/very-long-name-v1.2.3.zip
```
这将把文件保存为 `myfile.zip`，而不是原文件名。

#### 3. 断点续传 (`-c`)
**极其有用的功能**。如果下载中途中断（比如网络掉线），重新使用此命令可以从中断的地方继续下载，而无需重新开始。
```bash
wget -c https://example.com/big-file.iso
```

#### 4. 后台下载 (`-b`)
对于大文件，可以放入后台下载，终端可以继续做其他事情。
```bash
wget -b https://example.com/big-file.iso
```
后台下载的日志默认会写入当前目录的 `wget-log` 文件中，可以使用 `tail -f wget-log` 查看进度。

#### 5. 限速下载 (`--limit-rate`)
为了避免下载占用所有带宽，影响其他网络活动，可以限制下载速度。单位可以是 `k` (KB/s) 或 `m` (MB/s)。
```bash
wget --limit-rate=500k https://example.com/large-file.tar.gz
```

#### 6. 递归下载（镜像整个网站）(`-r`, `-l`, `-np`)
这是 `wget` 的王牌功能，可以下载整个网站以供离线浏览。
```bash
wget -r -l 5 --no-parent https://example.com/path/
```
*   `-r`: 递归下载。
*   `-l 5`（小写L）: 设置递归最大深度为5层。不设置则无限递归（小心使用！）。
*   `-np` (`--no-parent`): 不追溯至父目录。只在指定的 `path/` 目录内下载，不会爬到 `example.com` 的根目录。

#### 7. 伪装浏览器 User-Agent (`-U`)
有些网站会阻止常见的 `wget` User-Agent，可以伪装成浏览器。
```bash
wget -U "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" https://example.com
```

#### 8. 下载 FTP 资源
```bash
# 匿名 FTP
wget ftp://ftp.example.com/file.txt

# 需要认证的 FTP
wget --ftp-user=username --ftp-password=password ftp://ftp.example.com/private-file.txt
```

#### 9. 静默输出 (`-q`) 或 详细输出 (`-v`)
```bash
wget -q https://example.com/file.zip # 安静模式，不输出信息
wget -v https://example.com/file.zip # 详细模式，输出更多信息
```

#### 10. 跳过证书检查 (`--no-check-certificate`)
在下载一些使用自签名证书的 HTTPS 网站时，如果证书不受信任，可以使用此选项跳过检查（**安全性会降低，请谨慎使用**）。
```bash
wget --no-check-certificate https://internal-site.example.com/file
```

---

### 高级用法与示例

#### 1. 从文本文件读取多个 URL 进行批量下载 (`-i`)
创建一个文件 `urls.txt`，里面每行一个下载链接：
```
https://example.com/file1.zip
https://example.com/file2.zip
https://example.com/file3.zip
```
然后执行：
```bash
wget -i urls.txt
```

#### 2. 镜像整个网站（适合离线浏览）
```bash
wget --mirror --convert-links --adjust-extension --page-requisites --no-parent -P my_website_copy https://example.com
```
*   `--mirror`: 相当于 `-r -N -l inf --no-remove-listing`，开启镜像模式。
*   `--convert-links`: 下载完成后，转换文档中的链接，使其适合本地离线查看。
*   `--adjust-extension`: 为没有扩展名的文件添加合适的扩展名（如 `.html`）。
*   `--page-requisites`: 下载所有显示完整页面所需的资源（图片、CSS、JS等）。
*   `-P my_website_copy`: 将所有文件下载到 `my_website_copy` 目录中。

#### 3. 重试次数和超时设置
在不稳定的网络环境中，可以调整重试策略。
```bash
wget -t 10 -T 30 -w 5 https://example.com/unstable-file
```
*   `-t 10`: 重试最多10次（0表示无限重试）。
*   `-T 30`: 设置网络超时时间为30秒。
*   `-w 5`: 在两次重试之间等待5秒。

### 总结

`wget` 是一个不可或缺的命令行下载工具，其核心优势在于：

*   **简单可靠**：一个 URL 即可开始稳定的下载。
*   **功能全面**：尤其是**断点续传 (`-c`)** 和**递归下载 (`-r`)** 功能，解决了下载中的两大痛点。
*   **易于脚本化**：其非交互的特性使其成为 Shell 脚本中处理下载任务的**最佳选择**。

无论是快速下载一个文件，还是复杂地镜像整个网站，`wget` 都能高效地完成任务。记住 `wget -c`（断点续传）和 `wget -b`（后台下载）这两个最常用的选项，就能极大地提升你的工作效率。
