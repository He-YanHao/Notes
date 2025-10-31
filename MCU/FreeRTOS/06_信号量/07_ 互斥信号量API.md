# 创建互斥量信号量

## 动态创建 xSemaphoreCreateMutex()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateMutex();//（支持优先级继承）
```

**参数**：

- 

**返回值**：

- 
- 



## 静态创建 xSemaphoreCreateMutexStatic()

函数原型：

```c
SemaphoreHandle_t xSemaphoreCreateMutexStatic(StaticSemaphore_t *pxMutexBuffer);
```

**参数**：

- 

**返回值**：

- 
- 