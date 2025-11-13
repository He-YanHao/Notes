# 任务通知函数API

## 发送通知函数

### xTaskNotify()

#### 中断任务通知发送 xTaskNotifyFromISR()

专为中断上下文设计的任务通知发送函数，它允许在中断服务程序(ISR)中安全地向任务发送通知。这是实现高效中断到任务通信的关键工具。

```c
BaseType_t xTaskNotifyFromISR(
    TaskHandle_t xTaskToNotify,  // 目标任务句柄
    uint32_t ulValue,            // 传递的值
    eNotifyAction eAction,       // 操作类型
    BaseType_t *pxHigherPriorityTaskWoken // 上下文切换标志
);
```

**参数**：

- xTaskToNotify：目标任务句柄
- ulValue：传递的值
- eAction：操作类型
    - eNoAction：简单事件通知，最安全，无数据冲突风险
    - eSetBits：状态标志更新，原子操作，适合位操作
    - eIncrement：资源计数，避免计数器溢出
    - eSetValueWithOverwrite：数据传输，可能覆盖未处理数据
    - eSetValueWithoutOverwrite：保护性数据，可能失败(pdFAIL)，需处理未送达
- pxHigherPriorityTaskWoken：上下文切换标志

**返回值**：

- 



#### 通知值 加 1 xTaskNotifyGive()

```c
BaseType_t xTaskNotifyGive(TaskHandle_t xTaskToNotify);
```

**参数**：`xTaskToNotify`：目标任务的句柄，表示要通知哪个任务。

**返回值**：

- **成功返回**：pdPASS，原型为1。
- **失败返回**：pdFAIL，原型为0。

作用：

1. **增加通知值**：将目标任务的 32 位通知值 **加 1**。
2. **解除阻塞**：
    - 如果目标任务正在等待通知（通过 `ulTaskNotifyTake()` 阻塞）
    - 会**立即解除**该任务的阻塞状态
    - 使目标任务进入就绪状态
3. **通知状态更新**：
    - 将目标任务的通知状态设置为 `eNotified` 任务在等待期间已被通知（通知已送达）。
    - 表示该任务已被成功通知。

#### vTaskNotifyGiveFromISR

```c

```

**参数**：

- 

**返回值**：

- 

#### xTaskNotifyAndQueryFromISR

#### xTaskNotifyAndQuery





## 接收通知函数

#### xTaskNotifyWait()

```c
BaseType_t xTaskNotifyWait(
    uint32_t ulBitsToClearOnEntry,  // 进入时清除的位
    uint32_t ulBitsToClearOnExit,   // 退出时清除的位
    uint32_t *pulNotificationValue, // 存储通知值的指针
    TickType_t xTicksToWait         // 阻塞超时时间
);
```

**参数**：

- `ulBitsToClearOnEntry`：进入时清除的位。在函数开始等待**前**清除通知值的特定位。

    - 如：`0x0000000F`清除后四位。

- `ulBitsToClearOnExit`：退出时清除的位。成功接收到通知后，在函数**返回前**清除特定位。

    - `ULONG_MAX` (清除所有位) 
    - `BIT_0` (清除位0)

- `pulNotificationValue`：存储通知值的指针，存储接收到的通知值。

    - 应用`ulBitsToClearOnExit`**之后**的值

    - 在清除操作**之后**复制值

- `xTicksToWait`：阻塞超时时间

    - `0`：不等待。
    - `portMAX_DELAY`：无限期等待。

#### ulTaskNotifyTake()

#### 接收通知 ulTaskNotifyTake()

```c
uint32_t ulTaskNotifyTake(
    BaseType_t xClearCountOnExit,
    TickType_t xTicksToWait
);
```

**参数**：

- xClearCountOnExit：

    -  `pdTRUE`：成功接收后将通知值清零
    -  `pdFALSE`：成功接收后将通知值减1

- xTicksToWait：

    - 0：非阻塞模式（立即返回）

    - `portMAX_DELAY`：无限期等待。具体 tick 数：如 pdMS_TO_TICKS(100)

**返回值**：

- **成功接收时**：返回**进入函数时**的通知值（在清除/减 1 操作之前的值）
- **超时返回时**：返回 `0`

#### 全能通知 xTaskNotify()

```c
BaseType_t xTaskNotify(
    TaskHandle_t xTaskToNotify,  // 目标任务句柄
    uint32_t ulValue,            // 传递的值
    eNotifyAction eAction        // 操作类型
);
```

参数：

- `xTaskToNotify`：目标任务句柄

- `ulValue`：32位数据值，根据 `eAction` 不同有不同含义

    - 位掩码（`eSetBits`）
    - 数值（`eSetValue...`）
    - 增量（`eIncrement`）
    - 忽略（`eNoAction`）

- `eAction`：核心参数

    - | 枚举值                          | 作用             | 等效操作            | 典型应用   |
        | :------------------------------ | :--------------- | :------------------ | :--------- |
        | **`eNoAction`**                 | 仅更新通知状态   | `xTaskNotifyGive()` | 二值信号量 |
        | **`eSetBits`**                  | 位设置（OR操作） | 事件标志组          | 多事件通知 |
        | **`eIncrement`**                | 通知值加1        | `xTaskNotifyGive()` | 计数信号量 |
        | **`eSetValueWithOverwrite`**    | 覆盖通知值       | 轻量级消息队列      | 数据传输   |
        | **`eSetValueWithoutOverwrite`** | 非覆盖设置       | 保护性数据传递      | 防数据丢失 |

返回值：

- `pdPASS`：操作成功完成
- `pdFAIL`：仅当使用 `eSetValueWithoutOverwrite` 且通知值未被修改时返回

功能描述：

| 功能       | `xTaskNotifyGive()` | `xTaskNotify()`      | 优势           |
| :--------- | :------------------ | :------------------- | :------------- |
| 二值信号量 | ✅                   | ✅ (eNoAction)        | 相同性能       |
| 计数信号量 | ✅                   | ✅ (eIncrement)       | 相同性能       |
| 事件标志组 | ❌                   | ✅ (eSetBits)         | 支持位操作     |
| 数据传输   | ❌                   | ✅ (eSetValue...)     | 可携带32位数据 |
| 防覆盖保护 | ❌                   | ✅ (WithoutOverwrite) | 避免数据丢失   |
| 状态查询   | ❌                   | ✅ (通过返回值)       | 获取操作结果   |



## 状态管理函数

### xTaskNotifyStateClear()

```c

```

**参数**：

- 
- 

**返回值**：

- 
- 



### xTaskNotifyStateClearFromISR()



### ulTaskNotifyValueClear()

```c
uint32_t ulTaskNotifyValueClear(
    TaskHandle_t xTask,        // 目标任务句柄
    uint32_t ulBitsToClear     // 要清除的位掩码
);
```

**参数**：

- xTask：句柄。
- ulBitsToClear：为0时不清除。

**返回值**：

- 直接返回**读取时的通知值**（32位无符号整数）



### ulTaskNotifyValueClearFromISR()


