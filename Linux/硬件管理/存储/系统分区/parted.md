# parted

## 特点

- **交互式与非交互式**：支持命令行直接操作和交互式操作两种模式
- **即时生效**：大多数操作会立即写入磁盘，无需额外确认
- **多种文件系统支持**：ext2/3/4、xfs、btrfs、fat、ntfs 等
- **无损调整**：可以调整分区大小而不丢失数据（需文件系统支持）



## 基本命令格式

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



## 进入交互模式

```shell
sudo parted /dev/sdX
```

进入交互命令行，然后输入子命令操作。



## 查看分区表 print free

先查看硬盘情况。

```shell
print free
```

## 改变单位 unit MiB

```shell
unit MiB
```



## 创建分区表 mklabel

会清空硬盘所有数据！

```shell
(parted) mklabel gpt    # 创建 GPT 分区表，适用于超过2TB的磁盘或现代系统。
(parted) mklabel msdos  # 创建 MBR（msdos）分区表，适用于传统BIOS系统和2TB以下磁盘。
```



## 创建新分区 mkpart

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

## 删除分区

```shell
(parted) rm 分区号
```



## 退出 quit

```shell
(parted) quit
```


