# 队列函数API

### 创建队列

#### 动态创建队列 xQueueCreate()

函数原型：

```c
QueueHandle_t xQueueCreate(
    UBaseType_t uxQueueLength,  // 队列长度（最大数据项数量）
    UBaseType_t uxItemSize      // 每个数据项的大小（字节）
);
```

参数：

- uxQueueLength：队列长度（最大数据项数量）
- uxItemSize：每个数据项的大小（字节）

返回值：

- 成功：队列句柄（`QueueHandle_t`）
- 失败：`NULL`（内存不足时）

**队列图示**：

```c
// 队列内存布局示例 (队列长度uxQueueLength=3, 每个数据项的大小uxItemSize=4)
[槽位0: 4字节] [槽位1: 4字节] [槽位2: 4字节]
```

#### 静态创建队列 xQueueCreateStatic()

函数原型：

```c
QueueHandle_t xQueueCreateStatic(
    UBaseType_t uxQueueLength,
    UBaseType_t uxItemSize,
    uint8_t *pucQueueStorage,   // 用户提供的存储缓冲区
    StaticQueue_t *pxQueueBuffer // 用户提供的队列结构体
);
```

参数：

- uxQueueLength：队列长度（最大数据项数量）
- uxItemSize：队列长度（最大数据项数量）
- *pucQueueStorage：用户提供的存储缓冲区
- 

返回值：

- 

案例：

```c
// 静态分配内存
#define QUEUE_LENGTH 5
#define ITEM_SIZE sizeof(float)

static uint8_t ucQueueStorage[QUEUE_LENGTH * ITEM_SIZE];
static StaticQueue_t xQueueBuffer;

QueueHandle_t xFloatQueue = xQueueCreateStatic(
    QUEUE_LENGTH,
    ITEM_SIZE,
    ucQueueStorage,
    &xQueueBuffer
);
```

### 数据发送函数（任务中使用）

#### 发送到队首 xQueueSendToFront() 

函数原型：

```c
BaseType_t xQueueSendToFront(
    QueueHandle_t xQueue,
    const void *pvItemToQueue,
    TickType_t xTicksToWait
);
```

参数：

- 

返回值：

- 

```
//初始状态
[ 空 | 空 | 空 | 空 | 空 ]
//调用一次xQueueSendToFront() 
[data1| 空 | 空 | 空 | 空 ]
//调用两次xQueueSendToFront() 
[data1|data2| 空 | 空 | 空 ]
//调用三次xQueueSendToFront() 
[data3|data1|data2| 空 | 空 ]
//所有现有数据后移
```

**阻塞机制：**

*   **发送阻塞：** 当任务调用 `xQueueSend()` 或 `xQueueSendToBack()` (等同于 `xQueueSend()`) 或 `xQueueSendToFront()` 向一个**满队列**发送数据时：
    *   如果指定了阻塞时间（`xTicksToWait` > 0），任务会被**挂起**（进入阻塞状态），并放入该队列的**发送阻塞列表**。
    *   任务保持阻塞状态，直到：
        *   队列中有空间可用（另一个任务或ISR从队列中取走了一个数据项）。
        *   指定的阻塞时间超时。
    *   如果阻塞时间为 0 (`portMAX_DELAY` 表示无限等待，需配置 `configUSE_MUTEXES` 和 `INCLUDE_vTaskSuspend` 为 1)，任务会一直阻塞直到队列有空间。

#### 发送到队尾（FIFO） xQueueSend()/xQueueSendToBack()

函数原型：

```c
BaseType_t xQueueSend(
    QueueHandle_t xQueue,        // 队列句柄
    const void *pvItemToQueue,   // 指向要发送的数据
    TickType_t xTicksToWait      // 最大阻塞时间
);
```

```c
BaseType_t xQueueSendToBack(
    QueueHandle_t xQueue,
    const void *pvItemToQueue,
    TickType_t xTicksToWait
);
```

将数据添加到队列尾部（先进先出）

参数：

- xQueue：队列句柄
- pvItemToQueue：指向要发送的数据
- xTicksToWait：最大阻塞时间

返回值：

- `pdPASS`：发送成功
- `errQUEUE_FULL`：队列满且超时

**函数等价性**

- `xQueueSend()` 和 `xQueueSendToBack()` 在实现上是**完全相同的**
- 使用建议：
    - 新代码建议使用 `xQueueSendToBack()`（语义更明确）
    - 旧代码中 `xQueueSend()` 广泛使用（向后兼容）



#### `xQueueOverwrite()` - 覆盖发送

函数原型：

```c
BaseType_t xQueueOverwrite(
    QueueHandle_t xQueue,
    const void *pvItemToQueue
);
```

参数：

- 

返回值：

- 

### 数据接收函数（任务中使用）

#### `xQueueReceive()` - 接收并移除数据

函数原型：

```c
BaseType_t xQueueReceive(
    QueueHandle_t xQueue,
    void *pvBuffer,            // 接收数据缓冲区
    TickType_t xTicksToWait
);
```

参数：

- xQueue：目标队列句柄
- pvBuffer：接收数据缓冲区
- xTicksToWait：队列空时的最大等待时间

返回值：

- 

**接收阻塞：** 当任务调用 `xQueueReceive()` 从一个**空队列**接收数据时：

*   如果指定了阻塞时间（`xTicksToWait` > 0），任务会被挂起，并放入该队列的**接收阻塞列表**。
*   任务保持阻塞状态，直到：
    *   队列中有数据可用（另一个任务或ISR向队列发送了一个数据项）。
    *   指定的阻塞时间超时。
*   同样，阻塞时间可以是 0 或 `portMAX_DELAY`。

#### `xQueuePeek()` - 查看数据（不移除）

函数原型：

```c
BaseType_t xQueuePeek(
    QueueHandle_t xQueue,
    void *pvBuffer,
    TickType_t xTicksToWait
);
```

参数：

- 

返回值：

- 

### 中断安全版本（ISR中使用）

函数原型：

```c
// 发送到队尾
BaseType_t xQueueSendToBackFromISR(
    QueueHandle_t xQueue,
    const void *pvItemToQueue,
    BaseType_t *pxHigherPriorityTaskWoken
);

```

参数：

- 

返回值：

- 




函数原型：

```c
// 发送到队首
BaseType_t xQueueSendToFrontFromISR(
    QueueHandle_t xQueue,
    const void *pvItemToQueue,
    BaseType_t *pxHigherPriorityTaskWoken
);

```

参数：

- 

返回值：

- 




函数原型：

```c
// 覆盖发送
BaseType_t xQueueOverwriteFromISR(
    QueueHandle_t xQueue,
    const void *pvItemToQueue,
    BaseType_t *pxHigherPriorityTaskWoken
);
```

参数：

- 

返回值：

- 




函数原型：

```c
// 接收并移除数据
BaseType_t xQueueReceiveFromISR(
    QueueHandle_t xQueue,
    void *pvBuffer,
    BaseType_t *pxHigherPriorityTaskWoken
);


```

参数：

- 

返回值：

- 




函数原型：

```c
// 查看数据（不移除）
BaseType_t xQueuePeekFromISR(
    QueueHandle_t xQueue,
    void *pvBuffer
);
```

参数：

- 

返回值：

- 

### 队列管理函数


函数原型：

```c
// 获取队列中当前数据项数量（任务中使用）
UBaseType_t uxQueueMessagesWaiting(QueueHandle_t xQueue);


```

参数：

- 

返回值：

- 




函数原型：

```c
// 获取队列中当前数据项数量（ISR中使用）
UBaseType_t uxQueueMessagesWaitingFromISR(QueueHandle_t xQueue);


```

参数：

- 

返回值：

- 




函数原型：

```c
// 获取队列剩余空间（任务中使用）
UBaseType_t uxQueueSpacesAvailable(QueueHandle_t xQueue);
```

参数：

- 

返回值：

- 

#### 队列重置


函数原型：

```c
BaseType_t xQueueReset(QueueHandle_t xQueue);
```

参数：

- 

返回值：

- 

#### 队列删除


函数原型：

```c
void vQueueDelete(QueueHandle_t xQueue);
```

参数：

- 

返回值：

- 

### 高级功能函数

#### 注册队列集


函数原型：

```c
#if configUSE_QUEUE_SETS == 1
void vQueueAddToRegistry(
    QueueHandle_t xQueue,
    const char *pcQueueName
);
#endif
```

参数：

- 

返回值：

- 

#### 队列锁定/解锁


函数原型：

```c
void vQueueUnregisterQueue(QueueHandle_t xQueue);
```

参数：

- 

返回值：

- 















1.  **创建队列：**

    *   使用 `xQueueCreate()` 函数创建队列。
    *   需要指定两个关键参数：
        *   `uxQueueLength`: 队列的长度（能容纳的最大数据项数量）。
        *   `uxItemSize`: 每个队列项（数据项）的大小（以字节为单位）。
    *   创建成功返回一个 `QueueHandle_t` 类型的句柄（类似指针），用于后续操作该队列；失败返回 `NULL`。
    *   *示例：* `QueueHandle_t xSensorQueue = xQueueCreate(10, sizeof(SensorData_t));` 创建一个能存放 10 个 `SensorData_t` 结构体数据的队列。

2.  *   *   *

3.  **阻塞机制：**

    *   **发送阻塞：** 当任务调用 `xQueueSend()` 或 `xQueueSendToBack()` (等同于 `xQueueSend()`) 或 `xQueueSendToFront()` 向一个**满队列**发送数据时：
        *   如果指定了阻塞时间（`xTicksToWait` > 0），任务会被**挂起**（进入阻塞状态），并放入该队列的**发送阻塞列表**。
        *   任务保持阻塞状态，直到：
            *   队列中有空间可用（另一个任务或ISR从队列中取走了一个数据项）。
            *   指定的阻塞时间超时。
        *   如果阻塞时间为 0 (`portMAX_DELAY` 表示无限等待，需配置 `configUSE_MUTEXES` 和 `INCLUDE_vTaskSuspend` 为 1)，任务会一直阻塞直到队列有空间。
    *   **接收阻塞：** 当任务调用 `xQueueReceive()` 从一个**空队列**接收数据时：
        *   如果指定了阻塞时间（`xTicksToWait` > 0），任务会被挂起，并放入该队列的**接收阻塞列表**。
        *   任务保持阻塞状态，直到：
            *   队列中有数据可用（另一个任务或ISR向队列发送了一个数据项）。
            *   指定的阻塞时间超时。
        *   同样，阻塞时间可以是 0 或 `portMAX_DELAY`。
    *   **阻塞列表与调度器：** 当数据被发送到一个有任务在接收阻塞列表等待的队列，或者从队列中移除一个数据项使得队列不再满（有任务在发送阻塞列表等待）时，调度器会将被阻塞的任务中**优先级最高**的任务解除阻塞（移回就绪列表）。如果解除阻塞的任务优先级比当前运行任务高，会发生**上下文切换**。

4.  **中断服务程序（ISR）中使用队列：**

    *   ISR **绝对不能阻塞**。
    *   FreeRTOS 提供了专门的、以 `FromISR` 结尾的队列 API:
        *   `xQueueSendToBackFromISR()`
        *   `xQueueSendToFrontFromISR()`
        *   `xQueueReceiveFromISR()`
        *   `xQueuePeekFromISR()` (查看但不移除队列头数据)
    *   这些函数**不会阻塞**。如果操作失败（如向满队列发送或从空队列接收），它们会立即返回一个错误码（如 `errQUEUE_FULL`, `errQUEUE_EMPTY`）。
    *   这些函数通常需要一个指向 `BaseType_t` 类型变量的指针作为参数（`pxHigherPriorityTaskWoken`）。如果在 ISR 中操作队列导致了一个任务被解除阻塞，并且这个被解除阻塞的任务优先级**高于**当前运行的任务（即在 ISR 退出后将要返回的那个任务），那么这个函数会把 `pxHigherPriorityTaskWoken` 设置为 `pdTRUE`。**强烈建议**在 ISR 退出前检查这个值，如果为 `pdTRUE`，应调用 `portYIELD_FROM_ISR()` 或 `portEND_SWITCHING_ISR()` 来请求一次上下文切换，让更高优先级的任务立即运行。

