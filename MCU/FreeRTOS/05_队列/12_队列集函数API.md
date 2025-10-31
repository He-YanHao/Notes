# 队列集函数API

### 创建队列集 xQueueCreateSet()

```c
QueueSetHandle_t xQueueCreateSet( const UBaseType_t uxEventQueueLength )
```

参数：

- uxEventQueueLength：队列集能同时容纳的成员队列/信号量的最大数量。

    当有成员变为就绪状态时，其句柄会被发送到这个队列集内部的一个队列中。此参数指定了该内部队列的长度。它决定了队列集能够同时记录多少个成员的就绪事件。例如，如果设置为 3，则最多可以添加 3 个成员到集合中，并且该集合最多能同时记录 3 个就绪成员（虽然 `xQueueSelectFromSet()` 一次只返回一个）。

返回值：

- 成功：返回新创建的队列集的句柄 (`QueueSetHandle_t`)。
- 失败（通常因内存不足）：返回 `NULL`。

描述：

- `uxEventQueueLength` **不是** 指队列集成员本身内部队列的长度，而是队列集内部用于记录就绪事件的队列的长度。它必须 >= 你要添加到该集合中的成员总数。

### 添加队列集成员 xQueueAddToSet()

将你想要监听的队列或信号量句柄添加到队列集中。

*   注意：只能添加那些在创建时指定了 `queueSU_TYPE_BASIC` 类型的队列（使用 `xQueueCreate()` 或 `xQueueCreateStatic()` 创建的普通队列）和信号量（二进制信号量、计数信号量、互斥量）。
*   不能添加流缓冲区和消息缓冲区。

将一个队列或信号量添加到指定的队列集中，使其成为该集合的成员。


```c
BaseType_t xQueueAddToSet(
    QueueSetMemberHandle_t xQueueOrSemaphore,
    QueueSetHandle_t xQueueSet
)
```

参数：

- xQueueOrSemaphore：要添加到集合中的队列或信号量的句柄 (`QueueHandle_t` 或 `SemaphoreHandle_t`)。
- xQueueSet：目标队列集的句柄 (`QueueSetHandle_t`)。

返回值：

- `pdPASS` (1)： 成员成功添加到集合中。
- `pdFAIL` (0)： 添加失败。常见原因包括：
    *   队列集已满（已达到创建时指定的 `uxEventQueueLength`）。
    *   `xQueueOrSemaphore` 已经是另一个队列集的成员（一个对象只能属于一个队列集）。
    *   `xQueueOrSemaphore` 的类型不支持添加到队列集（见注意事项）。

描述：

- 成员必须在任务开始等待队列集 (`xQueueSelectFromSet`) 之前添加。

### 等待（阻塞）队列集中任何一个成员变为就绪状态 xQueueSelectFromSet()

任务调用此函数来**等待（阻塞）队列集中任何一个成员变为就绪状态**。当有成员就绪时，返回该就绪成员的句柄。

*   该函数会阻塞任务，直到队列集中**至少有一个**成员变为“就绪”状态（队列有数据可读，信号量可获取）。
*   当有成员就绪时，`xQueueSelectFromSet()` 会返回**一个**就绪成员的句柄。

*   


```c
QueueSetMemberHandle_t xQueueSelectFromSet(
    QueueSetHandle_t xQueueSet,
    TickType_t xTicksToWait
)
```

参数：

- xQueueSet：目标队列集的句柄 (`QueueSetHandle_t`)。
- xTicksToWait：最大阻塞时间。

返回值：

- QueueSetMemberHandle_t：就绪成员句柄。

### 接收数据项 xQueueReceive()

**接收（读取并移除）一个数据项**


```c
BaseType_t xQueueReceive(
    QueueHandle_t xQueue,
    void *pvBuffer,
    TickType_t xTicksToWait
)
```

参数：

- xQueue：**队列句柄**（即 `xQueueSelectFromSet` 返回的 `QueueSetMemberHandle_t`。
- pvBuffer：指向存储接收到的数据的缓冲区指针。
- xTicksToWait： **在队列集上下文中，此参数必须设置为 `0` (零阻塞)**。

返回值：

- `pdPASS` (1)： 成功接收到数据。
- `errQUEUE_EMPTY` (0)： **在队列集上下文中绝不会发生！** 如果发生，说明程序逻辑错误（未在 `xQueueSelectFromSet` 返回后立即调用，或者句柄类型错误）。

### 获取信号量 xSemaphoreTake()

**获取（锁定、减少计数）** 一个信号量（二进制信号量、计数信号量或互斥量）。


```c
BaseType_t xSemaphoreTake(
    SemaphoreHandle_t xSemaphore,
    TickType_t xTicksToWait
)
```

参数：

- xSemaphore：**信号量句柄**（即 `xQueueSelectFromSet` 返回的 `QueueSetMemberHandle_t`，需显式转换为 `SemaphoreHandle_t`）。
- xTicksToWait：**在队列集上下文中，此参数必须设置为 `0` (零阻塞)**。

返回值：

- `pdPASS` (1)： 成功获取到信号量。
- `pdFAIL` (0)： **在队列集上下文中绝不会发生！** 如果发生，说明程序逻辑错误（未在 `xQueueSelectFromSet` 返回后立即调用，或者句柄类型错误）。




```c
QueueSetHandle_t xQueueCreateSet( const UBaseType_t uxEventQueueLength )
```

参数：

- 

返回值：

- 