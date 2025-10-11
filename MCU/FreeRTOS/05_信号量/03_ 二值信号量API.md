# 创建二值信号量

## 动态创建二值信号量 xSemaphoreCreateBinary()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateBinary(void);
```

**参数**：

- 无

**返回值**：

- 成功：信号量句柄（`SemaphoreHandle_t` 类型）
- 失败：NULL



## 静态创建二值信号量 xSemaphoreCreateBinaryStatic()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateBinaryStatic(  
    StaticSemaphore_t *pxSemaphoreBuffer  
);  
```

**参数**：

- pxSemaphoreBuffer：指向用户分配的 `StaticSemaphore_t` 类型内存块

**返回值**：

- 成功：信号量句柄
- 失败：NULL