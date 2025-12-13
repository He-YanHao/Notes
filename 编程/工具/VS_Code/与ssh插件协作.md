# 与ssh插件协作

### 步骤 1：虚拟机网络设置

确保虚拟机网络是 **桥接模式** 或 **NAT + 端口转发**，让物理机可以通过 SSH 连接到虚拟机。

-   **桥接模式**：虚拟机会获得一个和物理机同网段的 IP，直接 SSH 该 IP。
-   **NAT模式**（更常见）：需要在虚拟机网络设置里做端口转发（例如将主机 2222 端口转发到虚拟机 22 端口），然后 Xshell 连接 `127.0.0.1:2222`。



### **步骤 2：在虚拟机中开启 SSH 服务**

```bash
# 以 Ubuntu/Debian 为例
sudo apt update
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
# 查看 IP
ip addr
```

记下虚拟机的 IP 地址（例如 `192.168.1.xxx`）。



### 步骤 3：使用 Xshell 连接虚拟机

1.  打开 Xshell，新建会话。
2.  主机填写虚拟机的 IP（桥接模式）或 `127.0.0.1`（NAT 端口转发时）。
3.  端口根据实际情况填写（默认 22 或转发的端口）。
4.  身份验证输入虚拟机的用户名和密码（或使用密钥）。
5.  连接成功，此时你已经在虚拟机的终端里操作。



### 步骤 4：在虚拟机中安装并配置 VSCode

#### **方案 A：直接安装 VSCode（有图形界面时）**

如果你虚拟机有桌面环境（如 Ubuntu Desktop）：

bash

```
# 下载并安装
sudo snap install --classic code  # 或从微软仓库安装
```

安装后，你可以在虚拟机内部打开图形界面的 VSCode，但**通过 Xshell 无法直接显示图形**，需要 X11 转发（较慢，不推荐）。



#### **方案 B：安装 VSCode 并仅用其命令行工具**

安装 VSCode 后，即使不启动 GUI，也可以使用 `code` 命令在终端中操作文件或目录：

bash

```
code /path/to/project        # 在虚拟机桌面环境打开项目（需要图形）
code --help                  # 查看命令行选项
```



#### **方案 C：安装 code-server（Web 版 VSCode，推荐）**

这是**最推荐**的方式，可以在浏览器里使用完整的 VSCode。

1.  **在虚拟机中安装 code-server**
    参考 [https://coder.com](https://coder.com/) 或直接：

    bash

    ```
    curl -fsSL https://code-server.dev/install.sh | sh
    systemctl --user enable --now code-server
    ```

    

2.  **配置 code-server**
    编辑 `~/.config/code-server/config.yaml`：

    yaml

    ```
    bind-addr: 0.0.0.0:8080
    auth: password
    password: your_password_here
    cert: false
    ```

    

3.  **启动 code-server**

    bash

    ```
    code-server
    ```

    

4.  **在物理机浏览器访问**
    浏览器打开 `http://虚拟机IP:8080`，输入密码，即可得到完整的 VSCode 网页版。

------

### **步骤 5：通过 Xshell 端口转发访问 code-server**

如果虚拟机是 NAT 模式，物理机无法直接访问虚拟机的 8080 端口，可以在 Xshell 会话设置中做**端口转发**：

1.  在 Xshell 会话属性中，找到 **“隧道” → “转移的TCP”**。
2.  添加一条转移规则：
    -   源主机：`127.0.0.1`
    -   侦听端口：`8080`
    -   目标主机：`虚拟机IP`
    -   目标端口：`8080`
3.  重新连接会话，此时在物理机浏览器访问 `http://127.0.0.1:8080` 就会转发到虚拟机的 code-server。

------

### **方案对比**

| 方式                               | 优点                                  | 缺点                                 |
| :--------------------------------- | :------------------------------------ | :----------------------------------- |
| Xshell + 纯命令行编辑              | 速度快，适合简单编辑                  | 没有 VSCode 的完整 GUI               |
| Xshell + X11 转发图形界面 VSCode   | 可在物理机显示虚拟机 GUI              | 非常卡顿，依赖 X11 配置              |
| **Xshell + code-server（网页版）** | **体验接近本地 VSCode，无需图形界面** | 需安装配置 code-server，占用一些内存 |

------

### **推荐最终方案**

1.  虚拟机安装 **code-server**。
2.  用 Xshell SSH 连接虚拟机，管理服务器和启动 code-server。
3.  在物理机浏览器通过 **Xshell 隧道端口** 访问 `http://localhost:8080`，获得完整的 VSCode 开发环境。

这样既利用了 Xshell 稳定 SSH 连接，又享受了 VSCode 的强大编辑功能，且不依赖虚拟机图形性能。