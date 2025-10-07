# Linux文件系统分区

## 列出块设备lsblk

列出块设备，以树形结构清晰显示所有磁盘和分区。

| 选项 | 说明                       |      |
| ---- | -------------------------- | ---- |
| `-a` | 显示所有设备（包括空设备） |      |
| `-f` | 查看文件系统信息           |      |
| `-l` | 使用列表格式输出（非树状） |      |
| `-t` | 显示设备的拓扑信息         |      |





## fdisk系分区

适用于 MBR

### fdisk



```shell
fdisk -l
# 列出素所有分区表
```





```shell
fdisk -d
# 删除分区


a ：活动分区标记/引导分区


n ：新建分区


p ：显示分区信息



t ：设置分区号


w ：保存修改

```





### cfsik



cfsik 具有互动式操作界面而非传统fdisk的问答式界面



### sfdisk



## gdisk分区

适用于 GPT





## parted 分区 推荐！！！

### 特点

- **交互式与非交互式**：支持命令行直接操作和交互式操作两种模式
- **即时生效**：大多数操作会立即写入磁盘，无需额外确认
- **多种文件系统支持**：ext2/3/4、xfs、btrfs、fat、ntfs 等
- **无损调整**：可以调整分区大小而不丢失数据（需文件系统支持）



### 基本命令格式

```shell
parted [选项] [设备] [命令 [参数]]
```

**常用选项**

| 选项 | 说明                         |
| :--- | :--------------------------- |
| `-l` | 列出所有块设备的分区信息     |
| `-s` | 脚本模式（非交互式）         |
| `-a` | 设置对齐类型（min/opt/none） |
| `-f` | 抑制部分警告信息             |



### 进入交互模式

```shell
sudo parted /dev/sdX
```

进入交互命令行，然后输入子命令操作。



### 查看分区表 print free

先查看硬盘情况。

```shell
print free
```

### 改变单位 unit MiB

```shell
unit MiB
```



### 创建分区表 mklabel

会清空硬盘所有数据！

```shell
(parted) mklabel gpt    # 创建 GPT 分区表，适用于超过2TB的磁盘或现代系统。
(parted) mklabel msdos  # 创建 MBR（msdos）分区表，适用于传统BIOS系统和2TB以下磁盘。
```



### 创建新分区 mkpart

使用 `mkpart` 命令创建新分区。

```shell
(parted) mkpart primary 0% 100%
```

**需要指定**：

- **分区类型**：

    - `primary`（主分区）

    - `extended`（扩展分区）

    - `logical`（逻辑分区）；

    MBR分区表需要区分这些类型，**GPT分区表则通常都使用 `primary`**。

- **文件系统类型**（可选）：

    - `ext4`, `xfs`, `fat32`, `ntfs`, `linux-swap`。

    `parted` 本身不创建文件系统，此类型仅用于设置分区标识。

- **起始点和结束点**：
    - 可以使用 `MB`, `GB`, `TB` 或百分比（如 `100%`）指定；
    - 也可使用 `MiB`, `GiB` 等 IEC 单位以获得更精确的控制。

### 删除分区

```shell
(parted) rm 分区号
```



### 退出 quit

```shell
(parted) quit
```





## 格式化分区

分区创建后（例如 `/dev/sdb1`），需要将其格式化为特定的文件系统。

### 制作文件系统 mkfs

```bash
# 格式化为 EXT4 (最常用的Linux文件系统)
sudo mkfs.ext4 /dev/sdb1

# 格式化为 XFS (高性能文件系统，适用于大文件)
sudo mkfs.xfs /dev/sdb1

# 格式化为 FAT32 (兼容性好，适用于U盘或ESP分区)
sudo mkfs.fat -F 32 /dev/sdb1

# 格式化为 NTFS (用于与Windows共享)
sudo mkfs.ntfs /dev/sdb1

# 使用 Btrfs 或 ZFS 等现代文件系统
sudo mkfs.btrfs /dev/sdb1
```

**注意：格式化会销毁该分区上所有现有数据！**









#### 动挂载 (临时生效，重启后失效)

```bash
# 首先创建一个目录作为挂载点
sudo mkdir /mnt/mydata

# 将分区挂载到该目录
sudo mount /dev/sdb1 /mnt/mydata

# 使用 df -h 检查是否挂载成功
df -h
```

现在你就可以通过 `/mnt/mydata` 目录访问新硬盘的存储空间了。

#### 2. 自动挂载 (永久生效，需配置 `/etc/fstab`)

手动挂载重启后会失效。要实现开机自动挂载，需要编辑 `/etc/fstab` 文件。

1. **获取分区的 UUID** (推荐使用UUID，比设备名更稳定)

    ```bash
    sudo blkid /dev/sdb1
    ```

    输出类似：`/dev/sdb1: UUID="a1b2c3d4-..." TYPE="ext4" PARTUUID="..."`

2. **备份并编辑 fstab 文件**

    ```bash
    sudo cp /etc/fstab /etc/fstab.backup  # 先备份！
    sudo nano /etc/fstab  # 或使用 vim /etc/fstab
    ```

3. **在文件末尾添加一行**

    ```bash
    # <file system>                           <mount point>   <type>  <options>       <dump>  <pass>
    UUID=a1b2c3d4-xxxx-xxxx-xxxx-xxxxxxxxxxxx /mnt/mydata     ext4    defaults        0       2
    ```

    *   **UUID=...**: 替换为你刚才用 `blkid` 查到的 UUID。
    *   **/mnt/mydata**: 挂载点目录必须已存在。
    *   **ext4**: 文件系统类型必须匹配。
    *   **defaults**: 使用默认挂载选项（如读写、执行等）。
    *   **0**: `dump` 备份工具是否使用，0 表示否。
    *   **2**: 系统启动时 `fsck` 磁盘检查的顺序，根目录 `/` 是 1，其他通常是 2。

4. **测试并应用**

    ```bash
    # 测试 fstab 配置是否正确，若无输出则通常表示正常
    sudo mount -a
    
    # 重启验证
    sudo reboot
    ```

---

### 











