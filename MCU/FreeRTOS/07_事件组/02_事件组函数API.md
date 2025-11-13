# 事件组函数API

## 创建事件组 xEventGroupCreate()

```c
EventGroupHandle_t xEventGroupCreate(void)
```

功能：创建一个新的事件组，并返回其句柄。所有事件位初始化为 0。

返回值：

- 成功则返回事件组句柄；
- 失败（通常内存不足）返回 NULL。

## 设置事件组事件位

### 设置事件组事件位 xEventGroupSetBits()

```c
EventBits_t xEventGroupSetBits(
    EventGroupHandle_t xEventGroup,
    const EventBits_t uxBitsToSet
)
```

功能：在指定的事件组中设置（置位）一个或多个事件位。

参数：

- xEventGroup：目标事件组句柄。
- uxBitsToSet：一个位掩码（bitmask），指定要设置哪些位。例如，设置位 0 和位 2，则 `uxBitsToSet = (1 << 0) | (1 << 2)`。

返回值：

- 设置操作**之后**事件组的值（所有位的最新状态）。

*   **上下文：** **任务上下文**。不能在中断中使用。
*   **副作用：** 检查并解除所有因等待 `uxBitsToSet` 中指定的位（根据它们的等待逻辑）而被阻塞的任务。





### 中断安全设置事件组事件位 xEventGroupSetBitsFromISR()

```c
BaseType_t xEventGroupSetBitsFromISR(
    EventGroupHandle_t xEventGroup,
    const EventBits_t uxBitsToSet,
    BaseType_t *pxHigherPriorityTaskWoken
)
```

功能：在中断服务程序（ISR）中设置事件组中的一个或多个位。这是 `xEventGroupSetBits()` 的中断安全版本。

参数：

- xEventGroup：
- uxBitsToSet：指定要设置哪些位的位掩码。
- pxHigherPriorityTaskWoken：函数可能解除阻塞任务。如果解除阻塞的任务优先级高于当前运行的任务（即被中断的任务），则该参数会被设为 `pdTRUE`。退出 ISR 前通常需要检查此值并进行上下文切换（`portYIELD_FROM_ISR()`）。

返回值：

- 如果消息成功发送到 RTOS 守护任务（负责实际设置位）则返回 `pdPASS`；
- 如果守护任务队列已满则返回 `pdFAIL`（设置失败）。



## 清除事件组中指定的一个或多个位

### 清除事件组中指定的一个或多个位 xEventGroupClearBits

```c
EventBits_t xEventGroupClearBits(
    EventGroupHandle_t xEventGroup,
    const EventBits_t uxBitsToClear
)
```

功能：清除事件组中指定的一个或多个位

参数：

*   `xEventGroup`: 目标事件组句柄。
*   `uxBitsToClear`: 位掩码，指定要清除哪些位。

返回值：

- 清除操作**之前**事件组的值。

**上下文：** 任务或中断（使用 `FromISR` 版本）。





### 中断安全清除事件组中指定一个或多个位 xEventGroupClearBitsFromISR()

```c
EventBits_t xEventGroupClearBitsFromISR(
    EventGroupHandle_t xEventGroup,
    const EventBits_t uxBitsToClear
)
```

功能：`xEventGroupClearBits()` 的中断安全版本。

功能：清除事件组中指定的一个或多个位

参数：

*   `xEventGroup`: 目标事件组句柄。
*   `uxBitsToClear`: 位掩码，指定要清除哪些位。

返回值：

- 清除操作**之前**事件组的值。

**上下文：** 任务或中断（使用 `FromISR` 版本）。





## 读取事件组的当前值

### 读取事件组的当前值 xEventGroupGetBits()

```c
EventBits_t xEventGroupGetBits(
    EventGroupHandle_t xEventGroup
)
```

功能：读取事件组的当前值（所有位的状态）。

参数：

- 事件组句柄。

返回值：

- 当前事件组的值。



### 中断上下文读取事件组的当前值 xEventGroupGetBitsFromISR()

```c
EventBits_t xEventGroupGetBitsFromISR(
    EventGroupHandle_t xEventGroup
)
```

功能：读取事件组的当前值（所有位的状态）。

参数：

- 事件组句柄。

返回值：

- 当前事件组的值。

**注意：** `GetBits` 用于任务上下文，`GetBitsFromISR` 用于中断上下文。这些函数不会阻塞，也不会改变事件位的值。



## 调用事件组 xEventGroupWaitBit()

```c
EventBits_t xEventGroupWaitBits(
    const EventGroupHandle_t xEventGroup,
    const EventBits_t uxBitsToWaitFor,
    const BaseType_t xClearOnExit,
    const BaseType_t xWaitForAllBits,
    TickType_t xTicksToWait
)
```

功能：使调用任务进入阻塞状态，等待指定事件组中的一个或多个事件位被设置。

参数：

- `xEventGroup`: 要等待的事件组句柄。

*   `uxBitsToWaitFor`: 位掩码，指定任务等待哪些位。
*   `xClearOnExit`:
    *   `pdTRUE`: 如果函数因为等待的条件满足而返回（不是因为超时），则在返回前**清除** `uxBitsToWaitFor` 中指定的位。
    *   `pdFALSE`: 不自动清除任何位。
*   `xWaitForAllBits`:
    *   `pdTRUE`: 任务等待 `uxBitsToWaitFor` 中指定的**所有**位都被置位（逻辑与）。
    *   `pdFALSE`: 任务等待 `uxBitsToWaitFor` 中指定的**任意一个**位被置位（逻辑或）。
*   `xTicksToWait`: 任务等待的最大时间（ticks）。使用 `portMAX_DELAY` 表示无限期等待（需配置 `INCLUDE_vTaskSuspend` 为 1）。

返回值：

*   满足等待条件时：返回事件组的值（在清除之前，如果指定了清除）。返回值中 `uxBitsToWaitFor` 相关的位会被置位（满足逻辑）。
*   超时返回：返回值中 `uxBitsToWaitFor` 相关的位可能不全置位。
*   事件组已被删除：返回 `0`（需配置 `configUSE_TRACE_FACILITY` 为 1）。