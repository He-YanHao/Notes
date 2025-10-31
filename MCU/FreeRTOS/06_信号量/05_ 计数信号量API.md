# 创建计数信号量

## 动态创建 xSemaphoreCreateCounting()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateCounting(
    UBaseType_t uxMaxCount,   // 最大计数值
    UBaseType_t uxInitialCount // 初始计数值
);
```

**参数**：

- uxMaxCount：最大计数值
- uxInitialCount：初始计数值

**返回值**：

- 
- 



## 静态创建 xSemaphoreCreateCountingStatic()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateCountingStatic(
    UBaseType_t uxMaxCount,
    UBaseType_t uxInitialCount,
    StaticSemaphore_t *pxSemaphoreBuffer
);
```

**参数**：

- uxMaxCount：
- uxInitialCount：
- pxSemaphoreBuffer：

**返回值**：

- 
- 