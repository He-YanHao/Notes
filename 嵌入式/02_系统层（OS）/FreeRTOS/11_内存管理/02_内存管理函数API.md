### 内存分配函数

```c
// 内存分配API示例
void *pvPortMalloc(size_t xWantedSize);  // 分配内存
void vPortFree(void *pv);                // 释放内存
size_t xPortGetFreeHeapSize(void);       // 获取当前空闲堆大小
```

