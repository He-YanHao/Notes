# 任务调度相关函数API

## 任务管理函数

| 函数作用                                   | 函数原型                                                     |
| ------------------------------------------ | ------------------------------------------------------------ |
| 获取任务优先级                             | UBaseType_t uxTaskPriorityGet( TaskHandle_t xTask );         |
| 修改任务优先级                             | void vTaskPrioritySet( TaskHandle_t xTask, UBaseType_t uxNewPriority); |
| 获取当前任务句柄                           | TaskHandle_t xTaskGetCurrentTaskHandle( void );              |
| 通过任务名获取句柄                         | TaskHandle_t xTaskGetHandle( const char *pcNameToQuery );    |
| 获取任务数量                               | UBaseType_t uxTaskGetNumberOfTasks( void );                  |
| 获取堆栈高水位标记（历史最小剩余堆栈空间） | UBaseType_t uxTaskGetStackHighWaterMark( TaskHandle_t xTask ); |
| 获取任务当前状态                           | eTaskState eTaskGetState( TaskHandle_t xTask );              |
| 获取任务详细信息                           | void vTaskGetInfo( <br/>    TaskHandle_t xTask,<br/>    TaskStatus_t *pxTaskStatus,<br/>    BaseType_t xGetFreeStackSpace,<br/>    eTaskState eState <br/>); |
| 获取所有任务状态信息                       | void vTaskPrioritySet(TaskHandle_t xTask, UBaseType_t uxNewPriority); |
| 生成所有任务的状态概览                     | vTaskList                                                    |
| 获取任务的运行时间                         | void vTaskGetRunTimeStats(char *pcWriteBuffer);              |

## 获取任务优先级 uxTaskPriorityGet



## 修改任务优先级 vTaskPrioritySet



## 获取当前任务句柄 xTaskGetCurrentTaskHandle



## 通过任务名获取句柄 xTaskGetHandle



## 获取任务数量 uxTaskGetNumberOfTasks





## 获取任务当前状态 eTaskGetState

`eTaskGetState()` 是 FreeRTOS 提供的一个关键 API 函数，用于查询指定任务的当前状态。

通过 `eTaskState eState = eTaskGetState(句柄)` 获取任务状态后打印输出。

| 枚举值       | 数值 | 状态描述                                           |
| :----------- | :--- | :------------------------------------------------- |
| `eRunning`   | 0    | 任务正在运行（当前正在执行的任务）                 |
| `eReady`     | 1    | 任务处于就绪状态（准备运行，等待调度）             |
| `eBlocked`   | 2    | 任务处于阻塞状态（调用了 vTaskDelay() 等阻塞函数） |
| `eSuspended` | 3    | 任务被挂起（调用了 vTaskSuspend()）                |
| `eDeleted`   | 4    | 任务已被删除（调用了 vTaskDelete()）               |
| `eInvalid`   | 5    | 无效状态（任务句柄错误或任务不存在）               |



## 生成所有任务的状态概览  vTaskList;

在 **03_系统运行调试输出任务信息** 。



## 获取任务的运行时间  void vTaskGetRunTimeStats(char *pcWriteBuffer);

在 **03_系统运行调试输出任务信息** 。

