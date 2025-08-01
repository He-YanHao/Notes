# 运行FreeRTOS

## 初始

无论如何，函数都会先进入 `main();` 开始运行，在运行FreeRTOS相关函数前，和裸机一致。

## 创建任务

### 被调度的任务函数介绍

FreeRTOS任务要求

1. 必须为无限循环（除非任务自删）。
2. 任务优先级数值越大优先级越高。
3. 堆栈大小需根据任务复杂度（局部变量、函数调用深度）动态调整。

```c
void task(void *pvParameters)
{
	//任务函数
	while(1);//在此停住
}
```

```c
void task(void *pvParameters)
{
	while(1)//{}内无限循环
	{
		//任务函数
	}
}
```

```c
void task(void *pvParameters)
{
    //任务函数
	for( ; ; )//无限循环
	{
	}
}
```

其中 `*pvParameters` 为参数。

### 创建任务函数

| API函数 | xTaskCreate()                                                | xTaskCreateStatic()                                          |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 描述    | 动态方式创建任务                                             | 静态方式创建任务                                             |
| 特点    | 任务的任务控制块以及任务的栈空间所需的内存，均由 FreeRTOS  从 FreeRTOS 管理的堆中分配。 | 任务的任务控制块以及任务的栈空间所需的内存，需用户分配提 供。 |
|         |                                                              |                                                              |
|         |                                                              |                                                              |

### 动态方式创建任务 xTaskCreate()  

**xTaskCreate函数结构：**

```c
BaseType_t xTaskCreate
//BaseType_t：BaseType_t为返回值，16位系统为int16_t，32位为int32_t。
//xTaskCreate：x表示返回值为非void类型 Task表示操作对象为任务 Create表示动态创建行为。
(
    TaskFunction_t pxTaskCode,//任务入口函数（永不返回的函数）
    //为函数指针类型，指向任务。void (*)(void*) 函数指针类型
    const char * const pcName,//指向任务函数的指针(任务名字，最大长度configMAX_TASK_NAME_LEN默认16字符)
    //指向只读变量的只读指针，只读变量为任务名字。
    const configSTACK_DEPTH_TYPE usStackDepth,//任务堆栈大小，默认单位4字节
    //configSTACK_DEPTH_TYPE为uint16_t或uint32_t（由FreeRTOSConfig.h定义）
    void * const pvParameters,//传递给任务函数的参数
    //申请一个结构体，把参数全部包含进去，再传递给任务。
    UBaseType_t uxPriority,//任务优先级，范围：0 ~configMAX_PRIORITIES - 1
    //UBaseType_t为uint16_t或uint32_t，取决于架构。
    TaskHandle_t * const pxCreatedTask//任务句柄，就是任务的任务控制块
    //用于操作任务（如删除、挂起）。
);
```

> 为什么不直接用入口函数的名字作为任务名，而是另外取一个？
>
> 答：多任务可能共享同一入口函数，导致无法区分。
>
> 入口函数的名字的不可修改，且仅是指向一处地址，运行后无意义。
>
> 任务名的作用是便于人阅读，且是可以修改的和使用串口输出作为日志的。

**通过`xTaskCreate()` 函数创建任务：**

```c
//声明句柄
TaskHandle_t StartTask_Handler;
/* 1.创建一个启动任务 */
xTaskCreate((TaskFunction_t)start_task,               // 任务函数的地址，调用start_task()函数。
            (char *)"start_task",                     // 任务名字符串
            (configSTACK_DEPTH_TYPE)START_TASK_STACK, // 任务栈大小，默认最小128，单位4字节，此处为一般用宏定义。
            (void *)NULL,                             // 传递给任务的参数为空
            (UBaseType_t)START_TASK_PRIORITY,         // 任务的优先级为1（数值越大优先级越高），此处为一般用宏定义。
            (TaskHandle_t *)&start_task_handle);      // 任务句柄的地址StartTask_Handler
/* 2.启动调度器:会自动创建空闲任务 */
vTaskStartScheduler();
```

返回值说明如下：

- pdPASS：任务创建成功。
- errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY：任务创建失败。 



### 静态方式创建任务 xTaskCreateStatic()  

**xTaskCreateStatic()  函数原型：**

```c
TaskHandle_t xTaskCreateStatic 
(  
    TaskFunction_t pxTaskCode,          /* 指向任务函数的指针 */ 
    const char * const pcName,          /* 任务函数名 */ 
    const uint32_t ulStackDepth,        /* 任务堆栈大小,单位是4字节 */ 
    void * const pvParameters,          /* 传递的任务函数参数 */ 
    UBaseType_t uxPriority,             /* 任务优先级 */ 
    StackType_t * const puxStackBuffer, /* 任务堆栈，一般为数组，由用户分
配 */ 
    StaticTask_t * const pxTaskBuffer   /* 任务控制块指针，由用户分配 */ 
)
```

返回值如下：

- NULL：用户没有提供相应的内存，任务创建失败。
- 其他值：任务句柄，任务创建成功。 

案例：

```c
/* 启动任务的配置 */
#define START_TASK_STACK    128      //配置堆栈
#define START_TASK_PRIORITY 1        //配置优先级
TaskHandle_t start_task_handle;      //声明句柄
StackType_t start_task_stack[START_TASK_STACK]; // 静态任务的任务栈，以数组形式存储
//typedef uint32_t StackType_t;  // 对于32位处理器
StaticTask_t start_task_tcb;                    // 静态任务的TCB结构体类型
//无需关心具体结果 储存任务细节
void start_task(void *pvParameters); //声明任务函数

//静态创建任务 使用xTaskCreateStatic()创建，并将返回值赋于句柄。
start_task_handle = xTaskCreateStatic(
                    vTaskCode,       // 任务函数地址
                    "TaskName",      // 任务名称
                    configMINIMAL_STACK_SIZE, // 栈大小
                    NULL,            // 参数
                    tskIDLE_PRIORITY,// 优先级
                    start_task_stack,      // 任务栈数组
                    &start_task_tcb );     // 任务控制块
```

静态创建方式，必须需要手动指定2个特殊任务的资源。

```c
/* 静态创建方式，需要手动指定2个特殊任务的资源 */
/* 空闲任务的配置 */
StackType_t idle_task_stack[configMINIMAL_STACK_SIZE]; // 静态任务的任务栈，以数组形式存储
StaticTask_t idle_task_tcb;                            // 静态任务的TCB结构体类型
/* 分配空闲任务的资源 */
void vApplicationGetIdleTaskMemory(StaticTask_t **ppxIdleTaskTCBBuffer,
                                   StackType_t **ppxIdleTaskStackBuffer,
                                   uint32_t *pulIdleTaskStackSize)
{
    *ppxIdleTaskTCBBuffer = &idle_task_tcb;
    *ppxIdleTaskStackBuffer = idle_task_stack;
    *pulIdleTaskStackSize = configMINIMAL_STACK_SIZE;
}

/* 软件定时器任务的配置 */
StackType_t timer_task_stack[configTIMER_TASK_STACK_DEPTH]; // 静态任务的任务栈，以数组形式存储
StaticTask_t timer_task_tcb;                            // 静态任务的TCB结构体类型
/* 分配软件定时器任务的资源 */
void vApplicationGetTimerTaskMemory(StaticTask_t **ppxTimerTaskTCBBuffer,
                                    StackType_t **ppxTimerTaskStackBuffer,
                                    uint32_t *pulTimerTaskStackSize)
{
    *ppxTimerTaskTCBBuffer = &timer_task_tcb;
    *ppxTimerTaskStackBuffer = timer_task_stack;
    *pulTimerTaskStackSize = configTIMER_TASK_STACK_DEPTH;
}
```

## 启动调度器

启动调度器，会自动创建空闲任务

```c
vTaskStartScheduler();
```

任务调度器仅需启动一次。

未启动调度器前任务不运行，无需保护共享资源。

## 删除任务

使用`vTaskDelete()`删除任务。

参数为任务句柄。

若为空(NULL)则为删除自身。

空闲任务会负责释放被删除任务中由系统分配的内存，但是由用户在 任务删除前申请的内存，则需要由用户在任务被删除前提前释放，否则将导致内存泄露。 

