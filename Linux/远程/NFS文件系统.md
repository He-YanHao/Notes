# NFS文件系统 Network File System

## 什么是 NFS？

NFS（网络文件系统）是一种由 **Sun Microsystems** 于 1984 年开发的分布式文件系统协议。它允许用户和客户端计算机上的应用程序像访问本地存储一样，通过网络访问远程服务器上的文件和目录。

**核心思想**：实现**资源共享**和**集中管理**。将一台服务器（NFS Server）上的目录共享到网络中，其他计算机（NFS Clients）可以将其挂载到本地目录树中，像使用本地硬盘一样使用它。



## NFS 的工作原理

NFS 遵循经典的 **客户端-服务器（Client-Server）** 架构：

1.  **服务器端（NFS Server）**：
    *   配置一个或多个需要共享的目录（例如 `/shared/data`）。
    *   在 `/etc/exports` 配置文件中定义哪些目录被共享、允许哪些客户端访问以及访问权限（如只读、读写）。
    *   运行 `nfs-server` 服务，等待客户端的请求。

2.  **客户端（NFS Client）**：
    *   查询服务器上可用的共享目录（`showmount -e <server_ip>`）。
    *   使用 `mount` 命令将远程共享目录挂载到本地的一个空目录上（例如 `/mnt/nfs/data`）。
    *   此后，所有对该本地目录（`/mnt/nfs/data`）的读写操作都会被重定向到网络上的 NFS 服务器。

3.  **通信过程**：
    *   基于 **RPC（Remote Procedure Call，远程过程调用）**。
    *   客户端通过 RPC 与服务器的 `rpcbind` 服务通信，查询 NFS 服务使用的端口。
    *   之后的文件读写操作都在 NFS 协议上完成。

## 主要版本

*   **NFSv2**： 较老，功能有限，最大支持 2GB 的文件。
*   **NFSv3**： 支持大文件（超过 2GB）、异步写入，性能更好，曾非常流行。
*   **NFSv4**： **目前最常用和推荐的版本**。
    *   增强了安全性（集成了 Kerberos 身份验证）。
    *   防火墙友好（只需要一个 TCP 端口 2049，而旧版本需要多个端口）。
    *   支持状态连接，更稳定。
    *   引入了“伪文件系统”，管理更简单。
*   **NFSv4.1** 和 **v4.2**： 提供了并行访问（pNFS）等高级功能，进一步提升了性能和扩展性。



## 基本配置与使用示例（基于 Linux）

假设我们有两台机器：
*   **服务器** IP: `192.168.1.100`
*   **客户端** IP: `192.168.1.101`

### 在服务器端（192.168.1.100）上：

1.  **安装 NFS 服务器软件包**：
    ```bash
    # 对于 Ubuntu/Debian
    sudo apt update
    sudo apt install nfs-kernel-server
    
    # 对于 RHEL/CentOS/Fedora
    sudo dnf install nfs-utils
    ```
    
2.  **创建要共享的目录并设置权限**：
    ```bash
    sudo mkdir -p /shared/data
    sudo chown nobody:nogroup /shared/data  # 放宽权限以便客户端写入
    ```

3.  **配置导出目录**（编辑 `/etc/exports`）：
    ```
    /shared/data 192.168.1.101(rw,sync,no_subtree_check)
    ```
    *   `/shared/data`: 要共享的目录。
    *   `192.168.1.101`: 允许访问的**客户端 IP**（可以是网段 `192.168.1.0/24` 或域名 `*.example.com`）。
    *   `rw`: 读写权限（`ro` 为只读）。
    *   `sync`: 同步写入，保证数据一致性（更安全）。
    *   `no_subtree_check`: 提高可靠性，尤其当子目录被导出时。

4.  **使配置生效并启动服务**：
    ```bash
    sudo exportfs -a  # 重新导出所有配置
    sudo systemctl enable --now nfs-server  # 启动并启用开机自启
    ```

### 在客户端（192.168.1.101）上：

1.  **安装 NFS 客户端软件包**：
    ```bash
    # Ubuntu/Debian
    sudo apt install nfs-common
    
    # RHEL/CentOS/Fedora
    sudo dnf install nfs-utils
    ```

2.  **查看服务器上的可用共享**：
    ```bash
    showmount -e 192.168.1.100
    ```

3.  **创建本地挂载点并挂载**：
    ```bash
    sudo mkdir -p /mnt/nfs-data
    sudo mount -t nfs 192.168.1.100:/shared/data /mnt/nfs-data
    ```

4.  **验证挂载**：
    ```bash
    df -hT
    # 你应该能看到一行类似下面的输出：
    # 192.168.1.100:/shared/data nfs4 ... /mnt/nfs-data
    ```

5.  **（可选）设置开机自动挂载**：
    编辑 `/etc/fstab` 文件，添加一行：
    
    ```
    192.168.1.100:/shared/data /mnt/nfs-data nfs defaults 0 0
    ```



## 安全性考虑

*   **权限映射**： NFS 依赖于服务器和客户端之间的 UID/GID。如果两边的用户 ID 不一致，会导致权限混乱。常用选项：
    *   `root_squash`（默认）： 将客户端的 root 用户映射到服务器上的无名用户（`nobody`），提升安全性。
    *   `all_squash`： 将所有客户端用户都映射到指定的匿名用户，适用于公共共享。
*   **网络隔离**： 使用防火墙限制可以访问 NFS 端口的客户端 IP 地址。
*   **使用更安全的版本**： 尽量使用 **NFSv4**，它支持 **Kerberos** 身份验证，提供了更强的安全性（`sec=krb5`）。



## 常见应用场景

1.  **共享家目录（Home Directory）**： 在集群或大学机房中，用户无论从哪台机器登录，都能看到相同的家目录。
2.  **虚拟化与云平台**： 为虚拟机（如 VMware、KVM）提供共享存储，允许虚拟机在不同物理主机间迁移。
3.  **Web 集群**： 多台 Web 服务器（如负载均衡后的节点）通过 NFS 共享同一个存储目录，用于存放用户上传的附件、图片等。
4.  **数据备份和集中存储**： 将重要数据集中存放在一台高性能存储服务器上，方便备份和管理。
5.  **嵌入式开发**： 在开发板上通过网络挂载主机上的 NFS 共享目录，方便调试和传输文件。

