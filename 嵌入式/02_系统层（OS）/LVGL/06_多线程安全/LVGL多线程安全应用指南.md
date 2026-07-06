# LVGL 多线程安全应用指南

## 1. 核心原则

LVGL 的 GUI 操作（控件创建、属性修改、样式更新等）**不是线程安全的**。 

所有会改变 LVGL 内部状态或 GUI 对象状态的函数，必须在 **唯一一个 LVGL 任务** 的上下文中串行执行。

- **写入操作**：`lv_<object>_set_xxx()`、`lv_obj_xxx()` 等，绝对不可被多个线程并发调用。
- **读取操作**：`lv_obj_get_xxx()` 在无同步时可能因另一线程的并发写入而返回不一致结果。若在 LVGL 任务内部调用（如事件回调中），此时已天然受锁保护，无需额外同步；若在外部线程调用，也必须持有锁。

**目标**：保证同一时刻**至多一个线程**在执行会改变 LVGL 内部状态的代码路径，避免数据竞争、渲染错乱或崩溃。

LVGL 提供两种合法的跨线程 UI 更新范式：
1. **全局锁范式**：外部线程通过获取互斥锁短时间进入临界区，直接调用 LVGL API。
2. **异步调用范式**：外部线程将操作请求排入 LVGL 内部队列，由 LVGL 任务在自身周期内执行（`lv_async_call`）。

两者适用于不同场景，不可偏废。

## 2. 整体架构

```
                   +----------- 方案A：全局锁 ----------+
                   |                                    |
+-----------------+      +---------------------+      +-----------------------+
| 其他线程          |     | LVGL 任务（固定线程）   |      | 其他线程               |
|                 |      |                     |      |                       |
| lv_lock()       |      | 循环调用              |      | lv_async_call(cb, p)  |
| lv_xxx()        |      | lv_timer_handler()  |      |     （无需加锁）        |
| lv_unlock()     |      | （内部已处理锁）       |      |                       |
+-----------------+      +---------------------+      +-----------------------+
        ↑                          ↑                             ↑
        └──────── 互斥访问 ──────────┘                     └── 异步队列，无锁 ──┘
```

- **LVGL 任务**：一个由您创建的、永不退出的线程，其主体循环负责定期调用 `lv_timer_handler()` 来驱动 LVGL 定时器、输入处理、渲染以及执行异步队列。
- **锁**：一个互斥锁，用于保护 LVGL 内部状态。  
  - LVGL v9：启用 `LV_USE_OS` 并实现 OS 接口后，官方提供 `lv_lock()` / `lv_unlock()`，且 `lv_timer_handler()` 内部会自动安全地加锁/解锁。  
  - LVGL v8：无内置锁，需用户自行实现一个全局互斥锁，并在所有访问处手动加锁。
- **异步调用**：`lv_async_call()` 将一个回调函数安全地转移到 LVGL 任务线程中执行，调用线程完全无需获取锁，不会阻塞，也不会丢失更新。

---

## 3. 方法一：全局锁（适合极高频、可丢弃的更新）

### 3.1 锁的类型：普通互斥锁 vs 递归互斥锁

- **LVGL v9 内置锁为递归互斥锁**。这是为了避免在事件回调中意外重入导致死锁，官方实现已权衡健壮性。若您直接使用 `lv_lock()`/`lv_unlock()`，无需关心锁的类型。
- 若您需要自行实现锁（如 v8 或定制 v9 配置），请遵循以下原则：
  - **优先使用普通互斥锁**。它强制您遵守严格的调用层次，能尽早暴露潜在的重入设计问题。
  - **仅当无法避免同一线程重入时才使用递归互斥锁**。例如：在某个 LVGL 事件回调中必须调用一个来自第三方库的函数，而该函数恰好会再次尝试加锁执行 LVGL API。此时应记录原因并改用递归锁。
  - **绝对禁止的模式**：编写内部自动加锁的“安全封装函数”（如 `safe_lv_label_set_text`），然后在事件回调、定时器回调或 `lv_async_call` 的回调中调用它。这在使用普通锁时会立即死锁，使用递归锁时虽不死锁但会掩盖逻辑错误，导致难以调试的并发问题。**此类封装函数仅允许从外部线程调用，严禁在 LVGL 任务上下文内使用。**

### 3.2 加锁位置与模式

#### 模式 A：LVGL v9（启用 OS 支持，推荐）

`lv_timer_handler()` 内部已经自动调用 `lv_lock()` 和 `lv_unlock()`，LVGL 任务本身**无需**手动加锁。

```c
// LVGL 任务
void lvgl_task(void *arg) {
    while(1) {
        uint32_t delay = lv_timer_handler();   // 内部已处理锁
        vTaskDelay(pdMS_TO_TICKS(delay));
    }
}

// 外部线程更新 UI（带超时）
void update_from_thread(lv_obj_t *obj, lv_coord_t x) {
    if (lv_lock()) {              // v9 中 lv_lock() 实为获取互斥锁，超时返回 false
        lv_obj_set_x(obj, x);
        lv_unlock();
    } else {
        // 获取锁超时，丢弃本次更新（不可重复重试）
    }
}
```

> **注意**：`lv_lock()` 在 v9 中默认是阻塞的递归锁获取，不包含超时。要实现超时控制，您可以自行实现一个带超时的尝试锁包装，或者使用异步调用方案。

#### 模式 B：手动加锁（v8 或未启用 OS 接口的 v9）

```c
SemaphoreHandle_t lvgl_mutex;

void lvgl_task(void *arg) {
    while(1) {
        if (xSemaphoreTake(lvgl_mutex, portMAX_DELAY) == pdTRUE) {
            uint32_t delay = lv_timer_handler();
            xSemaphoreGive(lvgl_mutex);
            vTaskDelay(pdMS_TO_TICKS(delay));
        }
    }
}

// 外部线程写入 UI（带超时）
void update_from_thread(lv_obj_t *label, const char *text) {
    if (xSemaphoreTake(lvgl_mutex, pdMS_TO_TICKS(100)) == pdTRUE) {
        lv_label_set_text(label, text);
        xSemaphoreGive(lvgl_mutex);
    } else {
        // 超时放弃，记录日志
    }
}
```

**关键约束**：
- `lv_timer_handler()` 本身并不长期持锁，外部线程等待锁的时间通常极短（毫秒级）。但如果 LVGL 任务正在执行复杂动画的渲染，持锁时间可能达到 10~30ms。**外部线程必须使用有限超时获取锁，超时即丢弃更新，绝不可无限阻塞。**
- **严禁在 LVGL 任务内部再次调用加锁版本**。事件回调、定时器回调等已在锁保护内运行，直接调用 `lv_obj_set_xxx()` 等原始 API 即可。

---

## 4. 方法二：异步调用（`lv_async_call`，适合不可丢失的更新）

### 4.1 函数原型与原理

```c
lv_res_t lv_async_call(lv_async_cb_t async_xcb, void *user_data);
```

- **`async_xcb`**：类型为 `void (*)(void *)`，用户实现的实际任务函数。
- **`user_data`**：传递给回调的自定义数据。
- **返回值**：`LV_RES_OK` 表示成功排入队列；`LV_RES_INV` 表示内存不足，回调**将不会被调用**，且系统不会自动重试。

**执行机制**：
1. 分配一个 `lv_async_info_t`，保存回调与参数。
2. 创建一个延迟为 0、仅执行一次的内部定时器。
3. 该定时器会在**下一次 `lv_timer_handler()` 调用**的早期阶段被触发，在 LVGL 任务上下文中执行 `async_xcb`，随后自动销毁定时器并释放 `info` 内存。

> **核心语义**：`lv_async_call()` 会立刻返回，此时回调**尚未执行**。它保证回调一定在将来的 LVGL 任务周期内被安全地、序列化地调用，且**不会丢失请求**（除非内存分配失败）。

### 4.2 与全局锁的对比与选择

| 维度             | 全局锁（带超时）                               | `lv_async_call`                                      |
| ---------------- | ---------------------------------------------- | ---------------------------------------------------- |
| **数据可靠性**   | 锁竞争超时会导致更新被丢弃                     | 请求排入队列，永不丢失（除极端内存不足）             |
| **调用线程阻塞** | 阻塞等待锁，最长可达超时窗口（如 100ms）       | 非阻塞，立即返回                                     |
| **死锁风险**     | 若在 LVGL 任务上下文内误调加锁封装函数，会死锁 | 调用方完全不加锁，天然免疫重入死锁                   |
| **时效性**       | 可亚毫秒级获取锁后立即执行                     | 推迟到下一个 `lv_timer_handler` 周期，通常 < 16ms    |
| **适用场景**     | 极高频率、可丢弃、对延迟敏感（如外部回路反馈） | 大多数网络、传感器等异步数据更新，不可丢失的 UI 指令 |

**推荐策略**：
- **默认首选 `lv_async_call`**。它没有死锁隐患，能保证更新不丢失，且性能开销低。除非有严格延迟需求且可容忍偶尔丢失，否则不要使用锁方案。
- **绝对禁止在 ISR 中调用 `lv_async_call`**。其内部调用了 `lv_mem_alloc`，不满足中断上下文要求。ISR 应通过 FreeRTOS 任务通知或信号量唤醒 LVGL 任务，再由该任务执行 `lv_async_call`（或直接调用 LVGL API）。

### 4.3 正确用法示例

```c
// 回调函数：在 LVGL 任务上下文中执行
void update_label_text(void *user_data) {
    const char *text = (const char *)user_data;
    lv_label_set_text(my_label, text);    // 直接调用，无需加锁
    free(user_data);                      // 如果由调用方动态分配
}

// 网络线程回调
void on_data_received(const char *new_text) {
    char *copy = strdup(new_text);
    if (copy == NULL) return;

    if (lv_async_call(update_label_text, copy) != LV_RES_OK) {
        // 内存极低的极端情况，更新丢失，需系统容忍
        free(copy);
    }
    // 函数立即返回，网络线程不会被阻塞
}
```

### 4.4 严格约束

- **回调内不得执行长耗时操作**（如大量计算、文件 I/O）。它运行在 LVGL 任务中，会阻塞渲染和动画。
- **回调内绝对不允许再次调用 `lv_async_call` 形成自依赖链**（尤其是循环或递归），会导致逻辑混乱和潜在的优先级反转。
- **资源生命周期管理**：`user_data` 指向的内存必须保证在回调执行时仍然有效。常用模式是调用方动态分配，回调中释放。如果使用静态缓冲区，需确保在回调执行前该缓冲区不会被覆盖（可能需要额外的数据拷贝）。
- **失败处理**：`LV_RES_INV` 表示系统内存极度紧张，应视为严重错误，但不可因此自旋重试或阻塞，以免加剧内存压力。

---

## 5. 回调与执行上下文（锁保护范围）

### 5.1 哪些回调已在锁保护内
以下回调由 `lv_timer_handler()` 直接或间接调用，因此在执行时**已天然持有 LVGL 锁**（无论是 v9 自动锁还是手动包裹锁）：

- **输入设备回调**：`lv_indev_drv_t` 中的 `read_cb`
- **控件事件回调**：`lv_obj_add_event_cb()` 注册的点击、值变更等回调
- **LVGL 软件定时器回调**：`lv_timer_create()` 创建的回调
- **`lv_async_call` 的回调**

在这些函数内部：
- **直接调用 LVGL API 即可，严禁再次加锁**（`lv_lock` 或手动获取互斥锁）。
- **严禁调用预先包装的“安全”加锁函数**（如 `safe_lv_label_set_text`）。若必须通过统一接口访问，应提供两个版本：内部版本（无锁）和外部版本（加锁），并明确调用边界。

### 5.2 例外情况
如果回调中需要访问与 LVGL 无关、且被其他线程修改的共享数据，应使用独立的互斥锁进行保护，**该锁必须与 LVGL 锁完全不同**，以避免死锁。

---

## 6. `lv_timer_handler()` 的独占性与连续性

- **仅允许一个固定线程** 调用 `lv_timer_handler()`，称为“LVGL 任务”。
- 即使通过锁串行化，也**不允许两个线程交替**调用该函数。其内部动画状态机、输入事件处理、脏区域追踪严重依赖调用线程的连续性，交替调用将导致画面闪烁、动画撕裂，甚至触发断言。
- 在 v9 中，若用户违反此规则，甚至可能因内部线程 ID 检查而直接崩溃。

---

## 7. 中断服务程序（ISR）约束

**绝对禁止在 ISR 中调用任何 LVGL API**，包括：
- `lv_obj_set_xxx()` 等对象操作函数
- `lv_async_call()`（因含有内存分配）
- `lv_lock()`/`lv_unlock()`（因可能阻塞）

ISR 的唯一合法行为是通过 RTOS 的原语发送信号：

```c
static TaskHandle_t lvgl_task_handle;

// 触摸屏中断
void touch_isr(void) {
    BaseType_t higher = pdFALSE;
    vTaskNotifyGiveFromISR(lvgl_task_handle, &higher);
    portYIELD_FROM_ISR(higher);
}

// LVGL 任务
void lvgl_task(void *arg) {
    while(1) {
        // 等待通知，或使用超时来周期驱动
        if (ulTaskNotifyTake(pdTRUE, pdMS_TO_TICKS(5)) > 0) {
            // 通知到达，立刻在下一次 handler 中处理
        }
        // 这里是带锁的 handler 调用（v9 自动）
        lv_timer_handler();
        // 或手动加锁版本...
    }
}
```

接收到信号的 LVGL 任务可以以锁方式或 `lv_async_call` 方式完成 UI 更新（通常在 `lv_timer_handler` 后的输入处理中自然更新）。

---

## 8. 避免长时间持锁与优先级设置

### 8.1 持锁时间
- 通常 `lv_timer_handler()` 单次执行 1~5ms，复杂界面可达 10~30ms。在此期间外部锁请求会被阻塞。
- 严禁在持有 LVGL 锁期间进行网络、文件 I/O 或大量计算。

### 8.2 任务优先级设计
- **LVGL 任务的优先级必须高于所有可能往 LVGL 提交异步请求或通过锁提交更新的其他线程**。否则可能发生优先级反转：低优先级 LVGL 任务被中优先级的提交线程抢占而无法处理请求，造成界面卡死。
- 同时，LVGL 任务优先级应**低于**关键实时任务（电机控制、传感器采样）。  
- 在 FreeRTOS 中，一个合理的初始设定可能是：关键实时任务优先级 5~6，LVGL 任务 4，网络/日志等后台任务 2~3。具体数值务必根据实际测量调整。
- 使用 `lv_async_call` 时该原则同样适用，若 LVGL 任务优先级过低，异步请求将迟迟得不到执行。

---

## 9. 常见错误与正确示例对照（扩展版）

| 错误做法                                                     | 问题                                                         | 正确做法                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 外部线程直接 `lv_label_set_text(label, txt)` 而没有锁        | 并发修改，崩溃或渲染错乱                                     | 使用 `lv_async_call`，或带超时的锁方案（见第 3、4 节）       |
| 在事件回调或 `lv_async_call` 回调中调用自制的加锁封装函数（如 `safe_lv_label_set_text`） | 若为普通锁则**死锁**；若为递归锁则隐藏逻辑错误               | 回调内直接调用原生 `lv_xxx` API，绝不使用任何外部加锁包装    |
| 多个线程轮换使用不同时间片争夺调用 `lv_timer_handler()`      | 动画撕裂、输入状态混乱，可能断言失败                         | 始终保持唯一一个 LVGL 任务                                   |
| ISR 中调用 `lv_obj_set_pos()` 或 `lv_async_call()`           | 破坏 LVGL 内存结构可能导致 hard fault；后者不可在中断上下文分配内存 | ISR 仅发送信号，由 LVGL 任务响应更新                         |
| 持锁期间进行网络读取或长时间计算                             | 界面完全卡住                                                 | 拆分操作：数据预处理在外部线程完成，仅将最终结果通过 `lv_async_call` 传入或极短持锁内复制至控件 |
| 在 `lv_async_call` 回调中调用 `lv_async_call` 并等待结果     | 形成请求链，可能导致回调风暴或顺序混乱                       | 绝对避免。若需多次操作，在同一回调内顺序完成                 |
| 高优先级外部线程频繁带锁更新，LVGL 任务优先级过低            | 优先级反转，LVGL 任务得不到 CPU 执行渲染                     | 确保 LVGL 任务优先级高于所有提交 UI 更新的外部线程；优先使用 `lv_async_call` 消除锁竞争 |

---

## 10. 版本与配置说明（精确化）

| 版本   | 锁机制                                                       | 推荐做法                                                     |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **v8** | 无内置锁，必须手动管理                                       | 创建全局互斥锁，在 `lv_timer_handler` 前后及外部线程加锁。优先使用 `lv_async_call` 减少手动加锁范围，降低出错风险。 |
| **v9** | 引入 `lv_lock`/`lv_unlock`，`LV_USE_OS` 开启后 `lv_timer_handler` 自动锁护 | **直接使用官方锁机制**，外部线程调用 `lv_lock()` 或使用 `lv_async_call`（推荐后者）。不要自行创建额外的 LVGL 锁，以免与内部锁嵌套酿成死锁。 |

在 v9 中，若您启用了 `LV_USE_OS` 并移植了对应操作系统的接口文件（如 `lv_os_freertos.c`），则 `lv_timer_handler()` 的实现会自动包裹 `lv_lock`/`lv_unlock`，无需用户干预。此时外部线程的两种安全访问方式为：
1. `lv_async_call(cb, data)` （首选）
2. `lv_lock(); /* 操作 */ lv_unlock();` （次选，注意不可在 LVGL 任务内调用，且必须考虑锁竞争超时）

---

## 11. 附录：完整参考骨架

### 11.1 FreeRTOS + LVGL v9（启用 OS，最佳实践）

```c
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "lvgl.h"

// 非 LVGL 线程更新 UI —— 异步方式（推荐）
void async_set_label_text(lv_obj_t *label, const char *text) {
    char *copy = strdup(text);
    if (copy && lv_async_call((lv_async_cb_t)lv_label_set_text, copy) != LV_RES_OK) {
        free(copy);
    }
}

// LVGL 任务
static void lvgl_task(void *arg) {
    while (1) {
        uint32_t delay = lv_timer_handler();
        vTaskDelay(pdMS_TO_TICKS(delay));
    }
}

void app_main(void) {
    lv_init();
    // ... 初始化显示、输入设备 ...

    xTaskCreate(lvgl_task, "lvgl", 4096, NULL, 4, NULL);
    // 创建 UI 界面
    lv_obj_t *label = lv_label_create(lv_scr_act());
    lv_label_set_text(label, "Hello");

    // 模拟外部线程稍后更新文本
    // ...
}
```

### 11.2 FreeRTOS + LVGL v8（手动锁 + 异步调用优先）

```c
#include "freertos/FreeRTOS.h"
#include "freertos/semphr.h"
#include "lvgl.h"

SemaphoreHandle_t lvgl_mutex;

// 异步回调实现
static void set_text_cb(void *user_data) {
    lv_label_set_text((lv_obj_t*)user_data, "Updated async");
}

// 外部线程通过异步调用更新
void request_update(lv_obj_t *label) {
    lv_async_call(set_text_cb, label);  // 无锁，不阻塞
}

// LVGL 任务
static void lvgl_task(void *arg) {
    while (1) {
        if (xSemaphoreTake(lvgl_mutex, portMAX_DELAY) == pdTRUE) {
            uint32_t delay = lv_timer_handler();
            xSemaphoreGive(lvgl_mutex);
            vTaskDelay(pdMS_TO_TICKS(delay));
        }
    }
}

// 外部线程也可以使用带超时的加锁方式（仅丢弃型高频更新）
void high_freq_update(lv_obj_t *obj, int value) {
    if (xSemaphoreTake(lvgl_mutex, pdMS_TO_TICKS(5)) == pdTRUE) {
        lv_bar_set_value(obj, value, LV_ANIM_OFF);
        xSemaphoreGive(lvgl_mutex);
    }
}
```

