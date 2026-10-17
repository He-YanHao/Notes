# ESP32-P4 硬件 JPEG 解码器与 LVGL 集成笔记

## 1. 硬件 JPEG 解码器简介

ESP32-P4 内置**硬件 JPEG 编解码器**，基于 JPEG Baseline 标准，通过**纯硬件状态机**实现，使用 DMA 传输 JPEG 数据流，可最大限度降低 CPU 占用。

**核心特性**：

- 支持 JPEG 解码（JPG → RGB565/RGB888）和编码（RGB → JPG）
- 同一时刻编解码器只能作为**编码器**或**解码器**之一工作
- 输出格式支持 RGB565 和 RGB888，RGB 顺序可配置为 BGR 或 RGB
- 解码性能可达 **1080P@40fps**
- 通过 DMA 直接访问 PSRAM，需要关注缓存一致性

## 2. 关键软件组件

### 2.1 `esp_lv_decoder`（解码器组件）

Espressif 提供的 LVGL 图像解码器组件，支持多种格式，核心能力如下：

| 格式     | 说明                  | 芯片支持         | 内存占用 | 解码速度   |
| -------- | --------------------- | ---------------- | -------- | ---------- |
| **JPG**  | 标准 JPEG             | ESP32-S3/S31/P4  | 中       | 快         |
| **PJPG** | PNG-JPEG（硬件加速）  | **ESP32-P4/S31** | 低       | **非常快** |
| **SJPG** | 分条 JPEG（16行条带） | 所有芯片         | 极低     | 快         |
| SPNG     | 分条 PNG              | 所有芯片         | 极低     | 中         |
| QOI      | Quite OK Image        | 所有芯片         | 低       | 非常快     |

**PJPG 是 ESP32-P4 硬件解码的核心格式**，相比软件解码**速度大幅提升，CPU 占用更低**。标准 JPG 在 ESP32-P4 上使用硬件路径的条件是：**宽和高均为 16 的倍数，且至少 64 像素**。渐进式 JPEG 可能不被硬件支持。

### 2.2 `esp_lvgl_adapter`（适配器组件）

Espressif 推荐的 LVGL 集成组件，已深度优化屏幕撕裂和帧率，并**无缝集成了图像解码、FreeType 字体渲染和 Dummy Draw 等高级功能**。它统一了显示注册、撕裂控制、线程安全 LVGL 访问，并可选集成文件系统和图像解码器。

## 3. 配置与集成步骤

### 3.1 添加依赖

```bash
idf.py add-dependency esp_lvgl_adapter
idf.py add-dependency esp_lv_decoder
```

### 3.2 启用 PSRAM 与调整分区表

硬件 JPEG 解码器的输入/输出缓冲区均从 PSRAM 堆中分配，必须启用 PSRAM 支持。同时，LVGL 和 JPEG 解码器的内存占用较大，建议修改分区表扩大程序存储区（如 15MB）并确保 Flash 容量匹配（如 16MB）。

```
nvs,      data, nvs,      , 0x6000,
phy_init, data, phy,      , 0x1000,
factory,  app,  factory,  , 15M,
```

### 3.3 配置图像资源转换

使用 `esp_mmap_assets` 组件在编译时自动将图像转换为目标格式。以 JPG → PJPG（硬件加速）为例：

```cmake
spiffs_create_partition_assets(
    my_spiffs_partition
    my_folder
    FLASH_IN_PROJECT
    MMAP_FILE_SUPPORT_FORMAT ".jpg"
    MMAP_SUPPORT_PJPG
)
```

编译后输出日志会显示转换结果，如 `navi_52.jpg -> navi_52.pjpg`。

### 3.4 LVGL 中加载图像

在 LVGL 中使用标准图像控件加载 PJPG 格式图像，解码由 `esp_lv_decoder` 在后台自动完成，无需手动调用 JPEG 硬件接口。

## 4. 直接使用硬件 JPEG 解码器（底层方式）

若不使用 `esp_lv_decoder`，也可直接调用 ESP-IDF 的 JPEG 驱动，核心流程如下：

### 4.1 初始化解码器引擎

```c
jpeg_decode_engine_cfg_t cfg = {
    .intr_priority = 0,
    .timeout_ms = 40,   // 大图可适当延长
};
jpeg_decoder_handle_t handle = NULL;
ESP_ERROR_CHECK(jpeg_new_decoder_engine(&cfg, &handle));
```


### 4.2 分配 DMA 对齐缓冲区

使用 `jpeg_alloc_decoder_mem()` 分配输入/输出缓冲区，确保满足 DMA 对齐要求：

```c
jpeg_decode_memory_alloc_cfg_t input_cfg  = { .buffer_direction = JPEG_DEC_ALLOC_INPUT_BUFFER  };
jpeg_decode_memory_alloc_cfg_t output_cfg = { .buffer_direction = JPEG_DEC_ALLOC_OUTPUT_BUFFER };

uint8_t *in_buf  = jpeg_alloc_decoder_mem(jpeg_size, &input_cfg,  &in_size);
uint8_t *out_buf = jpeg_alloc_decoder_mem(width * height * 2, &output_cfg, &out_size);
```

### 4.3 配置解码参数并执行

```c
jpeg_decode_cfg_t dec_cfg = {
    .rgb_order     = JPEG_DEC_RGB_ELEMENT_ORDER_BGR,  // 若颜色失真则改为 RGB
    .output_format = JPEG_DECODE_OUT_FORMAT_RGB565,   // 或 RGB888
};

memcpy(in_buf, jpeg_data, jpeg_size);
jpeg_decoder_process(handle, &dec_cfg, in_buf, jpeg_size, out_buf, out_size, &out_size);
free(in_buf);
// out_buf 即为解码后的 RGB 数据，可直接用于显示
```


## 5. 关键问题：Cache 同步与随机黑线

### 5.1 问题现象与原因

使用硬件 JPEG 解码器时，画面**随机出现黑线**。原因是：**CPU 将数据读入 Cache，而硬件 JPEG 解码器直接从 PSRAM 读取数据**。当 Cache 未及时同步时，硬件读到的数据与 CPU 写入的数据不一致，导致黑线。

### 5.2 三步解决方案

| 步骤               | 操作                                            | 调用                                                         |
| ------------------ | ----------------------------------------------- | ------------------------------------------------------------ |
| **① 输入同步**     | 解码前，确保原始 JPEG 数据已从 Cache 写回 PSRAM | `safe_cache_sync(jpeg_data, jpeg_size, ESP_CACHE_MSYNC_FLAG_DIR_M2C)` |
| **② 输出缓冲准备** | 清除旧 Cache 数据，防止硬件读到过期像素         | `safe_cache_sync(out_buf, out_size, ESP_CACHE_MSYNC_FLAG_DIR_M2C \| ESP_CACHE_MSYNC_FLAG_INVALIDATE)` |
| **③ 结果同步**     | 解码完成后，同步结果数据                        | 解码后再执行一次 Cache 同步                                  |



ESP-IDF 后续版本已包含针对此问题的修复（如“Fixed random black line appearance while jpeg decode”），**建议升级至包含该修复的版本**。

## 6. 与 LVGL 显示管线的协同

解码得到的 RGB 数据需要最终显示到 MIPI 屏幕上，流程如下：

**JPEG 文件 → 硬件解码器 → RGB 数据（PSRAM） → LVGL 图像控件 → PPA/DMA2D → MIPI DSI 屏幕**

- **LVGL 集成方式**：通过 `esp_lv_decoder` 注册解码器后，LVGL 的图像控件（`lv_image`）可直接加载 PJPG 文件，解码过程对上层透明。
- **格式选择**：解码输出格式应与 LVGL 渲染格式和屏幕格式保持一致（如 RGB888 或 RGB565），避免额外的格式转换开销。
- **内存对齐**：LVGL 绘制缓冲区需 64 字节对齐（`CONFIG_LV_DRAW_BUF_ALIGN=64`），硬件 JPEG 解码器的输入/输出缓冲区也需满足 DMA 对齐要求。

## 7. 性能优化建议

1. **优先使用 PJPG 格式**：这是 ESP32-P4 上唯一使用硬件 JPEG 解码加速的格式，速度远超软件解码，CPU 占用最低。
2. **内存受限时使用 SJPG/SPNG**：分条格式按 16 行条带解码，RAM 占用减少约 **40 倍**，适合 PSRAM 紧张的场景，但有轻微性能损失。
3. **使用 MMAP 方式加载资源**：相比 `.c` 数组嵌入，MMAP 方式 Flash 占用低（压缩二进制形式），解码效率好。
4. **合理设置超时**：解码引擎的超时值（`timeout_ms`）建议根据最大图像尺寸设置，大图适当延长。
5. **RGB 顺序验证**：若显示颜色失真但轮廓可见，说明 RGB 顺序配置错误，将 `JPEG_DEC_RGB_ELEMENT_ORDER_BGR` 改为 `RGB` 即可。

## 8. 参考示例与资源

| 资源                     | 说明                                   |
| ------------------------ | -------------------------------------- |
| `lvgl_decode_image` 示例 | 官方多格式解码演示，包含 PJPG 硬件加速 |
| `lvgl_common_demo` 示例  | 统一 LVGL 演示，支持多种 LCD 接口      |
| `esp_lv_decoder` 组件    | 图像解码器组件                         |
| `esp_lvgl_adapter` 组件  | LVGL 适配器，集成解码、字体、显示优化  |
| ESP-IDF JPEG 驱动文档    | `jpeg_decoder_process()` 等 API 参考   |

**推荐配置组合**：

- `CONFIG_LV_USE_PPA=y`
- `CONFIG_LV_DRAW_BUF_ALIGN=64`
- `CONFIG_SPIRAM_SPEED_200M=y`
- 图像格式：**PJPG**（硬件加速）
- 资源加载：**MMAP + 分区表**
- Cache 同步：**解码前后各执行一次 `safe_cache_sync`**

