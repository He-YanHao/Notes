# WIFI-OTA说明

## ESP32-S3基础说明

| 项目          | 说明                                     |
| :------------ | :--------------------------------------- |
| **芯片型号**  | `esp32c3` - 基于RISC-V架构的ESP32-C3芯片 |
| **CPU核心**   | `1 CPU core(s)` - 单核处理器             |
| **无线功能**  | `WiFi/BLE` - 支持WiFi和蓝牙低功耗        |
| **硅版本**    | `silicon revision v0.4` - 芯片硬件版本   |
| **外部Flash** | `2MB external flash`                     |



## WIFI-OTA核心问题

CSV分区 和 WIFI。



## OTA

需要CSV分区自定义Flash的管理。



### Type字段

Type 字段可以指定为名称或数字 0～254（或者十六进制 0x00-0xFE）。注意，不得使用预留给 ESP-IDF 核心功能的 0x00-0x3F。

- `app` (0x00)，
- `data` (0x01)，
- `bootloader` (0x02)。该分区为可选项且不会影响系统功能，因此默认情况下，该分区不会出现在 ESP-IDF 的任何 CSV 分区表文件中，仅在引导加载程序 OTA 更新和 flash 分区时有用。即使 CSV 文件中没有该分区，仍然可以执行 OTA。
- `partition_table` (0x03)。默认情况下，该分区也不会出现在 ESP-IDF 的任何 CSV 分区表文件中。
- 0x40-0xFE 预留给 **自定义分区类型**。如果你的应用程序需要以 ESP-IDF 尚未支持的格式存储数据，请在 0x40-0xFE 内添加一个自定义分区类型。

#### SubType字段

SubType 字段长度为 8 bit，内容与具体分区 Type 有关。

目前，ESP-IDF 仅仅规定了 `app` 和 `data` 两种分区类型的子类型含义。

参考 `esp_partition_subtype_t` 以了解 ESP-IDF 定义的全部子类型列表，包括：

- 当 Type 定义为 `app` 时，SubType 字段可以指定为 `factory` (0x00)、`ota_0` (0x10) … `ota_15` (0x1F) 或 `test` (0x20)。

- 当 Type 定义为 `bootloader` 时，可以将 SubType 字段指定为：

    -   `primary` (0x00)，即二级引导加载程序，位于 flash 的 0x1000 地址处。工具会自动确定此子类型的适当大小和偏移量，因此为此子类型指定的任何大小或偏移量将被忽略。你可以将这些字段留空或使用 `N/A` 作为占位符。

    -   `ota` (0x01)，是一个临时的引导加载程序分区，在 OTA 更新期间可用于下载新的引导加载程序镜像。工具会忽略此子类型的大小，你可以将其留空或使用 `N/A`。你只能指定一个偏移量，或者将其留空，工具将根据先前使用的分区的偏移量进行计算。

    -   `recovery` (0x02)，这是用于安全执行引导加载程序 OTA 更新的恢复引导加载程序分区。`gen_esp32part.py` 工具会自动确定该分区的地址和大小，因此可以将这些字段留空或使用 `N/A` 作为占位符。该分区地址必须与 Kconfig 选项定义的 eFuse 字段相匹配。如果正常的引导加载程序加载路径失败，则一级 (ROM) 引导加载程序会尝试加载 eFuse 字段指定地址的恢复分区。

    -   引导加载程序类型的大小由 `gen_esp32part.py` 工具根据指定的 `--offset` （分区表偏移量）和 `--primary-partition-offset` 参数计算得出。具体来说，引导加载程序的大小定义为 ([CONFIG_PARTITION_TABLE_OFFSET](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/kconfig-reference.html#config-partition-table-offset) - 0x1000)。此计算得出的大小适用于引导加载程序的所有子类型。

- 当 Type 定义为 `partition_table` 时，可以将 SubType 字段指定为：

    -   `primary` (0x00)，是主分区表，位于 flash 的 [CONFIG_PARTITION_TABLE_OFFSET](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/kconfig-reference.html#config-partition-table-offset) 地址处。工具会自动确定此子类型的适当大小和偏移量，因此为此子类型指定的任何大小或偏移量将被忽略。你可以将这些字段留空或使用 `N/A` 作为占位符。

    -   `ota` (0x01)，是一个临时的分区表分区，在 OTA 更新期间可用于下载新的分区表镜像。工具会忽略此子类型的大小，你可以将其留空或使用 `N/A`。你可以指定一个偏移量，或者将其留空，工具将根据先前分配的分区的偏移量进行计算。

    -   `partition_table` 的大小固定为 `0x1000`，适用于 `partition_table` 的所有子类型。

- 当 Type 定义为 `data` 时，SubType 字段可以指定为 `ota` (0x00)、`phy` (0x01)、`nvs` (0x02)、`nvs_keys` (0x04) 或者其他组件特定的子类型。

    `ota` (0) 即OTA 数据分区 ，用于存储当前所选的 OTA 应用程序的信息。这个分区的大小需要设定为 0x2000。

    `phy` (1) 分区用于存放 PHY 初始化数据，从而保证可以为每个设备单独配置 PHY，而非必须采用固件中的统一 PHY 初始化数据。

    默认配置下，phy 分区并不启用，而是直接将 phy 初始化数据编译至应用程序中，从而节省分区表空间（直接将此分区删掉）。

    如果需要从此分区加载 phy 初始化数据，请打开项目配置菜单（`idf.py menuconfig`），并且使能 [CONFIG_ESP_PHY_INIT_DATA_IN_PARTITION](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/kconfig-reference.html#config-esp-phy-init-data-in-partition) 选项。此时，还需要手动将 phy 初始化数据烧至设备 flash（esp-idf 编译系统并不会自动完成该操作）。

    `nvs` (2) 是专门给 [非易失性存储 (NVS) API](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/storage/nvs_flash.html) 使用的分区。

    用于存储每台设备的 PHY 校准数据（注意，并不是 PHY 初始化数据）。

    用于存储 Wi-Fi 数据（如果使用了 [esp_wifi_set_storage(WIFI_STORAGE_FLASH)](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/network/esp_wifi.html) 初始化函数）。

    NVS API 还可以用于其他应用程序数据。

    强烈建议为 NVS 分区分配至少 0x3000 字节空间。

    如果使用 NVS API 存储大量数据，请增加 NVS 分区的大小（默认是 0x6000 字节）。

    当 NVS 用于存储出厂设置时，建议将这些设置保存在单独的只读 NVS 分区中。只读 NVS 分区最小为 0x1000 字节。有关更多详情，请参阅 [只读 NVS](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/storage/nvs_flash.html#read-only-nvs) 了解详情。ESP-IDF 提供了 [NVS 分区生成工具](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/storage/nvs_partition_gen.html)，能够生成包含出厂设置的 NVS 分区，并与应用程序一起烧录。

    `nvs_keys` (4) 是 NVS 秘钥分区。详细信息，请参考 [非易失性存储 (NVS) API](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/storage/nvs_flash.html) 文档。

    用于存储加密密钥（如果启用了 NVS 加密 功能）。

    此分区应至少设定为 4096 字节。

- 如果分区类型是由应用程序定义的任意值 (0x40-0xFE)，那么 `subtype` 字段可以是由应用程序选择的任何值 (0x00-0xFE)。

    请注意，如果用 C++ 编写，应用程序定义的子类型值需要转换为 [`esp_partition_type_t`](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/storage/partition.html#_CPPv420esp_partition_type_t)，从而与 [分区 API](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/api-reference/storage/partition.html#api-reference-partition-table) 一起使用。

#### 偏移地址 (Offset) 和大小 (Size) 字段

偏移地址表示 SPI flash 中的分区地址，扇区大小为 0x1000 (4 KB)。因此，偏移地址必须是 4 KB 的倍数。

- 若 CSV 文件中的分区偏移地址为空，则该分区会接在前一个分区之后；若为首个分区，则将接在分区表之后。
- `app` 分区的偏移地址必须与 0x10000 (64 KB) 对齐。如果偏移字段留空，则 `gen_esp32part.py` 工具会自动计算得到一个满足对齐要求的偏移地址。如果 `app` 分区的偏移地址没有与 0x10000 (64 KB) 对齐，则该工具会报错。
- `app` 分区的大小必须与 flash 扇区大小对齐。为 `app` 分区指定未对齐的大小将返回错误。
- 若启用了 Secure Boot V1，则 `app` 分区的大小需与 0x10000 (64 KB) 对齐。
- `bootloader` 的偏移量和大小不受 Secure Boot V1 选项的影响。无论是否启用 Secure Boot V1，引导加载程序的大小保持不变，并且不包括安全摘要，安全摘要位于 flash 的 0x0 偏移地址处，占用一个扇区（4096 字节）。
- `app` 分区的大小和偏移地址可以采用十进制数或是以 0x 为前缀的十六进制数，且支持 K 或 M 的倍数单位（K 和 M 分别代表 1024 和 1024*1024 字节）。
- 对于 `bootloader` 和 `partition_table`，在 CSV 文件中将大小和偏移量指定为 `N/A` 意味着这些值将由工具自动确定，无法手动定义。这需要设置 `gen_esp32part.py` 工具的 `--offset` 和 `--primary-partition-offset` 参数。

#### Flags 字段

目前支持 `encrypted` 和 `readonly` 标记：

- 如果 Flags 字段设置为 `encrypted`，且已启用 flash 加密功能，则该分区将会被加密。

- 如果 Flags 字段设置为 `readonly`，则该分区为只读分区。

    `readonly` 标记仅支持除 `ota` 和 `coredump` 子类型外的 `data` 分区。

    使用该标记，防止意外写入如出厂数据分区等包含关键设备特定配置数据的分区。



## 

### 针对ESP32-S3

```
# Name,   Type, SubType, Offset,   Size, Flags
# 分区名称  类型  子类      偏移      大小   标记
nvs,      data, nvs,     0x9000,   0x4000,
otadata,  data, ota,     0xd000,   0x2000,
phy_init, data, phy,     0xf000,   0x1000,
factory,  app,  factory, 0x10000,  512K,
ota_0,    app,  ota_0,   ,         512K,
ota_1,    app,  ota_1,   ,         512K,
```



## WIFI



### 蓝本 https_request

**这个案例提供了：**

1. **完整的HTTPS客户端实现**
2. **证书验证机制**（安全下载必备）
3. **分块数据传输处理**
4. **错误处理机制**
5. **易于修改为固件下载**



### https

#### ESP32 使用https证书的方式

内置证书：证书已经内置在ESP32的固件中，无需单独管理证书，可以直接使用。这种方式比较简单，适用于使用不频繁的HTTPS请求。



### 乐鑫官网

一般来说，要编写自己的 Wi-Fi 应用程序，最高效的方式是先选择一个相似的应用程序示例，然后将其中可用的部分移植到自己的项目中。

如果你希望编写一个强健的 Wi-Fi 应用程序，强烈建议在开始之前先阅读本文。**非强制要求，请依个人情况而定。**

#### 整体介绍

`esp_http_client` 提供了一组 API，用于从 ESP-IDF 应用程序中发起 HTTP/S 请求，具体的使用步骤如下：

- 首先调用 [`esp_http_client_init()`](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/protocols/esp_http_client.html#_CPPv420esp_http_client_initPK24esp_http_client_config_t)，创建一个 [`esp_http_client_handle_t`](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/protocols/esp_http_client.html#_CPPv424esp_http_client_handle_t) 实例，即基于给定的 [`esp_http_client_config_t`](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/protocols/esp_http_client.html#_CPPv424esp_http_client_config_t) 配置创建 HTTP 客户端句柄。此函数必须第一个被调用。若用户未明确定义参数的配置值，则使用默认值。
- 其次调用 [`esp_http_client_perform()`](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/protocols/esp_http_client.html#_CPPv423esp_http_client_perform24esp_http_client_handle_t)，执行 `esp_http_client` 的所有操作，包括打开连接、交换数据、关闭连接（如需要），同时在当前任务完成前阻塞该任务。所有相关的事件（在 [`esp_http_client_config_t`](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/protocols/esp_http_client.html#_CPPv424esp_http_client_config_t) 中指定）将通过事件处理程序被调用。
- 最后调用 [`esp_http_client_cleanup()`](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/protocols/esp_http_client.html#_CPPv423esp_http_client_cleanup24esp_http_client_handle_t) 来关闭连接（如有），并释放所有分配给 HTTP 客户端实例的内存。此函数必须在操作完成后最后一个被调用。

















