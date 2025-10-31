# 软件定时器函数API

## 基础标配函数

### 创建软件定时器 xTimerCreate

```c
TimerHandle_t xTimerCreate(
    const char * const pcTimerName,
    const TickType_t xTimerPeriodInTicks,
    const UBaseType_t uxAutoReload,
    void * const pvTimerID,
    TimerCallbackFunction_t pxCallbackFunction
)
```

功能：创建新的软件定时器实例

参数：

- `pcTimerName`: 定时器名称（调试用）
- `xTimerPeriodInTicks`: 定时器周期（tick数）
- `uxAutoReload`:
    - `pdTRUE`: 自动重载（周期性）
    - `pdFALSE`: 单次定时器
- `pvTimerID`: 定时器ID标识符，一个唯一的数字（用于区分共享回调的定时器）。
- `pxCallbackFunction`: 定时器到期回调函数

返回值：

- 成功：定时器句柄
- 失败：NULL



### 启动定时器 xTimerStart

```c
BaseType_t xTimerStart(
    TimerHandle_t xTimer,
    TickType_t xTicksToWait
)
```

功能：启动或重启定时器（若已在运行则重置）

参数：

- `xTimer`: 目标定时器句柄
- `xTicksToWait`: 命令队列满时最大等待时间

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



### 停止定时器 xTimerStop

```c
BaseType_t xTimerStop(
    TimerHandle_t xTimer,
    TickType_t xTicksToWait
)
```

功能：停止运行中的定时器

参数：

- `xTimer`: 目标定时器句柄
- `xTicksToWait`: 命令队列满时最大等待时间

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



### 重置定时器 xTimerReset

```c
BaseType_t xTimerReset(
    TimerHandle_t xTimer,
    TickType_t xTicksToWait
)
```

功能：重置定时器（重新开始计时）

参数：

- `xTimer`: 目标定时器句柄
- `xTicksToWait`: 命令队列满时最大等待时间

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



### 修改定时器周期 xTimerChangePeriod

```c
BaseType_t xTimerChangePeriod(
    TimerHandle_t xTimer,
    TickType_t xNewPeriod,
    TickType_t xTicksToWait
)
```

功能：动态修改定时器周期

参数：

- `xTimer`: 目标定时器句柄
- `xNewPeriod`: 新周期值（tick数）
- `xTicksToWait`: 命令队列满时最大等待时间

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



### 删除定时器 xTimerDelete

```c
BaseType_t xTimerDelete(
    TimerHandle_t xTimer,
    TickType_t xTicksToWait
)
```

功能：删除定时器并释放资源

参数：

- `xTimer`: 目标定时器句柄
- `xTicksToWait`: 命令队列满时最大等待时间

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败





### 查询定时器状态 xTimerIsTimerActive

```c
BaseType_t xTimerIsTimerActive(
    TimerHandle_t xTimer
)
```

功能：检查定时器是否处于活动状态

参数：

- `xTimer`: 目标定时器句柄

返回值：

- `pdFALSE`: 未激活（休眠状态）
- 非零值：已激活







## 定时器ID相关API

### 获取定时器ID xTimerGetTimerID

```c
void * xTimerGetTimerID(
    TimerHandle_t xTimer
)
```

功能：获取定时器关联的ID值

参数：

- `xTimer`: 目标定时器句柄

返回值：

- 定时器关联的ID指针



### 设置定时器ID vTimerSetTimerID

```c
void vTimerSetTimerID(
    TimerHandle_t xTimer,
    void *pvNewID
)
```

功能：设置定时器关联的ID值

参数：

- `xTimer`: 目标定时器句柄
- `pvNewID`: 新的ID值

返回值：无





## 中断安全版本（ISR）

### 中断中启动定时器 xTimerStartFromISR

```c
BaseType_t xTimerStartFromISR(
    TimerHandle_t xTimer,
    BaseType_t *pxHigherPriorityTaskWoken
)
```

功能：中断服务程序中启动定时器

参数：

- `xTimer`: 目标定时器句柄
- `pxHigherPriorityTaskWoken`: 是否触发上下文切换

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



### 中断中停止定时器 xTimerStopFromISR

```c
BaseType_t xTimerStopFromISR(
    TimerHandle_t xTimer,
    BaseType_t *pxHigherPriorityTaskWoken
)
```

功能：中断服务程序中停止定时器

参数：

- `xTimer`: 目标定时器句柄
- `pxHigherPriorityTaskWoken`: 是否触发上下文切换

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



### 中断中重置定时器 xTimerResetFromISR

```c
BaseType_t xTimerResetFromISR(
    TimerHandle_t xTimer,
    BaseType_t *pxHigherPriorityTaskWoken
)
```

功能：中断服务程序中重置定时器

参数：

- `xTimer`: 目标定时器句柄
- `pxHigherPriorityTaskWoken`: 是否触发上下文切换

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



### 中断中修改周期 xTimerChangePeriodFromISR

```c
BaseType_t xTimerChangePeriodFromISR(
    TimerHandle_t xTimer,
    TickType_t xNewPeriod,
    BaseType_t *pxHigherPriorityTaskWoken
)
```

功能：中断服务程序中修改定时器周期

参数：

- `xTimer`: 目标定时器句柄
- `xNewPeriod`: 新周期值（tick数）
- `pxHigherPriorityTaskWoken`: 是否触发上下文切换

返回值：

- `pdPASS`: 命令入队成功
- `pdFAIL`: 命令入队失败



## 关键说明

1. 所有非ISR函数在**任务上下文**调用，ISR版本在**中断上下文**调用
2. 定时器操作通过命令队列实现，实际执行由守护任务完成
3. 回调函数在守护任务上下文中执行，不能调用阻塞API
4. `xTicksToWait`参数：
    - `0`：非阻塞调用
    - `portMAX_DELAY`：无限等待（需配置`INCLUDE_vTaskSuspend=1`）
5. ISR版本中的`pxHigherPriorityTaskWoken`：
    - 初始值应为`pdFALSE`
    - 返回`pdTRUE`表示需要上下文切换（调用`portYIELD_FROM_ISR()`）



## 典型应用场景

1.  **周期性任务：**
    *   传感器数据采样（如每 100ms 读取一次温度）。
    *   状态灯闪烁（如 LED 心跳灯）。
    *   周期性状态检查或维护任务。
    *   发送周期性网络心跳包。

2.  **超时处理：**
    *   按键消抖后检测长按（按下启动定时器，松开停止定时器；定时器到期时若按键仍按下则为长按）。
    *   通信协议中的响应超时（发送请求后启动定时器，收到响应停止定时器；定时器到期则认为超时）。
    *   状态机中的状态超时（进入某个状态启动定时器，超时后自动转移到超时状态）。
    *   看门狗喂狗（在更高优先级任务中周期性重置看门狗定时器）。

3.  **延迟执行：** 在未来的某个时间点执行一次操作。

## 优点

*   **节省硬件资源：** 不需要额外的硬件定时器外设，尤其适用于需要大量“逻辑定时器”的场景。
*   **灵活性：** 可以动态创建、启动、停止、修改、删除定时器。
*   **易于管理：** 通过统一的 API 和守护任务进行管理，简化了代码结构。
*   **集成性：** 与 FreeRTOS 的任务调度、队列、信号量等机制无缝集成。
*   **可移植性：** 作为 FreeRTOS 内核的一部分，具有良好的可移植性。

## 缺点/注意事项

*   **精度有限：** 依赖系统节拍中断，精度低于硬件定时器，存在节拍抖动和调度延迟。
*   **执行上下文：** 回调在守护任务中执行，不能阻塞，必须简短。需要小心处理与其他任务的同步和数据共享。
*   **守护任务优先级：** 设置不当可能导致回调延迟或抢占问题。
*   **资源消耗：** 每个定时器对象需要内存，守护任务需要栈空间和 CPU 时间。
*   **启动延迟：** 定时器启动后，第一次到期的时间至少是完整的周期时间（不是立即触发）。