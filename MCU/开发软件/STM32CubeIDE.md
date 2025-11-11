# STM32CubeIDE

## 新建工程（点灯为例）

1. 点击左上角 File -> New -> STM32Project 进入创建新工程界面。
2. 搜索对应型号。点击下一步。
3. 工程命名，下一步，YES。
4. 选中 GPIOB-Pin2，选择 GPIO_Output 配置为输出模式。
5. 选中 System Core，找到 GPIOB-Pin2，修改默认电平/速度/上拉|下拉。
6. 保存。
7. 小锤子图标编译。
8. 绿色播放按钮下载（DeBug按钮左边）。

## 生成HAL库目录

```
| -- .settings 意义不明
| -- Core 存放核心代码
|    | -- Inc 存放核心代码的.h文件
|    |    | -- main.h
|    |    | -- stm32f4xx_hal_conf.h
|    |    | -- stm32f4xx_it.h
|    | -- Src 存放核心代码的.c文件
|    |    | -- main.c
|    |    | -- stm32f1xx_hal_msp.c
|    |    | -- stm32f1xx_it.c
|    |    | -- syscalls.c
|    |    | -- sysmem.c
|    |    | -- system_stm32f1xx.c
|    | -- Startup 存放启动文件
|    |    | -- startup_stm32f103zetx.s
| -- Drivers 存放外设驱动函数
|    | -- CMSIS CMSIS级别
|    |    | -- Device 驱动函数
|    |    |    | -- ST ST代码
|    |    |    |    | -- STM32F1xx STM32F1xx系列
|    |    |    |    |    | -- Include 头文件
|    |    |    |    |    |    | -- stm32f1xx.h
|    |    |    |    |    |    | -- stm32f103xe.h
|    |    |    |    |    |    | -- system_stm32f1xx.h
|    |    | -- Include 一系列头文件
|    |    |    | -- core_cm3.h
|    |    |    | -- ......
|    | -- STM32F1xx_HAL_Driver 
|    | -- Inc 存放内置外设驱动的.h文件
|    | -- Src 存放内置外设驱动的.c文件
```

## HAL

