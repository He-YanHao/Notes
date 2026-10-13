# ESP-IDF VFS 笔记

## VFS 是什么

VFS 全称 Virtual Filesystem Component，中文叫虚拟文件系统组件。

它是 ESP-IDF 提供的一个中间层。

作用：

- 把不同的文件系统和设备驱动统一起来
- 上层应用可以用标准 C 库函数访问文件
- 上层应用也可以用 POSIX 风格函数访问文件
- 下层可以是 FATFS、SPIFFS、LittleFS、SD 卡、串口、socket 等

VFS 本身不实现具体读写。

它根据路径找到对应驱动，然后把调用转发给驱动。

## 为什么需要 VFS

- 统一接口。不同文件系统 API 不同。VFS 把它们统一成 `open`、`read`、`write`、`close`，以及 `fopen`、`fread`、`fwrite`、`fclose`。
- 方便移植。换文件系统时，上层代码基本不用改。
- 路径隔离。不同存储可以挂到不同路径。
- 支持多种存储。SPI Flash、SD 卡、外部 RAM 等都可以接入。
- 方便中间件。LVGL 等中间件可以通过 POSIX 接口访问文件。

## VFS 核心思想

核心是路径前缀匹配。

每个注册到 VFS 的驱动都有一个挂载点。挂载点就是路径前缀，例如 `/storage`。

应用访问：

```text
/storage/0000.jpg
```

VFS 匹配到 `/storage` 前缀。

然后把剩余路径 `0000.jpg` 和操作转发给 FATFS 驱动。

驱动再做实际读写。

## VFS 层次

```text
应用层
C 库和 POSIX 函数
VFS 组件
具体文件系统驱动
物理存储或设备
```

## 关键概念

- 挂载点：驱动注册时指定的路径前缀，例如 `/storage`
- 驱动：实现 `open`、`close`、`read`、`write`、`lseek` 等函数的模块
- 文件描述符：`open` 成功返回整数 fd，`read`、`write`、`close` 用 fd 找到对应驱动
- 文件操作结构体：`esp_vfs_fs_ops_t`，里面是函数指针
- 目录操作结构体：`esp_vfs_dir_ops_t`，用于 `opendir`、`readdir`、`closedir`
- 注册：把驱动加入 VFS
- 挂载：把具体存储和驱动绑定
- 静态注册：使用 `ESP_VFS_FLAG_STATIC`，VFS 直接引用结构体，效率高，结构体必须长期有效
- 动态注册：使用 `ESP_VFS_FLAG_DEFAULT`，VFS 复制结构体，适用于局部变量

## 注册文件系统驱动

一般流程：

- 定义文件操作函数，例如 `open`、`close`、`read`、`write`、`lseek`、`fstat`
- 定义目录操作函数，例如 `opendir`、`readdir`、`closedir`
- 填充 `esp_vfs_fs_ops_t` 结构体
- 调用 `esp_vfs_register_fs` 注册
- 注册成功后路径前缀生效
- 卸载用 `esp_vfs_unregister_fs`

示例结构：

```c
static const esp_vfs_fs_ops_t myfs = {
    .open = &myfs_open,
    .close = &myfs_close,
    .read = &myfs_read,
    .write = &myfs_write,
};

ESP_ERROR_CHECK(esp_vfs_register_fs("/data", &myfs, ESP_VFS_FLAG_STATIC, NULL));
```

## 挂载文件系统

对于 FATFS，有封装函数。

只读挂载 SPI Flash FATFS：

```c
esp_vfs_fat_spiflash_mount_ro(mount_point, partition_label, &mount_config);
```

读写挂载并带磨损均衡：

```c
esp_vfs_fat_spiflash_mount_rw_wl(mount_point, partition_label, &mount_config);
```

挂载参数包括：

- 挂载点
- 分区标签
- 配置

配置包括：

- `format_if_mount_failed`
- `max_files`
- `allocation_unit_size`

挂载成功后可以用 `/storage/文件名` 访问。

卸载用：

- `esp_vfs_fat_spiflash_unmount_ro`
- `esp_vfs_fat_spiflash_unmount_rw_wl`

## 使用 VFS

C 标准库函数：

- `fopen`
- `fread`
- `fwrite`
- `fclose`
- `fseek`
- `ftell`

POSIX 函数：

- `open`
- `read`
- `write`
- `close`
- `lseek`
- `fstat`

路径以挂载点开头，例如：

```text
/storage/test.txt
```

打开模式：

- 只读
- 只写
- 读写
- 创建
- 追加
- 截断

目录操作：

- `opendir`
- `readdir`
- `closedir`

文件状态：

- `stat`
- `fstat`

## VFS 与 FATFS 的关系

FATFS 是具体文件系统。

FATFS 提供：

- `f_open`
- `f_read`
- `f_write`
- `f_close`

VFS 是上层抽象。

VFS 提供：

- `open`
- `read`
- `write`
- `close`

ESP-IDF FATFS 组件把两者连接起来。

挂载函数内部会注册 FATFS 驱动，然后调用 `f_mount`。

应用不用直接调用 `f_open`，通过 VFS 即可。

## VFS 与 LVGL 的关系

LVGL 有自己的文件系统抽象层 `lv_fs`。

LVGL 通过盘符区分不同驱动。

配置：

```c
#define LV_USE_FS_POSIX 1

#if LV_USE_FS_POSIX
    #define LV_FS_POSIX_LETTER 'S'
    #define LV_FS_POSIX_PATH "/storage"
    #define LV_FS_POSIX_CACHE_SIZE 0
#endif
```

含义：

- `LV_USE_FS_POSIX` 为 1，启用 POSIX 驱动
- `LV_FS_POSIX_LETTER` 为 `S`，盘符为 `S:`
- `LV_FS_POSIX_PATH` 为 `/storage`，路径前缀为 `/storage`
- `LV_FS_POSIX_CACHE_SIZE` 为 0，不额外缓存

LVGL 路径：

```text
S:/0000.jpg
```

会转成：

```text
/storage/0000.jpg
```

然后调用 `open`、`read`、`close`。

最终由 ESP-IDF VFS 转发到 FATFS。

## 当前工程示例说明

当前工程使用 SPI Flash 上的只读 FATFS。

挂载点是 `/storage`。

分区标签是 `storage`。

挂载函数是：

```c
esp_vfs_fat_spiflash_mount_ro("/storage", "storage", &mount_config);
```

配置：

```c
.format_if_mount_failed = false,
.max_files = 5,
.allocation_unit_size = 0,
```

LVGL 使用 POSIX 驱动。

盘符是 `S:`。

路径前缀是 `/storage`。

代码中：

```c
lv_img_set_src(img, "S:/0000.jpg");
```

实际访问：

```text
/storage/0000.jpg
```

最终读取 SPI Flash 的 `storage` 分区。

注意当前代码：

```c
const int num_files = 82;
```

注释写的是：

```text
0000.sjpg 到 0135.sjpg
```

实际路径用的是 `.jpg`。

需要根据真实文件修改数量和扩展名。

## 路径映射示例

```text
LVGL 路径：S:/0000.jpg
POSIX 路径：/storage/0000.jpg
FATFS 路径：0:/0000.jpg
物理位置：SPI Flash storage 分区
```

## 常用 API 记录

注册和卸载：

- `esp_vfs_register_fs`
- `esp_vfs_unregister_fs`
- `esp_vfs_fat_register`
- `esp_vfs_fat_unregister`

挂载和卸载：

- `esp_vfs_fat_spiflash_mount_ro`
- `esp_vfs_fat_spiflash_mount_rw_wl`
- `esp_vfs_fat_spiflash_unmount_ro`
- `esp_vfs_fat_spiflash_unmount_rw_wl`

文件操作：

- `open`、`read`、`write`、`close`
- `fopen`、`fread`、`fwrite`、`fclose`

## 高级特性

- eventfd。VFS 提供事件文件描述符，可用于 select 事件循环。行为类似 Linux eventfd。默认不支持 `EFD_SEMAPHORE`。
- select 多路复用。驱动可以实现 `start_select`、`end_select`，用于 socket、串口等。
- 文件描述符表。VFS 维护 fd 到驱动的映射。`open` 返回 fd。`read`、`write`、`close` 用 fd 找到驱动。
- 标准输入输出错误。0、1、2 通常保留。文件描述符从 3 开始。

## 注意事项

- 挂载点不要冲突
- 路径大小写可能敏感
- 只读挂载不能写
- 最大打开文件数有限制
- 注册的驱动结构体生命周期要正确
- 卸载前关闭文件
- 多任务访问要注意同步
- 文件系统损坏时只读挂载不会自动格式化
- 分区标签要和分区表一致
- LVGL 盘符和路径前缀要匹配
- LVGL 缓存大小按需设置
- 图片格式要符合 LVGL 支持

## 总结

VFS 是 ESP-IDF 的虚拟文件系统中间层。

它通过路径前缀匹配和调用转发，把上层标准文件操作和下层具体文件系统连接起来。

VFS 让应用可以使用统一接口访问不同存储。

理解 VFS 对使用 FATFS、LVGL 图片显示、SD 卡、SPIFFS 等都很重要。
