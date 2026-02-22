## dd

## 基础介绍

`dd` 是 Linux 和 Unix 系统中的一个非常强大且经典的命令行工具。它的名字源于 **“Data Duplicator”** 或 **“Convert and Copy”**。

它的核心功能是**按块（block）从输入文件读取数据，并按块写入到输出文件**。由于其底层和直接的数据操作方式，它被称为 **“磁盘毁灭者”（Disk Destroyer）** 或 **“数据销毁者”（Data Destroyer）**，因为一旦使用不当，后果非常严重。

`dd` 不像普通复制命令那样按文件操作，而是**按“块”来搬运原始数据**。你可以把“块”想象成搬运数据的“勺子”的大小。

*   `if=input.file`：指定输入的“数据池”（Input File）。
*   `of=output.file`：指定输出的“数据池”（Output File）。
*   `bs=BYTES`：设置“勺子”的大小（Block Size）。一次读这么多，一次也写这么多。
*   `count=N`：只用“勺子”舀 `N` 次。
*   `skip=N`：从输入文件开头跳过 `N` 个“块”开始读。
*   `seek=N`：从输出文件开头跳过 `N` 个“块”开始写。

## 基本语法

```bash
dd if=<输入文件> of=<输出文件> [选项参数]
```

**重要警告**：**务必双重甚至三重检查 `if` (源) 和 `of` (目标) 参数！** 写错目标盘符会导致不可逆的数据丢失。



## 常用选项（参数）

| 选项           | 含义                   | 说明与示例                                                   |
| :------------- | :--------------------- | :----------------------------------------------------------- |
| `if=FILE`      | 输入文件 (input file)  | 默认为标准输入 (`stdin`)。可以是设备文件（如 `/dev/sda`）或普通文件。 |
| `of=FILE`      | 输出文件 (output file) | 默认为标准输出 (`stdout`)。可以是设备文件或普通文件。        |
| `bs=BYTES`     | 块大小 (block size)    | **一次读写**的字节数。这是 `ibs` 和 `obs` 的结合。这是**性能调优的关键参数**。常用单位：`512`（字节）, `4K`（4096字节）, `1M`（1048576字节）。`bs=4K` |
| `ibs=BYTES`    | 输入块大小             | 一次**读取**的字节数。                                       |
| `obs=BYTES`    | 输出块大小             | 一次**写入**的字节数。                                       |
| `count=N`      | 复制块的数量           | 只复制 `N` 个输入块后停止。`count=1000`                      |
| `skip=N`       | 跳过输入块             | 从输入文件开头跳过 `N` 个块后再开始读取。`skip=1`            |
| `seek=N`       | 跳过输出块             | 从输出文件开头跳过 `N` 个块后再开始写入。`seek=1`            |
| `status=LEVEL` | 状态信息级别           | **控制输出信息**。`progress` 显示传输进度（**非常有用**），`noxfer` 不显示最后的统计信息。`status=progress` |
| `conv=CONVS`   | 转换参数               | 用逗号分隔的转换参数列表。如 `notrunc`（不截断输出文件）, `noerror`（读取错误时继续）, `sync`（用0填充输入块）。`conv=noerror,sync` |



## 常见用途示例

### 创建磁盘/分区的完整镜像（备份）
这是最经典的用法。将整个磁盘 `/dev/sdc` 备份到一个名为 `disk_backup.img` 的镜像文件中。
```bash
sudo dd if=/dev/sdc of=./disk_backup.img bs=4M status=progress
```
*   `bs=4M`：使用 4MB 的块大小，可以显著提高大文件复制速度。
*   `status=progress`：显示实时传输速度和进度。

### 将镜像文件恢复到磁盘（还原）
将 `disk_backup.img` 镜像文件还原到磁盘 `/dev/sdc`。
```bash
sudo dd if=./disk_backup.img of=/dev/sdc bs=4M status=progress
```

### 制作启动U盘
将下载的 ISO 系统镜像（如 Ubuntu）写入 U 盘（假设U盘是 `/dev/sdX`）。
```bash
sudo dd if=./ubuntu-22.04.iso of=/dev/sdX bs=4M status=progress oflag=sync
```
*   `oflag=sync`：使用同步写入方式，确保所有数据都真正写入硬件后才返回，保证数据完整性。

### 测试磁盘读写速度
可以用来测试存储设备的原始顺序读写性能。
```bash
# 测试写入速度（写入一个 1GB 的空文件，完成后自动删除）
dd if=/dev/zero of=./testfile bs=1G count=1 oflag=direct status=progress

# 测试读取速度（读取刚才的文件）
dd if=./testfile of=/dev/null bs=1G count=1 iflag=direct status=progress
```
*   `oflag=direct` / `iflag=direct`：绕过系统缓存，直接对磁盘进行读写，得到真实的磁盘速度。

### 备份磁盘的MBR（主引导记录）
MBR 位于磁盘最开始的 512 字节。
```bash
sudo dd if=/dev/sda of=./mbr_backup.bak bs=512 count=1
```

### 安全擦除磁盘数据
用随机数据或零填充整个磁盘，确保数据无法恢复。
```bash
# 用零填充（一次）
sudo dd if=/dev/zero of=/dev/sdX bs=4M status=progress

# 用随机数据填充（更安全）
sudo dd if=/dev/urandom of=/dev/sdX bs=4M status=progress
```

### 修改文件的一部分
跳过输出文件的前 1MB（`seek=1M`），然后从输入文件读取 128KB 的数据写入。
```bash
dd if=./patch.data of=./largefile.bin bs=1K seek=1024 count=128 conv=notrunc
```
*   `conv=notrunc`：至关重要！确保只修改文件的一部分，而**不截断**（ truncate ）原文件的剩余内容。



## 最佳实践与警告

1.  **三思而后行**：`dd` 没有确认对话框。命令一旦执行，数据立即被覆盖。**永远确认 `if` 和 `of` 参数是否正确**。
2.  **先卸载**：在对磁盘分区进行操作前，最好先卸载 (`umount`) 它，以确保数据一致性。
3.  **使用 `status=progress`**：对于长时间操作，这个选项能让你看到进度，安心很多。
4.  **调整 `bs`**：增大块大小（如 `4M`, `64K`）通常能显著提高传输效率。但并非越大越好，可以尝试不同值来找到最佳性能点。
5.  **数据验证**：做完镜像或恢复后，可以使用 `md5sum` 或 `sha256sum` 来验证源和目标是否完全一致。
    ```bash
    sudo dd if=/dev/sdc | md5sum
    # 然后与镜像文件的校验和对比
    md5sum ./disk_backup.img
    ```
