# FreeRTOS目录

## FreeRTOS 9.0.0 文件目录

FreeRTOS 9.0.0 文件目录包括：

```
|-- FreeRTOS
|   |-- /Demo 案例，FreeRTOS为主流单片机写了案例代码。
|   |   |-- CORTEX_STM32F103_Keil 基于Keil的STM32F103
|   |   |   |-- 
|   |   |   |-- 
|   |   |-- Common 独立的通用代码，大多已废弃。
|   |-- /License 许可说明，不用于商业用途不用看。
|   |-- /Source 内核源码与移植层代码
|   |   |-- croutine 协程 过时了
|   |   |-- event_groups 事件组 过时了
|   |   |-- list 实现内核链表
|   |   |-- queue 队列、信号量、互斥锁
|   |   |-- stream_buffer
|   |   |-- tasks 任务操作
|   |   |-- timers 软件定时器
|   |   |-- /include 通用头文件
|   |   |   |--
|   |   |   |--
|   |   |-- /portable 不同编译器使用的不同支持文件
|   |   |   |-- /MemMang 内存管理函数
|   |   |   |   |-- heap1~5 必须使用一个，初学使用heap4.c。
|   |   |   |-- /KEIL 存放KEIL使用的支持文件，与RVDS相同，故此处仅指向RVDS，无实际文件。
|   |   |   |-- /RVDS 存放RVDS使用的支持文件，与KEIL相同。
|   |   |   |   |-- ARM_CM0
|   |   |   |   |-- ARM_CM3 STM32F1XX系列ARM内核
|   |   |   |   |-- ARM_CM4 STM32F4XX系列ARM内核
|   |-- Test 
|-- FreeRTOS-Plus 生态文件，非必须。
|-- 基本介绍和网站链接
```



## FreeRTOS在项目文件夹里

-   **/include：**
-   FreeRTOS在include文件夹下所有文件
    
-   **/portable：**

    -   MemMang
        -   heap_[1~5].c
    -   RVDS
        -   ARM_CM4F：根据MCU型号决定。
            -   port.c
            -   portmacro.h

-   **/source：**
-   croutine.c
    -   event_groups.c
    -   list.c
    -   queue.c
    -   stream_buffer.c
    
-   tasks.c
    -   timers.c
    
-   FreeRTOSConfig.h





## FreeRTOS在Keil里

-   FreeRTOS/Portable
    -   heap_[1~5].c
    -   port.c
    -   FreeRTOSConfig.h

-   FreeRTOS/Source

    -   croutine.c
    -   event_groups.c
    -   list.c
    -   queue.c
    -   stream_buffer.c

    -   tasks.c
    -   timers.c

