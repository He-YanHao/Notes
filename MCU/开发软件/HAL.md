# HAL

## STM32Cube_FW_F1_V1.8.0文件目录

```
| -- _htmresc 一些log
| -- Documentation 说明文档
| -- Drivers 其他组件
|    | -- BSP 案例板板级支持包
|    | -- CMSIS ARM级内核
|    |    | -- Core 内核
|    |    |    | -- Include 代码
|    |    |    |    | -- cmsis_armcc.h  ARM Compiler 5(armcc) 的特定实现
|    |    |    |    | -- cmsis_armclang.h  ARM Compiler 6(armclang) 的特定实现
|    |    |    |    | -- cmsis_compiler.h  编译器抽象层（核心文件） 在这里定义了uint16_t
|    |    |    |    | -- cmsis_version.h  定义 CMSIS 版本信息
|    |    |    |    | -- core_armv8mbl.h  TrustZone基础版 物联网终端
|    |    |    |    | -- core_armv8mml.h  TrustZone增强版 边缘计算
|    |    |    |    | -- core_cm3.h  Cortex-M3处理器核心访问层
|    |    |    |    | -- core_cm4.h  Cortex-M4处理器核心访问层 提供兼容性
|    |    |    |    | -- core_cm7.h  Cortex-M7处理器核心访问层 提供兼容性
|    |    |    |    | -- core_sc000  基于Cortex-M0架构的安全关键应用的处理器核心
|    |    |    |    | -- core_sc300  基于Cortex-M3架构的安全关键应用的处理器核心
|    |    |    |    | -- mpu_armv7.h  ARMv7内存保护单元 仅F1高级型号
|    |    |    |    | -- mpu_armv8.h  ARMv8内存保护单元 不需要
|    |    |    |    | -- tz_context.h  TrustZone上下文管理 不需要
|    | -- STM32F1xx_HAL_Driver 内部外设
|    |    | -- Inc 存放核心代码的.h文件
|    |    | -- Src 存放核心代码的.c文件
| -- Middlewares 额外组件支持 STemWim USB FreeRTOS等
| -- Projects 实例工程
| -- Utilities
STM32Cube_FW_F1_V1.8.0\Drivers\CMSIS\Core\Include
```

新建工程文件目录

```
| -- Core 内核和启动文件
| -- MDK-ARM 存放工程
| -- STM32F1xx_HAL_Driver 内部外设驱动
```

