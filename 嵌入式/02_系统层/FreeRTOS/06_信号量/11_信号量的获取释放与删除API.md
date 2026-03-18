# 信号量的获取释放与删除

## 获取

### 通用获取 xSemaphoreTake()

函数原型：

```c
BaseType_t xSemaphoreTake(  
    SemaphoreHandle_t xSemaphore,  // 信号量句柄  
    TickType_t xTicksToWait        // 最大阻塞时间  
);
```

**参数**：

- xSemaphore：信号量句柄

- xTicksToWait：

    - `0`：立即返回
    - `portMAX_DELAY`：无限等待

    - N：等待N个tick

**返回值**：

- **`pdTRUE`**：获取成功
- **`pdFALSE`**：获取失败（超时或信号量不可用）

- 

### 中断安全获取 xSemaphoreTakeFromISR()

函数原型：

```c
// 中断安全获取（仅用于二值/计数信号量）
BaseType_t xSemaphoreTakeFromISR(
    SemaphoreHandle_t xSemaphore,
    BaseType_t *pxHigherPriorityTaskWoken // 输出是否需触发上下文切换
);
```

**参数**：

- 

**返回值**：

- 
- 



## 释放

### 通用释放 xSemaphoreGive()

函数原型：

```c
// 通用释放
BaseType_t xSemaphoreGive(SemaphoreHandle_t xSemaphore);
```

**参数**：

- xSemaphore：信号量句柄

**返回值**：

- **`pdPASS`**：释放成功
- **`pdFAIL`**：释放失败

- 

### 中断安全释放 xSemaphoreGiveFromISR()

函数原型：

```c
// 中断安全释放（仅用于二值/计数信号量）
BaseType_t xSemaphoreGiveFromISR(
    SemaphoreHandle_t xSemaphore,
    BaseType_t *pxHigherPriorityTaskWoken
);
```

**参数**：

- 

**返回值**：

- 
- 



## 删除

### 删除 vSemaphoreDelete()

函数原型：

```c
void vSemaphoreDelete(SemaphoreHandle_t xSemaphore);
```

**参数**：

- 

**返回值**：

- 
- 

