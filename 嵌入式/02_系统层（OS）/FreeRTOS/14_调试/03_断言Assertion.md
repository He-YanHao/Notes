# 断言Assertion

*   **FreeRTOS 内置 `configASSERT()`:** 这是**极其重要**的调试手段。在 `FreeRTOSConfig.h` 中定义 `configASSERT(x)` 宏。当参数 `x` 为假时触发，通常用于捕获非法状态。
*   **常见使用点:**
    *   任务栈溢出检测 (`uxTaskGetStackHighWaterMark()` 返回值接近 0)。
    *   无效句柄 (NULL 队列、信号量句柄)。
    *   在中断服务程序 (ISR) 中调用了不允许的 API (以 `FromISR` 结尾的除外)。
    *   队列操作返回错误 (如发送满队列、接收空队列且阻塞时间为 0)。
    *   内存分配失败 (`pvPortMalloc` 返回 NULL)。
*   **实现:** `configASSERT()` 内部通常调用一个死循环或触发断点，便于你定位问题代码。