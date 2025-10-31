# 低功耗函数API

## 必需的配置宏

在 `FreeRTOSConfig.h` 中必须定义以下宏来启用低功耗功能：

| 配置宏                                | 说明                                                         |
| :------------------------------------ | :----------------------------------------------------------- |
| configUSE_TICKLESS_IDLE               | 设置为 **1** 时，启用 **Tickless 低功耗模式**。              |
| configEXPECTED_IDLE_TIME_BEFORE_SLEEP | 设置进入低功耗模式的**最小空闲Tick数**，以避免频繁进出睡眠。通常建议≥2。 |
| configUSE_IDLE_HOOK                   | 设置为 **1** 时，启用**空闲任务钩子函数**，以便在其中插入低功耗代码。 |
| configPRE_SLEEP_PROCESSING()          | **可选**宏。在进入低功耗**前**调用，用于关闭外设时钟等。     |
| configPOST_SLEEP_PROCESSING()         | **可选**宏。在退出低功耗**后**调用，用于重新开启外设等。     |



### 关闭外设宏详解configPRE_SLEEP_PROCESSING

#### 函数原型

```c
void vPreSleepProcessing( TickType_t xExpectedIdleTime );
```

#### 参数说明

- `xExpectedIdleTime`：预期的空闲时间（单位：Tick）
    - 可用于决定进入何种低功耗模式
    - 短时间：浅度睡眠
    - 长时间：深度睡眠

#### 主要职责

##### 外设时钟管理

-   根据睡眠时间决定电源策略
-   长时间睡眠：关闭不必要的外设时钟
-   短时间睡眠：仅关闭部分外设



##### GPIO 配置优化

-   配置未使用的GPIO为模拟模式以降低功耗
-   但保持唤醒相关GPIO的配置

##### 系统时钟降频

-   保存当前时钟配置
-   降低系统时钟频率以进一步降低功耗
-   切换到低速时钟



### 开启外设宏详解configPOST_SLEEP_PROCESSING

#### 函数原型

```c
void vPostSleepProcessing( TickType_t xExpectedIdleTime );
```

#### 主要职责

##### 系统时钟恢复

-   从低功耗模式唤醒后，需要恢复系统时钟
-   重新配置系统时钟到正常频率
-   重新初始化滴答定时器



##### 外设时钟重新启用

-   重新启用必要的外设时钟
-   重新配置GPIO
-   重新初始化通信接口



##### 外设状态恢复

-   恢复通信接口
-   清除可能的错误状态



### 宏定义基本格式

```c
// 在 FreeRTOSConfig.h 中定义
#define configPRE_SLEEP_PROCESSING(xExpectedIdleTime)  vPreSleepProcessing( xExpectedIdleTime )
#define configPOST_SLEEP_PROCESSING(xExpectedIdleTime) vPostSleepProcessing( xExpectedIdleTime )

// 对应的函数实现
void vPreSleepProcessing( TickType_t xExpectedIdleTime );
void vPostSleepProcessing( TickType_t xExpectedIdleTime );
```



## Tickless 核心函数 `eTaskConfirmSleepModeStatus()`

这是一个在Tickless模式下至关重要的函数。它在 `portSUPPRESS_TICKS_AND_SLEEP()` 中被调用，用于**最终确认**是否可以进入睡眠以及进入何种睡眠模式。

- **返回值**：
    - `eAbortSleep`: 有任务就绪，**不得进入**睡眠。
    - `eNoTasksWaitingTimeout`: 无任务等待，可进入**深度睡眠**且无需定时器唤醒。
- `eStandardSleep`: 其他情况，进入**标准睡眠**，需定时器在指定时间唤醒。



## 传统低功耗函数 `vApplicationIdleHook()`

这是用户必须实现的**空闲任务钩子函数**。当没有其他用户任务运行时，系统会执行空闲任务并循环调用此函数。

```c
void vApplicationIdleHook( void )
{
    /* 在此处直接让处理器进入低功耗模式，例如对于ARM Cortex-M */
    __asm volatile( "WFI" ); /* 使用 WFI (等待中断) 指令 */
    // 或者使用具体的硬件低功耗指令，如MSP430的：__bis_SR_register(LPM3_bits + GIE);
}
```
**注意**：使用此方法时，系统滴答定时器（SysTick）会周期性中断唤醒CPU，因此无法实现极低功耗。



## 使用案例

### 低功耗上下文结构

```c
typedef struct {
    uint32_t previousClockConfig;
    uint32_t gpioAState;
    uint32_t gpioBState;
    uint8_t sleepMode;
} LowPowerContext_t;
static LowPowerContext_t xLowPowerContext;
```



### 进入低功耗前的处理 vPreSleepProcessing

```c
void vPreSleepProcessing(TickType_t xExpectedIdleTime)
{
    // 根据预期空闲时间选择睡眠模式
    if(xExpectedIdleTime > 50) {
        // 长时间空闲：进入停止模式
        xLowPowerContext.sleepMode = 2; // Stop mode
        
        // 保存当前时钟配置
        xLowPowerContext.previousClockConfig = RCC->CFGR;
        
        // 关闭不必要的外设时钟
        __HAL_RCC_USART1_CLK_DISABLE();
        __HAL_RCC_USART2_CLK_DISABLE();
        __HAL_RCC_SPI1_CLK_DISABLE();
        __HAL_RCC_I2C1_CLK_DISABLE();
        
        // 配置调试接口（保持调试器连接）
        __HAL_DBGMCU_FREEZE_TIM2();
        __HAL_DBGMCU_FREEZE_TIM3();
        __HAL_DBGMCU_FREEZE_TIM4();
        
    } else if(xExpectedIdleTime > 5) {
        // 中等时间空闲：进入睡眠模式
        xLowPowerContext.sleepMode = 1; // Sleep mode
        HAL_SuspendTick();  // 暂停SysTick
        
    } else {
        // 短时间空闲：不进入低功耗
        xLowPowerContext.sleepMode = 0;
        return;
    }
    
    // 配置未使用的GPIO为模拟输入以降低功耗
    ConfigureUnusedGPIOsForLowPower();
}
```



### 退出低功耗后的处理 vPostSleepProcessing

```c
void vPostSleepProcessing(TickType_t xExpectedIdleTime)
{
    if(xLowPowerContext.sleepMode == 0) {
        return;
    }
    
    if(xLowPowerContext.sleepMode == 2) {
        // 停止模式唤醒后的处理
        SystemClock_Config();  // 重新配置系统时钟
        SystemCoreClockUpdate();
        
        // 重新启用外设时钟
        __HAL_RCC_USART1_CLK_ENABLE();
        __HAL_RCC_USART2_CLK_ENABLE();
        __HAL_RCC_SPI1_CLK_ENABLE();
        __HAL_RCC_I2C1_CLK_ENABLE();
        
        // 恢复调试接口
        __HAL_DBGMCU_UNFREEZE_TIM2();
        __HAL_DBGMCU_UNFREEZE_TIM3();
        __HAL_DBGMCU_UNFREEZE_TIM4();
        
    } else if(xLowPowerContext.sleepMode == 1) {
        // 睡眠模式唤醒后的处理
        HAL_ResumeTick();  // 恢复SysTick
    }
    
    // 恢复GPIO配置
    RestoreGPIOConfiguration();
}
```



### 配置未使用的GPIO为低功耗模式

```c
void ConfigureUnusedGPIOsForLowPower(void)
{
    GPIO_InitTypeDef GPIO_InitStruct = {0};
    
    // 保存GPIO状态
    xLowPowerContext.gpioAState = GPIOA->MODER;
    xLowPowerContext.gpioBState = GPIOB->MODER;
    
    // 配置为模拟模式（最低功耗）
    GPIO_InitStruct.Mode = GPIO_MODE_ANALOG;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    
    // 配置GPIOA（保留必要的引脚）
    GPIO_InitStruct.Pin = GPIO_PIN_All & ~(GPIO_PIN_13 | GPIO_PIN_14); // 保留SWD调试引脚
    HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);
    
    // 配置GPIOB
    GPIO_InitStruct.Pin = GPIO_PIN_All;
    HAL_GPIO_Init(GPIOB, &GPIO_InitStruct);
}
```



### 恢复GPIO配置

```c
void RestoreGPIOConfiguration(void)
{
    // 重新配置GPIOA
    GPIOA->MODER = xLowPowerContext.gpioAState;
    
    // 重新配置GPIOB  
    GPIOB->MODER = xLowPowerContext.gpioBState;
    
    // 重新配置LED引脚
    GPIO_InitTypeDef GPIO_InitStruct = {0};
    GPIO_InitStruct.Pin = GPIO_PIN_12 | GPIO_PIN_13 | GPIO_PIN_14 | GPIO_PIN_15;
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
    HAL_GPIO_Init(GPIOD, &GPIO_InitStruct);
}
```



## 启用流程

为避免出现争用情况， RTOS 调度器在 调用 portSUPPRESS_TICKS_AND_SLEEP（） 之前挂起， 并在 portSUPPRESS_TICKS_AND_SLEEP（） 完成时 恢复。这确保了在微控制器退出低功耗状态、 portSUPPRESS_TICKS_AND_SLEEP（） 完成执行之间，应用程序任务无法执行。 此外，portSUPPRESS_TICKS_AND_SLEEP（） 函数必须在停止滴答源和 微控制器进入休眠状态之间创建一个小的临界区。 应从该临界区调用 eTaskConfirmSleepModeStatus（） 。