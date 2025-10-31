# 创建递归互斥量信号量

## 动态创建 xSemaphoreCreateRecursiveMutex()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateRecursiveMutex();
```

**参数**：

- 

**返回值**：

- 
- 



## 静态创建 xSemaphoreCreateRecursiveMutexStatic()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateRecursiveMutexStatic(StaticSemaphore_t *pxMutexBuffer);
```

**参数**：

- 

**返回值**：

- 
- 

- 





## 递归互斥量专属获取 xSemaphoreTakeRecursive()

函数原型：

```c
//递归互斥量专属获取
BaseType_t xSemaphoreTakeRecursive(
    SemaphoreHandle_t xMutex,
    TickType_t xBlockTime
);
```

**参数**：

- 

**返回值**：

- 
- 



#### 递归互斥量专属释放 xSemaphoreGiveRecursive()

函数原型：

```c
// 递归互斥量专属释放
BaseType_t xSemaphoreGiveRecursive(SemaphoreHandle_t xMutex);
```

**参数**：

- 

**返回值**：

- 
- 
