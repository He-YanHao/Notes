# FreeRTOS目录

## FreeRTOS 9.0.0 文件目录

FreeRTOS 9.0.0 文件目录包括：

```
|-- FreeRTOS
|   |-- Demo 案例，FreeRTOS为主流单片机写了案例代码。
|   |   |-- CORTEX_STM32F103_Keil 基于Keil的STM32F103
|   |   |   |-- 
|   |   |   |-- 
|   |   |-- Common 独立的通用代码，大多已废弃。
|   |-- License 许可说明，不用于商业用途不用看。
|   |-- Source 内核源码与移植层代码
|   |   |-- croutine 协程 过时了
|   |   |-- event_groups 事件组 过时了
|   |   |-- list 实现内核链表
|   |   |-- queue 队列、信号量、互斥锁
|   |   |-- stream_buffer
|   |   |-- tasks 任务操作
|   |   |-- timers 软件定时器
|   |   |-- include 通用头文件
|   |   |   |--
|   |   |   |--
|   |   |-- portable 不同编译器使用的不同支持文件
|   |   |   |-- MemMang 内存管理函数
|   |   |   |   |-- heap1~5 必须使用一个，初学使用heap4.c。
|   |   |   |-- KEIL 存放KEIL使用的支持文件，与RVDS相同，故此处仅指向RVDS，无实际文件。
|   |   |   |-- RVDS 存放RVDS使用的支持文件，与KEIL相同。
|   |   |   |   |-- ARM_CM0
|   |   |   |   |-- ARM_CM3 STM32F1XX系列ARM内核
|   |   |   |   |-- ARM_CM4 STM32F4XX系列ARM内核
|   |-- Test 
|-- FreeRTOS-Plus 生态文件，非必须。
|-- 基本介绍和网站链接
```

## FreeRTOS核心目录

- FreeRTOS：
  - src：保存 FreeRTOS 中的核心源文件，来自 ` FreeRTOSv9.0.0\FreeRTOS\Source ` 下的所以.c文件。
  - port：
    - MemMang：内存管理相关代码，来自 ` FreeRTOSv9.0.0\FreeRTOS\Source\portable ` 下的 ` MemMang ` 文件夹
    - RVDS：处理器架构相关代码，来自 ` FreeRTOSv9.0.0\FreeRTOS\Source\portable ` 下的 ` RVDS ` 文件夹
  - include：FreeRTOS 的一些头文件来自 ` FreeRTOSv9.0.0\FreeRTOS\Source ` 的 include 文件夹，直接整个复制过来。
- user：存放用户主函数； ` FreeRTOSv9.0.0\FreeRTOS\Demo\符合自己型号的案例 ` 文件夹下的 ` FreeRTOSConfig.h ` 存放到user里。