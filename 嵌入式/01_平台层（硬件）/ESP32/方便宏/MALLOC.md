这些宏来自 ESP-IDF 的 **heap_caps 堆内存能力（capabilities）API**，不是日志等级。它们主要传给：

```c
heap_caps_malloc(size, MALLOC_CAP_xxx);
heap_caps_calloc(n, size, MALLOC_CAP_xxx);
heap_caps_get_free_size(MALLOC_CAP_xxx);
```

多个能力用 `|` 组合，表示 **必须同时满足**，不是“或”。

---

## 一、逐个解释

| 宏                         | 含义                                                         | 典型用途 / 注意                                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `HEAP_IRAM_ATTR`           | 不是分配能力，而是函数/变量属性宏，把 heap 相关函数放到 IRAM | 让函数在 flash cache 关闭时也能执行，容易和 `MALLOC_CAP_*` 混淆 |
| `MALLOC_CAP_32BIT`         | 内存允许按 32 位对齐访问                                     | 适合 32 位数据；常见于 IRAM；**不保证**字节访问              |
| `MALLOC_CAP_8BIT`          | 内存允许 8/16/... 位访问                                     | 适合字节缓冲区、字符串、普通 malloc；通常 DRAM 支持          |
| `MALLOC_CAP_DMA`           | 内存可被 DMA 访问                                            | DMA 缓冲区常用，通常要求内部内存；常和 `MALLOC_CAP_8BIT` 一起用 |
| `MALLOC_CAP_PID2` ~ `PID7` | 映射到 PID2~PID7 内存空间                                    | 文档明确说 PID 当前未使用，属于预留，普通应用不要用          |
| `MALLOC_CAP_SPIRAM`        | 内存在 SPI RAM / PSRAM 中                                    | 外部大内存，容量大但速度较慢，未必能 DMA                     |
| `MALLOC_CAP_INTERNAL`      | 内存在内部；flash/SPIRAM cache 关闭时不会消失                | 中断、DMA、写 flash 期间可用的内存                           |
| `MALLOC_CAP_DEFAULT`       | 可被默认 `malloc()` / `calloc()` 返回的内存                  | 普通 `malloc` 用的默认堆，通常内部默认内存                   |
| `MALLOC_CAP_IRAM_8BIT`     | 内存在 IRAM，且允许非对齐/字节访问                           | 比较特殊；传统 IRAM 常只支持 32 位访问                       |
| `MALLOC_CAP_RETENTION`     | 可被 retention DMA 访问                                      | 低功耗保持相关，睡眠/保持场景                                |
| `MALLOC_CAP_RTCRAM`        | 内存在 RTC fast memory                                       | 睡眠时可能保留，容量小，适合低功耗保留数据                   |
| `MALLOC_CAP_SPM`           | 内存在 SPM 内存                                              | 芯片特定区域，普通应用很少用，具体看芯片手册                 |
| `MALLOC_CAP_TCM`           | 内存在 TCM，紧耦合内存                                       | 低延迟、实时性要求高的代码/数据；具体看芯片                  |
| `MALLOC_CAP_DMA_DESC_AHB`  | 可存放 AHB DMA 描述符                                        | 不是普通 DMA 数据缓冲区，是描述符内存                        |
| `MALLOC_CAP_DMA_DESC_AXI`  | 可存放 AXI DMA 描述符                                        | 用于 AXI 总线 DMA 描述符                                     |
| `MALLOC_CAP_CACHE_ALIGNED` | 必须对齐到中间缓存的 cache line 大小                         | DMA 缓冲区常用，避免缓存一致性问题                           |
| `MALLOC_CAP_SIMD`          | 可用于 SIMD 指令                                             | 向量/SIMD 数据，通常有额外对齐要求                           |
| `MALLOC_CAP_SPIRAM_NO_ENC` | 在 PSRAM 免 flash 加密区域，PSRAM 中为明文                   | 需配置 `CONFIG_SPIRAM_ENC_EXEMPT`，用于需要明文的场景        |
| `MALLOC_CAP_INVALID`       | 无效能力 / 列表结束标记                                      | **不能用于分配**，只作标记                                   |

---

## 二、怎么区分这些宏？

可以按“用途维度”分：

### 1. 按内存位置分
- `MALLOC_CAP_INTERNAL`：内部内存
- `MALLOC_CAP_SPIRAM`：外部 SPI RAM / PSRAM
- `MALLOC_CAP_RTCRAM`：RTC 快速内存
- `MALLOC_CAP_TCM`：TCM 紧耦合内存
- `MALLOC_CAP_SPM`：SPM 内存
- `MALLOC_CAP_IRAM_8BIT`：IRAM 且支持 8 位访问
- `MALLOC_CAP_SPIRAM_NO_ENC`：PSRAM 免加密区域

### 2. 按访问方式/对齐分
- `MALLOC_CAP_8BIT`：支持字节访问
- `MALLOC_CAP_32BIT`：支持 32 位对齐访问
- `MALLOC_CAP_CACHE_ALIGNED`：按 cache line 对齐
- `MALLOC_CAP_SIMD`：支持 SIMD 特定位访问

### 3. 按 DMA / 外设能力分
- `MALLOC_CAP_DMA`：普通 DMA 可访问
- `MALLOC_CAP_DMA_DESC_AHB`：AHB DMA 描述符
- `MALLOC_CAP_DMA_DESC_AXI`：AXI DMA 描述符
- `MALLOC_CAP_RETENTION`：retention DMA 可访问

### 4. 默认 / 特殊 / 预留
- `MALLOC_CAP_DEFAULT`：默认 malloc 可返回
- `MALLOC_CAP_INVALID`：无效/结束标记
- `MALLOC_CAP_PID2` ~ `PID7`：当前未使用的 PID 空间

### 5. 属性宏
- `HEAP_IRAM_ATTR`：不是分配能力，是 IRAM 函数属性。

---

## 三、常见组合示例

```c
// 普通分配，等价于默认堆
void *p = heap_caps_malloc(1024, MALLOC_CAP_DEFAULT);

// DMA 缓冲区，要求 DMA 可访问且字节可访问
void *dma_buf = heap_caps_malloc(1024, MALLOC_CAP_DMA | MALLOC_CAP_8BIT);

// 中断/cache 关闭时也要能访问，通常在内部内存
void *int_buf = heap_caps_malloc(512, MALLOC_CAP_INTERNAL | MALLOC_CAP_32BIT);

// 大块数据放 PSRAM
void *big = heap_caps_malloc(100 * 1024, MALLOC_CAP_SPIRAM);

// 低功耗保留
void *rtc_buf = heap_caps_malloc(256, MALLOC_CAP_RTCRAM);
```



## 四、关键注意点

1. **组合是“与”关系** 

   `MALLOC_CAP_DMA | MALLOC_CAP_SPIRAM` 表示“既要 DMA 又要 PSRAM”。多数芯片没有这种内存，会分配失败。

2. **互斥能力不要乱组合** 

   例如 `MALLOC_CAP_INTERNAL | MALLOC_CAP_SPIRAM` 通常没有结果。

3. **不是所有芯片都支持所有宏**  

   `TCM`、`SPM`、`RTCRAM`、`DMA_DESC_AXI` 等和具体芯片有关，看目标芯片的 `soc_caps.h` 和 heap 文档。

4. **查询空闲内存**  

   ```c
   size_t free_dma = heap_caps_get_free_size(MALLOC_CAP_DMA);
   size_t free_spiram = heap_caps_get_free_size(MALLOC_CAP_SPIRAM);
   ```

5. **优先尝试多种内存** 

   如果希望“优先 PSRAM，失败再用内部”，可用 `heap_caps_malloc_prefer()` 按优先级传多组能力。

一句话总结： 

`MALLOC_CAP_*` 是“我要一块满足这些条件的内存”；`HEAP_IRAM_ATTR` 是“把这个函数放 IRAM”；`MALLOC_CAP_INVALID` 只是结束标记，不能用来分配。

