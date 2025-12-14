# 与ssh插件协作

## Xshell

### 虚拟机网络设置

确保虚拟机网络是 **桥接模式** 或 **NAT + 端口转发**，让物理机可以通过 SSH 连接到虚拟机。

-   **桥接模式**：虚拟机会获得一个和物理机同网段的 IP，直接 SSH 该 IP。
-   **NAT模式**（更常见）：需要在虚拟机网络设置里做端口转发（例如将主机 2222 端口转发到虚拟机 22 端口），然后 Xshell 连接 `127.0.0.1:2222`。



### 在虚拟机中开启 SSH 服务

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

### 使用 Xshell 连接虚拟机

1.  打开 Xshell，新建会话。
2.  主机填写虚拟机的 IP（桥接模式）或 `127.0.0.1`（NAT 端口转发时）。
3.  端口根据实际情况填写（默认 22 或转发的端口）。
4.  身份验证输入虚拟机的用户名和密码（或使用密钥）。
5.  连接成功，此时已经在虚拟机的终端里操作。

## VSCode

Windows 进入 VSCode

1. 按 F1 或 Ctrl+Shift+P
2. 输入 "Remote-SSH: Connect to Host"
3. 选择 "Add New SSH Host"
4. 输入连接字符串：ssh username@IP地址 -p 端口号
   
   示例：ssh ubuntu@192.168.1.100 -p 22
5. 选择配置文件保存位置（默认 ~/.ssh/config）



1.  按 F1 → "Remote-SSH: Connect to Host"
2.  选择配置好的主机（如 `my-ubuntu`）
3.  选择平台（Linux）
4.  输入密码（如果使用密钥则不需要）

**第一次连接会自动：**

-   在服务器下载VS Code Server
-   安装必要的组件
-   建立连接通道

