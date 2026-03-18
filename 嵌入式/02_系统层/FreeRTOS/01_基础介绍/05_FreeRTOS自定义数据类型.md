# FreeRTOS自定义数据类型

FreeRTOS 通过自定义数据类型提高了代码的可移植性、可读性和安全性。这些数据类型主要分为 **基础类型**、**任务相关类型**、**同步/通信类型** 和 **配置依赖类型**。以下是分类详解：

## 基础数据类型
（定义在 `portmacro.h` 或 `FreeRTOS.h` 中，适配不同硬件架构）  
- **TickType_t**  
  - 系统节拍计数器类型，默认为 `uint32_t`（可配置为 `uint16_t` 以节省内存）。  
  - 用于时间相关函数（如 `vTaskDelay(100)` 中的 `100`）。  

- **BaseType_t** / **UBaseType_t**  
  - 有符号（`int`）和无符号（`unsigned int`）基础类型，通常与 CPU 字长一致（如 32 位架构下为 `int32_t`/`uint32_t`）。  
  - 用于优先级、返回值（如 `pdTRUE/pdFALSE`）和通用计数。  

- **StackType_t**  
  - 任务栈单元类型，通常为 `uint32_t`（32 位架构）。  

## 任务相关类型
- **TaskHandle_t**  
  - 任务句柄，本质是任务控制块（TCB）的抽象指针，用于操作任务（如删除、挂起）。  

- **TaskFunction_t**  
  - 任务入口函数类型，定义为 `void (*)(void *)`，强制统一函数签名。  

- **configSTACK_DEPTH_TYPE**  
  - 自定义栈深度类型（默认为 `uint16_t`），影响 `xTaskCreate()` 中栈大小的数值范围。  

## 同步/通信类型
- **QueueHandle_t** / **SemaphoreHandle_t**  
  - 队列和信号量的句柄，本质是内核对象的抽象指针。  
  - 信号量是队列的特例，故共用同一句柄类型。  

- **EventGroupHandle_t**  
  - 事件组句柄，用于多任务事件同步。  

- **StreamBufferHandle_t** / **MessageBufferHandle_t**  
  - 流缓冲区和消息缓冲区句柄，用于高效数据流传输。  

## 配置依赖类型
（通过 `FreeRTOSConfig.h` 自定义）  
- **TickType_t**  
  - 若 `configUSE_16_BIT_TICKS=1`，则强制为 `uint16_t`。  

- **UBaseType_t**  
  - 任务优先级类型，默认范围由 `configMAX_PRIORITIES` 决定（通常 0~31）。  

## 其他实用类型
- **StaticTask_t** / **StaticQueue_t**  
  - 静态分配任务或队列时的存储结构体，替代动态内存分配。  

- **List_t**  
  - 内核链表结构，用于任务调度等内部逻辑（用户通常不直接操作）。  

## 设计目标

1. **可移植性**：通过宏适配不同编译器（如 GCC、IAR）和架构（8/16/32 位）。  
2. **类型安全**：避免直接使用 `void*`，减少误用风险（如 `TaskHandle_t` 仅用于任务操作）。  
3. **资源控制**：明确数据范围（如 `TickType_t` 决定最大延时时间）。  

## 示例：类型使用场景  
```c
// 创建任务（使用 TaskHandle_t 和 TaskFunction_t）
TaskHandle_t xHandle;
xTaskCreate((TaskFunction_t)vTaskFunc, "Task", (configSTACK_DEPTH_TYPE)1024, NULL, (UBaseType_t)1, &xHandle);

// 延时（使用 TickType_t）
vTaskDelay((TickType_t)100 / portTICK_PERIOD_MS);
```
