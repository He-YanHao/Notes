# 心跳任务heartbeat

创建FreeRTOS任务heartbeat，每秒钟允许一次。

任务内容包括：

- 统计堆空间，若堆空间剩余不足20%，发出警告日志，并改变一些配置策略。
- 计算历史剩余最小堆空间，若减小则发出日志，而非警告。



存在问题：

- 心跳任务两次执行的间隙内，若某个函数申请并释放了大量内存，则无法捕捉到。

问题解决方案：
````
你的分析很准确：心跳任务每秒执行一次，只能捕捉到**那一刻**的堆空间状态。如果某个函数在两次心跳之间申请了大量内存并很快释放，堆空间可能瞬间降到很低（比如只剩 5%），但心跳检查时又恢复到了正常水平（比如 40%），这样你就**无法发现**这个潜在的风险。

---

## 问题本质

这是典型的**采样频率不足**问题，尤其是对于短暂的、剧烈的内存波动。

---

## 解决方案

### 方案一：在关键函数中主动检查（最推荐）

在那些可能申请大量内存的函数**内部**，在申请前、申请后、释放后分别检查堆空间，并记录最低值。

```c
// 包装一个内存检查宏
#define CHECK_HEAP_MIN() do { \
    size_t free_now = heap_caps_get_free_size(MALLOC_CAP_INTERNAL); \
    if(free_now < g_min_free_heap) { \
        g_min_free_heap = free_now; \
        ESP_LOGW("MEM", "New min heap: %u bytes (%.1f%%)", \
                 free_now, (float)free_now / total_heap * 100); \
    } \
    if(free_now < total_heap * 0.2) { \
        ESP_LOGE("MEM", "Heap critical (<20%%)"); \
    } \
} while(0)

// 在关键操作前后调用
void risky_function(void) {
    CHECK_HEAP_MIN();  // 操作前
    void *buf = malloc(LARGE_SIZE);
    if(buf) {
        CHECK_HEAP_MIN();  // 申请后
        // 使用 buf...
        free(buf);
        CHECK_HEAP_MIN();  // 释放后
    }
}
```

**优点**：精准捕捉，无性能损失（只在关键路径增加检查）。  
**缺点**：需要手动插入检查代码。

---

### 方案二：替换 malloc/free 钩子（全局监控）

通过 `heap_caps_register_failed_alloc_cb()` 或自定义 `malloc` 包装，在每次分配/释放后检查堆水位。

```c
// 注册内存分配失败回调（ESP-IDF 提供）
void esp_heap_trace_init(void);

// 或者自定义 new/delete 钩子（C++）
// 更简单：在每次分配后检查阈值，如果低于警戒线就记录
static void heap_check_on_alloc(void *ptr, size_t size) {
    size_t free_now = heap_caps_get_free_size(MALLOC_CAP_INTERNAL);
    // 更新最小值，记录警告...
}

// 用宏包装 malloc
#define malloc(size) ({ \
    void *p = malloc(size); \
    heap_check_on_alloc(p, size); \
    p; \
})
```

**优点**：覆盖所有动态内存操作，无遗漏。  
**缺点**：增加每次分配/释放的开销，对性能敏感的应用不友好。

---

### 方案三：提高心跳检测频率 + 滑动窗口

将心跳任务改为每 **10ms** 或 **20ms** 执行一次（更高频率会增加 CPU 负载）。同时记录一段时间内的最低空闲内存，而不是瞬时值。

```c
// 使用更快的定时器（如 esp_timer）
static void high_freq_timer_cb(void *arg) {
    size_t free_now = heap_caps_get_free_size(MALLOC_CAP_INTERNAL);
    if(free_now < g_min_free_in_window) {
        g_min_free_in_window = free_now;
    }
}

// 然后心跳任务（1秒）只负责报告窗口内的最小值
static void heartbeat_task(void *arg) {
    size_t min_in_last_sec = g_min_free_in_window;
    g_min_free_in_window = UINT32_MAX; // 重置
    // 检查是否低于20%，是否比历史最小值更低...
}
```

**优点**：无需修改业务代码，采样频率提升可捕捉更短的内存波动。  
**缺点**：频繁调用 `heap_caps_get_free_size()` 有轻微开销（通常可接受）；对微秒级的瞬时波动仍可能漏掉（但概率极低）。

---

### 方案四：使用内存水印（Watermark）机制

许多 RTOS 提供“最小剩余堆空间”的自动记录功能。例如 FreeRTOS 的 `xPortGetMinimumEverFreeHeapSize()`（需要配置 `configUSE_TRACE_FACILITY` 或类似）。ESP-IDF 中也提供了：

```c
// ESP-IDF 提供的 API，记录历史上最小的空闲堆大小（DRAM）
size_t heap_caps_get_minimum_free_size(MALLOC_CAP_INTERNAL);
```

该值会在每次 `malloc`/`free` 时**自动更新**，记录的是“从启动到现在的全局最小空闲值”。你只需要在心跳任务中读取它并判断是否下降即可：

```c
static size_t last_min_free = 0;
void heartbeat_task(void) {
    size_t current_min = heap_caps_get_minimum_free_size(MALLOC_CAP_INTERNAL);
    if(current_min < last_min_free) {
        ESP_LOGW("MEM", "New all-time low heap: %u bytes", current_min);
        last_min_free = current_min;
    }
    // 检查当前剩余是否小于20%（当前剩余 != 历史最小值）
    size_t free_now = heap_caps_get_free_size(MALLOC_CAP_INTERNAL);
    if(free_now < total_heap * 0.2) {
        ESP_LOGE("MEM", "Current heap below 20%%");
    }
}
```

**优点**：无需自己实现追踪，ESP-IDF 已经帮你做了。能捕捉**任意时刻**的最低点。  
**缺点**：只能得到全局最小值，无法知道最小值发生的时间或频率；也无法知道是否在某一秒内多次低于 20%。

---

### 方案五：组合方案（最佳实践）

结合方案一和方案四：

1. **全局水印**：利用 `heap_caps_get_minimum_free_size()` 记录历史最低点，在心跳任务中检查是否下降。
2. **关键路径主动检查**：在可能大量分配内存的函数（如解析 JSON、加载图片、创建 UI 对象）前后主动调用 `heap_caps_get_free_size()`，低于阈值时立即警告。
3. **保留心跳任务**：用于常规监控（当前空闲、最小空闲变化）和调整策略（如降低 UI 刷新率、清理缓存）。

这样既不会漏掉瞬时低谷（由水印机制保证），又能在关键操作发生时立即感知并采取措施。

---

## 总结推荐

| 方案 | 实现难度 | 性能开销 | 捕获能力 | 适用场景 |
| :--- | :--- | :--- | :--- | :--- |
| **方案一（关键函数检查）** | 低 | 极低 | 手动覆盖 | 代码可控、知道哪些函数会大块分配内存 |
| **方案二（malloc钩子）** | 中 | 高 | 全覆盖 | 需要绝对无遗漏，且性能不敏感 |
| **方案三（高频采样）** | 低 | 中 | 较高 | 不愿改业务代码，采样频率可接受 |
| **方案四（水印API）** | **极低** | **极低** | **全历史最低** | **最推荐**：零侵入，自动获得全局最低点 |
| **方案五（组合）** | 中 | 低 | 最佳 | 生产级项目 |

在你的心跳任务场景中，**方案四**是最简单且有效的改进：直接使用 `heap_caps_get_minimum_free_size()` 即可捕获任意时刻的历史最低堆空间，包括两次心跳之间发生的瞬时低谷。
````

