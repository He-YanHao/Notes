# 添加FreeRTOS步骤

1. 先创建没有FreeRTOS的项目。

2. 复制FreeRTOS核心目录中的include文件夹放到到项目里。

3. 根据自己的平台，复制FreeRTOS核心目录中的portable文件夹对应的文件夹和MemMang文件夹放到到项目里。

4. 案例中找到合适的 ` FreeRTOSConfig.h ` 放到到项目里。

5. ` FreeRTOSConfig.h `里面的宏 和`stm32f10x_it.c`里的中断函数名重复，需要注释掉`stm32f10x_it.c`里的中断函数。

6. 添加工程文件FreeRTOS/Source内核和FreeRTOS/Portable接口，并添加文件。

   > | **堆管理方案** | **特点**                   | **适用场景**              | **configTOTAL_HEAP_SIZE 作用** |
   > | :------------- | :------------------------- | :------------------------ | :----------------------------- |
   > | heap_1.c       | 静态分配，不支持释放       | 极简系统，无动态删除需求  | 定义初始固定内存池             |
   > | heap_2.c       | 最佳匹配算法，会产生碎片   | 已废弃，不推荐使用        | 决定可分配总空间               |
   > | heap_3.c       | 封装标准 malloc/free       | 需要与标准库共存的环境    | 被忽略（直接使用系统堆）       |
   > | heap_4.c       | 首次适应算法，支持碎片合并 | 通用嵌入式系统（推荐）    | 定义专用内存池大小             |
   > | heap_5.c       | 支持非连续内存区域合并     | 复杂内存架构（如多RAM区） | 定义多个内存区域的总和         |

7. 添加头文件地址：

   -  ` .\FreeRTOS\include ` 

   -  ` .\FreeRTOS\port\RVDS\ARM_CM3 ` 

8. 修改 ` FreeRTOSConfig.h ` ，确保主频一致。 

9.  重写Delay

    ```c
    //直接使用FreeRTOS API（推荐）
    
    // 替换原来的HAL_Delay(100)为：
    vTaskDelay(pdMS_TO_TICKS(100));
    
    // 替换原来的delay_ms(100)为：
    vTaskDelay(pdMS_TO_TICKS(100));
    
    
    
    ```

10.  



注意

裸机的delay和rtos的delay并不一样。
