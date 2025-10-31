# STM32F103C8T6库函数

## PWR电源控制

### 将外设PWR寄存器重设为缺省值

将外设PWR寄存器重设为缺省值，函数原型为：

void PWR_DeInit(void);

### 进入停止（STOP）模式

进入停止（STOP）模式，函数原型为：

void PWR_EnterSTOPMode(u32 PWR_Regulator, u8 PWR_STOPEntry);

- 参数1 PWR_Regulator:  电压转换器在停止模式下的状态，允许取值范围：
  - PWR_Regulator_ON	停止模式下电压转换器ON
  - PWR_Regulator_LowPower	停止模式下电压转换器进入低功耗模式

- 参数2 PWR_STOPEntry:  选择使用指令WFE还是WFI来进入停止模式，允许取值范围：
  - PWR_STOPEntry_WFI	使用指令WFI来进入停止模式
  - PWR_STOPEntry_WFE	使用指令WFE来进入停止模式

## BKP备份寄存器

### 使用PWR开启对备份寄存器的访问

函数原形 void PWR_BackupAccessCmd(FunctionalState NewState);

功能描述 使能或者失能 RTC 和后备寄存器访问 

- 输入参数 NewState: RTC 和后备寄存器访问的新状态。这个参数可以取：ENABLE 或者 DISABLE。

### 写入数据到备份寄存器

函数原型为：void BKP_WriteBackupRegister(u16 BKP_DR, u16 Data);

- 参数1 BKP_DR：要存入的备份寄存器的编号，可以取BKP_DR1~BKP_DR10；
- 参数2 Data：为16位要写入数据；

### 读取备份寄存器的数据

函数原型为：u16 BKP_ReadBackupRegister(u16 BKP_DR);

- 参数 BKP_DR：要存入的备份寄存器的编号，可以取BKP_DR1~BKP_DR10；
- 返回值：该寄存器储存的值。

## RCC时钟

### 检查指定的RCC标志位设置与否

检查指定的RCC标志位设置与否，原型为：

FlagStatus RCC_GetFlagStatus(u8 RCC_FLAG) ;

返回值为RCC_FLAG的新状态（SET或者RESET）。

参数为：

| RCC_FLAG         | 描述         |
| ---------------- | ------------ |
| RCC_FLAG_HSIRDY  | HSI 晶振就绪 |
| RCC_FLAG_HSERDY  | HSE 晶振就绪 |
| RCC_FLAG_PLLRDY  | PLL 就绪     |
| RCC_FLAG_LSERDY  | LSI 晶振就绪 |
| RCC_FLAG_LSIRDY  | LSE 晶振就绪 |
| RCC_FLAG_PINRST  | 管脚复位     |
| RCC_FLAG_PORRST  | POR/PDR 复位 |
| RCC_FLAG_SFTRST  | 软件复位     |
| RCC_FLAG_IWDGRST | IWDG复位     |
| RCC_FLAG_WWDGRST | WWDG复位     |
| RCC_FLAG_LPWRRST | 低功耗复位   |

## GPIO口

### 读取指定端口管脚的输入

函数原型为：

u8 GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, u16 GPIO_Pin)

参数为GPIOx和GPIO_Pin，读取结果为高电平1，低电平0，返回这个两个数字。

### 读取指定端口的输入

函数原型为：

u16 GPIO_ReadInputData(GPIO_TypeDef* GPIOx) ;

参数为GPIOx，读取结果为一个十六位的二进制数，返回这个数字。

### 读取指定端口管脚的输出

函数原型为：

u8 GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, u16 GPIO_Pin);

参数为GPIOx和GPIO_Pin，读取结果为高电平1，低电平0，返回这个两个数字。

### 读取指定端口的输出

函数原型为：

u16 GPIO_ReadOutputData(GPIO_TypeDef* GPIOx);

参数为GPIOx，读取结果为一个十六位的二进制数，返回这个数字。

### 设置端口管脚为高电平

函数原型为：

void GPIO_SetBits(GPIO_TypeDef* GPIOx, u16 GPIO_Pin) ;

参数为GPIOx和GPIO_Pin，即将指定位设置为高电平。

### 设置端口管脚为低电平

函数原型为：

void GPIO_ResetBits(GPIO_TypeDef* GPIOx, u16 GPIO_Pin) ;

参数为GPIOx和GPIO_Pin，即将指定位设置为低电平。

### 设置端口管脚电平

函数原型为：

void GPIO_WriteBit(GPIO_TypeDef* GPIOx, u16 GPIO_Pin, BitAction BitVal);

参数为GPIOx和GPIO_Pin以及BitVal，决定指定端口管脚的数据位。其中BitVal为枚举值BitAction其中一个值，若直接用数字使用时最好进行数据类型强制转换，即(BitAction)0或(BitAction)1。

Bit_RESET：清除数据端口位；Bit_SET ：设置数据端口位。 

### 翻转指定的数据端口位

函数原型为：

void HAL_GPIO_TogglePin(GPIO_TypeDef *GPIOx,uint16_tGPIO_Pin);

参数为GPIOx和GPIO_Pin，即将指定位翻转。

用于设置引脚的电平翻转，也是通过BSRR寄存器复位或者置位操作。

### 选择GPIO管脚用作外部中断线路

函数原型为：

void GPIO_EXTILineConfig(u8 GPIO_PortSource, u8 GPIO_PinSource);

## NVIC中断优先级

### 设置中断优先级分组

函数原形 void NVIC_PriorityGroupConfig(u32 NVIC_PriorityGroup) 

功能描述：设置优先级分组，先占优先级和从优先级

- 输入参数 NVIC_PriorityGroup：优先级分组位长度
  - NVIC_PriorityGroup_0 先占优先级 0 位 从优先级 4 位 
  - NVIC_PriorityGroup_1 先占优先级 1 位 从优先级 3 位 
  - NVIC_PriorityGroup_2 先占优先级 2 位 从优先级 2 位 
  - NVIC_PriorityGroup_3 先占优先级 3 位 从优先级 1 位 
  - NVIC_PriorityGroup_4 先占优先级 4 位 从优先级 0 位

**优先级分组只能也只需要设置一次**

### 中断优先级初始化

函数原形 void NVIC_Init(NVIC_InitTypeDef* NVIC_InitStruct) 

功能描述 根据 NVIC_InitStruct 中指定的参数初始化外设 NVIC 寄存器

- 输入参数 NVIC_InitStruct：指向结构 NVIC_InitTypeDef 的指针，包含了外设 GPIO 的配置信息

```c
typedef struct
{
    u8 NVIC_IRQChannel;
    u8 NVIC_IRQChannelPreemptionPriority;
    u8 NVIC_IRQChannelSubPriority;
    FunctionalState NVIC_IRQChannelCmd;
} NVIC_InitTypeDef; 
```

- NVIC_IRQChannel ：该参数用以使能或者失能指定的 IRQ 通道。
  - EXTI(X)_IRQChannel 外部中断线(X)中断，取值0~4
  - EXTI9_5_IRQChannel 外部中断线 9-5 中断
  - EXTI15_10_IRQChannel 外部中断线 15-10 中断
  - DMAChannel(X)_IRQChannel DMA 通道(X)中断，取值0~7
  - 
  - WWDG_IRQChannel 窗口看门狗中断 
  - PVD_IRQChannel PVD 通过 EXTI 探测中断 
  - TAMPER_IRQChannel 篡改中断 
  - RTC_IRQChannel RTC 全局中断 
  - FlashItf_IRQChannel FLASH 全局中断 
  - RCC_IRQChannel RCC 全局中断 
  - 
  - ADC_IRQChannel ADC 全局中断 
  - USB_HP_CANTX_IRQChannel USB 高优先级或者 
  - CAN 发送中断 USB_LP_CAN_RX0_IRQChannel 
  - USB 低优先级或者 CAN 接收 0 中断 
  - CAN_RX1_IRQChannel CAN 接收 1 中断 
  - CAN_SCE_IRQChannel CAN SCE 中断
  - 
  - TIM1_BRK_IRQChannel TIM1 暂停中断 
  - TIM1_UP_IRQChannel TIM1 刷新中断 
  - TIM1_TRG_COM_IRQChannel TIM1 触发和通讯中断 
  - TIM1_CC_IRQChannel TIM1 捕获比较中断 
  - TIM2_IRQChannel TIM2 全局中断 
  - TIM3_IRQChannel TIM3 全局中断 
  - TIM4_IRQChannel TIM4 全局中断 
  - I2C1_EV_IRQChannel I2C1 事件中断 
  - I2C1_ER_IRQChannel I2C1 错误中断 
  - I2C2_EV_IRQChannel I2C2 事件中断 
  - I2C2_ER_IRQChannel I2C2 错误中断
  - SPI1_IRQChannel SPI1 全局中断 
  - SPI2_IRQChannel SPI2 全局中断 
  - USART1_IRQChannel USART1 全局中断 
  - USART2_IRQChannel USART2 全局中断 
  - USART3_IRQChannel USART3 全局中断 
  - RTCAlarm_IRQChannel RTC 闹钟通过 EXTI 线中断 
  - USBWakeUp_IRQChannel USB 通过 EXTI 线从悬挂唤醒中断

- NVIC_IRQChannelPreemptionPriority  该参数设置了成员 NVIC_IRQChannel 中的先占优先级。 
- NVIC_IRQChannelSubPriority  该参数设置了成员 NVIC_IRQChannel 中的从优先级。

## DMA直接存储器读取

### DMA初始化

函数原型为：

void DMA_Init(DMA_Channel_TypeDef* DMA_Channelx, DMA_InitTypeDef*  DMA_InitStruct);

DMA_Channelx：x 可以是 1，2…，或者 7 来选择 DMA 通道 x 。根据设备决定DMA通道的数量。

DMA_InitStruct参数：

- DMA_PeripheralBaseAddr：外设基地址，是一个指针。
- DMA_MemoryBaseAddr：存储器基地址，是一个指针。
- DMA_DIR：数据传输方向，有两个取值：
  - DMA_DIR_PeripheralDST 外设作为数据传输的目的地。
  - DMA_DIR_PeripheralSRC 外设作为数据传输的来源。
- DMA_BufferSize：转运的数据数量，是一个数字。
- DMA_PeripheralInc：外设地址变化方式，有两个取值：
  - DMA_PeripheralInc_Enable 外设地址寄存器递增。
  - DMA_PeripheralInc_Disable 外设地址寄存器不变。
- DMA_MemoryInc：存储器地址变化方式，有两个取值：
  - DMA_MemoryInc_Enable 存储器地址寄存器递增。
  - DMA_MemoryInc_Disable 存储器地址寄存器不变。
- DMA_PeripheralDataSize：外设数据宽度，三个取值：
  - DMA_PeripheralDataSize_Byte 数据宽度为 8 位
  - DMA_PeripheralDataSize_HalfWord 数据宽度为 16 位
  - DMA_PeripheralDataSize_Word 数据宽度为 32 位
- DMA_MemoryDataSize：存储器数据宽度，三个取值：
  - DMA_MemoryDataSize_Byte 数据宽度为 8 位
  - DMA_MemoryDataSize_HalfWord 数据宽度为 16 位
  - DMA_MemoryDataSize_Word 数据宽度为 32 位
- DMA_Mode：模式，有两个取值：
  - DMA_Mode_Circular 工作在循环缓存模式
  - DMA_Mode_Normal 工作在正常缓存模式
- DMA_Priority：优先级，四个取值：
  - DMA_Priority_VeryHigh DMA 通道 x 拥有非常高优先级
  - DMA_Priority_High DMA 通道 x 拥有高优先级
  - DMA_Priority_Medium DMA 通道 x 拥有中优先级
  - DMA_Priority_Low DMA 通道 x 拥有低优先级
- DMA_M2M：存储器到存储器，有两个取值：
  - DMA_M2M_Enable DMA 通道 x 设置为内存到内存传输
  - DMA_M2M_Disable DMA 通道 x 没有设置为内存到内存传输

### DMA使能或失能

函数原型为：

void DMA_Cmd(DMA_Channel_TypeDef* DMA_Channelx, FunctionalState NewState);

参数为：

- DMA_Channelx：x 可以是 1，2…，或者 7 来选择 DMA 通道 x 。
- NewState：DMA 通道 x 的新状态 这个参数可以取：ENABLE 或者 DISABLE。

### 设置当前通道中DMA传输的数据单元数量。

函数原型为：

void DMA_SetCurrDataCounter(DMA_Channel_TypeDef* DMAy_Channelx, uint16_t DataNumber);

参数为当前DMA通道DMAy_Channelx和传输数据单元数量DataNumber。

### 检查当前通道DMA是否完成

函数原型为：

FlagStatus DMA_GetFlagStatus(u32 DMA_FLAG);

参数DMA_FLAG有下表取值：

| DMA_FLAG          | 描述                  |
| ----------------- | --------------------- |
| DMA_FLAG_GL1      | 通道 1 全局标志位     |
| DMA_FLAG_TC1      | 通道 1 传输完成标志位 |
| DMA_FLAG_HT1      | 通道 1 传输过半标志位 |
| DMA_FLAG_TE1      | 通道 1 传输错误标志位 |
| DMA_FLAG_GL2      | 通道 2 全局标志位     |
| DMA_FLAG_TC2      | 通道 2 传输完成标志位 |
| DMA_FLAG_HT2      | 通道 2 传输过半标志位 |
| DMA_FLAG_TE2      | 通道 2 传输错误标志位 |
| STM32F10系列直到7 | 更改数字即可          |

返回值为DMA_FLAG 的新状态（SET 或者 RESET）。

### DMA清除标志位

函数原形	void DMA_ClearFlag(u32 DMA_FLAG);

功能描述	清除 DMA 通道 x 待处理标志位

输入参数	DMA_FLAG：待清除的 DMA 标志位，使用操作符 | 。参考“检查当前通道DMA是否完成”。

## ADC/

### 设置ADC时钟（ADCCLK）

函数原型为：void ADC_ADCCLKConfig(u32 RCC_ADCCLKSource) :

参数RCC_ADCCLKSource为分频数，可以选择2，4，6，8分频，用RCC_PCLK2_Div(x)表示。PCLK2意为该时钟源自APB2时钟。

设置指定ADC的规则组通道，设置它们的转化顺序和采样时间。



ADC结构体

ADC_InitTypeDef

ADC_RegularChannelConfig

## DAC

## TIM定时器/

### 设置TIMx内部时钟

设置TIMx内部时钟，函数原型为：

void TIM_DMACmd(TIM_TypeDef* TIMx, u16 TIM_DMASource, FunctionalState Newstate);

参数填写一个TIMx就行了，不填也默认是内部时钟。

### 清除TIMx的待处理标志位

清除TIMx的待处理标志位，函数原型为：

void TIM_ClearFlag(TIM_TypeDef* TIMx, u32 TIM_FLAG) ;

清除定时器更新标志位，若不清除此标志位，则开启中断后，会立刻进入一次中断。

### TIM_FLAG：待清除的TIM标志位

参数为TIMx和TIM_FLAG，TIM_FLAG为待清除的TIM标志位，取值有TIM_FLAG_Update。

使能或者失能指定的TIM中断

### 使能或者失能指定的TIM中断

清除定时器更新标志位，函数原型为：

void TIM_ITConfig(TIM_TypeDef* TIMx, u16 TIM_IT, FunctionalState  NewState) ;

NewState：TIMx 中断的新状态  这个参数可以取：ENABLE或者DISABLE。

### 使能或者失能TIMx外设

使能或者失能TIMx外设，定时器开始运行，函数原型为：

void TIM_Cmd(TIM_TypeDef* TIMx, FunctionalState NewState);

NewState：TIMx 中断的新状态  这个参数可以取：ENABLE或者DISABLE。

## RCC系统时钟

### 设置外部低速晶振

函数原形 void RCC_LSEConfig(u32 RCC_LSE) 

功能描述 设置外部低速晶振（LSE）

- 参数RCC_LSE：
  - RCC_LSE_OFF LSE 晶振 OFF  
  - RCC_LSE_ON LSE 晶振 ON  
  - RCC_LSE_Bypass LSE 晶振被外部时钟旁路

### 检查指定的 RCC 标志位设置与否

函数名 RCC_ GetFlagStatus  

函数原形 FlagStatus RCC_GetFlagStatus(u8 RCC_FLAG) 

功能描述 检查指定的 RCC 标志位设置与否 

- 输入参数 RCC_FLAG：待检查的 RCC 标志位
  - RCC_FLAG_HSIRDY HSI 晶振就绪 
  - RCC_FLAG_HSERDY HSE 晶振就绪 
  - RCC_FLAG_PLLRDY PLL 就绪 
  - RCC_FLAG_LSERDY LSI 晶振就绪 
  - RCC_FLAG_LSIRDY LSE 晶振就绪 
  - RCC_FLAG_PINRST 管脚复位 
  - RCC_FLAG_PORRST POR/PDR 复位 
  - RCC_FLAG_SFTRST 软件复位 
  - RCC_FLAG_IWDGRST IWDG 复位 
  - RCC_FLAG_WWDGRST WWDG 复位 
  - RCC_FLAG_LPWRRST 低功耗复位
- 返回值 RCC_FLAG 的新状态（SET 或者 RESET）

###  设置 RTC 时钟

函数原形 void RCC_RTCCLKConfig(u32 RCC_RTCCLKSource)  

功能描述 设置 RTC 时钟（RTCCLK） 

- 输入参数 RCC_RTCCLKSource: 
  - RCC_RTCCLKSource_LSE 选择 LSE 作为 RTC 时钟 
  - RCC_RTCCLKSource_LSI 选择 LSI 作为 RTC 时钟 
  - RCC_RTCCLKSource_HSE_Div128 选择 HSE 时钟频率除以 128 作为 RTC 时钟

先决条件 RTC 时钟一经选定即不能更改，除非复位后备域

### 使能或者失能 RTC 时钟

函数名 RCC_RTCCLKCmd  

函数原形 void RCC_RTCCLKCmd(FunctionalState NewState)  

输入参数 NewState：RTC 时钟的新状态 这个参数可以取：ENABLE 或者 DISABLE 

先决条件 该函数只有在通过函数 RCC_RTCCLKConfig 选择 RTC 时钟后，才能调用

## RTC实时时钟

### 等待最近一次对 RTC 寄存器的写操作完成

函数原形 void RTC_WaitForSynchro(void)  

功能描述 等待最近一次对 RTC 寄存器的写操作完成

### 等待最近一次对 RTC 寄存器的写操作完成？等待上一次操作完成

函数原形 void RTC_WaitForLastTask(void)  

功能描述 等待最近一次对 RTC 寄存器的写操作完成

（两个函数功能一致？）

### 设置RTC预分频器

函数名 RTC_SetPrescaler  

函数原形 void RTC_SetPrescaler(u32 PrescalerValue)  

功能描述 设置 RTC 预分频的值 

输入参数 PrescalerValue：新的 RTC 预分频值。

先决条件 在使用本函数前必须先调用函数 RTC_WaitForLastTask()，等待标志位 RTOFF 被设置

函数原形 void RTC_SetCounter(u32 CounterValue)  

功能描述 设置 RTC 计数器的值 // 将秒计数器写入到RTC的CNT中

输入参数 CounterValue：新的 RTC 计数器值



进入RTC配置模式

进入RTC配置模式，

## 独立看门狗/

### 开启看门狗

使能或者失能对寄存器IWDG_PR和IWDG_RLR的写操作，函数原型为：

void IWDG_WriteAccessCmd(u16 IWDG_WriteAccess) ;

参数为IWDG_WriteAccess。IWDG_WriteAccess可以是IWDG_WriteAccess_Enable（使能对寄存器IWDG_PR 和 IWDG_RLR 的写操作）或IWDG_WriteAccess_Disable（失能对寄存器IWDG_PR 和 IWDG_RLR 的写操作）。

### 设置IWDG预分频值

设置IWDG预分频值，函数原型为：

void IWDG_SetPrescaler(u8 IWDG_Prescaler);

参数为IWDG_Prescaler，可以取IWDG_Prescaler_4 ~ IWDG_Prescaler_256（均为2的次方）。

### 设置IWDG重装载值

设置IWDG重装载值，函数原型为：

void IWDG_SetReload(u16 Reload) 

参数为Reload，为IWDG的重装载值，该参数允许取值范围为0 – 0x0FFF(4095)。

### 重装计数器

重装计数器，函数原型为：

void IWDG_ReloadCounter(void) ;

将重装载值计数器清零，也就是喂狗。

## 窗口看门狗/

### 将外设WWDG寄存器重设为缺省值，函数原型为：

WWDG_DeInit

void WWDG_DeInit(WWDG_TypeDef* WWDGx) 

### 设置WWDG预分频值 

设置WWDG预分频值，函数原型为：

void WWDG_SetPrescaler(u32 WWDG_Prescaler) ;

参数为WWDG_Prescaler，可以取WWDG_Prescaler_1 ~ WWDG_Prescaler_8（均为2的次方或1）。

### 设置WWDG窗口值

### 设置WWDG窗口值，函数原型为：

void WWDG_SetWindowValue(u8 WindowValue);

参数为WindowValue，可以取在0x40与0x7F之间。

### 使能WWDG早期唤醒中断（EWI）

void WWDG_EnableIT(void) 

### 设置WWDG计数器值 

WWDG_SetCounter

### 使能WWDG并装入计数器值 

WWDG_Enable

### 检查WWDG早期唤醒中断标志位被设置与否 

WWDG_GetFlagStatus

### 清除早期唤醒中断标志位 

WWDG_ClearFlag

## SPI串行外设接口

### 将外设 SPIx 寄存器重设为缺省值

函数原形 void SPI_DeInit(SPI_TypeDef* SPIx) ;

输入参数 SPIx：x 可以是 1 或者 2，来选择 SPI 外设;

### 初始化外设 SPIx 寄存器

函数原形 void SPI_Init(SPI_TypeDef* SPIx, SPI_InitTypeDef* SPI_InitStruct) ;

- 输入参数 1 SPIx：x 可以是 1 或者 2，来选择 SPI 外设。

- 输入参数 2 SPI_InitStruct：指向结构 SPI_InitTypeDef 的指针，包含了外设 SPI 的配置信息 参阅 Section：SPI_InitTypeDef 查阅更多该参数允许取值范围

SPI_InitTypeDef包含参数：

```
typedef struct
{
u16 SPI_Direction;
u16 SPI_Mode;
u16 SPI_DataSize;
u16 SPI_CPOL;
u16 SPI_CPHA;
u16 SPI_NSS;
u16 SPI_BaudRatePrescaler;
u16 SPI_FirstBit;
u16 SPI_CRCPolynomial;
} SPI_InitTypeDef; 
```

- SPI_Direction：设置了 SPI 单向或者双向的数据模式
  - SPI_Direction_2Lines_FullDuplex SPI 设置为双线双向全双工 
  - SPI_Direction_2Lines_RxOnly SPI 设置为双线单向接收 
  - SPI_Direction_1Line_Rx SPI 设置为单线双向接收 
  - SPI_Direction_1Line_Tx SPI 设置为单线双向发送
- SPI_Mode：SPI 工作模式
  - SPI_Mode_Master 设置为主 SPI  
  - SPI_Mode_Slave 设置为从 SPI
- SPI_DataSize：SPI 的数据大小
  - SPI_DataSize_16b SPI 发送接收 16 位帧结构 
  - SPI_DataSize_8b SPI 发送接收 8 位帧结构

- SPI_CPOL：选择了串行时钟的稳态，即空闲时的SPI电位。
  - SPI_CPOL_High 时钟悬空高 
  - SPI_CPOL_Low 时钟悬空低
- SPI_CPHA：位捕获的时钟活动沿，和SPI_CPOL共同决定了SPI模式。
  - SPI_CPHA_2Edge 数据捕获于第二个时钟沿 
  - SPI_CPHA_1Edge 数据捕获于第一个时钟沿
- SPI_NSS：指定了 NSS 信号由硬件（NSS 管脚）还是软件（使用 SSI 位）管理。
  - SPI_NSS_Hard NSS 由外部管脚管理 
  - SPI_NSS_Soft 内部 NSS 信号有 SSI 位控制
- SPI_BaudRatePrescaler：定义波特率预分频的值，可以取SPI_BaudRatePrescaler(X取2~256)

- SPI_FirstBit：指定数据传输从 MSB 高位还是 LSB 低位开始
  - SPI_FisrtBit_MSB 数据传输从 MSB 高位开始 
  - SPI_FisrtBit_LSB 数据传输从 LSB 低位开始
- SPI_CRCPolynomial：定义了用于 CRC 值计算的多项式，无需该功能则为7。

### 使能或者失能 SPI 外设

函数原形 void SPI_Cmd(SPI_TypeDef* SPIx, FunctionalState NewState)

- 输入参数 1 SPIx：x 可以是 1 或者 2，来选择 SPI 外设 

- 输入参数 2 NewState: 外设 SPIx 的新状态 这个参数可以取：ENABLE 或者 DISABLE

### 等待发送数据寄存器空

读取数据寄存器空

FlagStatus SPI_I2S_GetFlagStatus(SPI_TypeDef* SPIx, uint16_t SPI_I2S_FLAG_TXE);

- 参数1 SPIx：x 可以是 1 或者 2，来选择 SPI 外设 
- 参数2 SPI_I2S_FLAG_TXE：寄存器状态 ？

- 返回值：FlagStatus
  - SET代表高电平，为真。
  - RESET代表低电平，为假。

while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_TXE) != SET);

### 写入数据到发送数据寄存器

写入数据到发送数据寄存器，开始产生时序

void SPI_I2S_SendData(SPI_TypeDef* SPIx, uint16_t ByteSend);

- 参数1 ByteSend：发送值。

### 等待接收数据寄存器非空

函数原型：

FlagStatus SPI_I2S_GetFlagStatus(SPI_TypeDef* SPIx, uint16_t SPI_I2S_FLAG);

- 参数1 SPIx：x 可以是 1 或者 2，来选择 SPI 外设 
- 参数2 SPI_I2S_FLAG：标志位
- 返回值：FlagStatus
  - SET代表高电平，为真。
  - RESET代表低电平，为假。

while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_RXNE) != SET);

### 返回SPIx数据寄存器的值

函数原型为 uint16_t SPI_I2S_ReceiveData(SPI_TypeDef* SPIx);

- 参数1 SPIx：x 可以是 1 或者 2，来选择 SPI 外设 
- 返回值：返回该SPI外设收到的值。

### 按默认值初始化SPI

把 SPI_InitStruct 中的每一个参数按缺省值填入。

函数原形 void SPI_StructInit(SPI_InitTypeDef* SPI_InitStruct);

## I2C/

### 使能I2C

使能I2C，开始运行，函数原型为：

void I2C_Cmd(I2C_TypeDef* I2Cx, FunctionalState NewState) ;

参数为I2Cx和NewState。I2Cx可以是I2C1或I2C2。NewState是状态，有ENABLE（开始）或者DISABLE（结束）两个选项。

### I2C开始

开始硬件I2C，函数原型为：

void I2C_GenerateSTART(I2C_TypeDef* I2Cx, FunctionalState NewState) ;

参数为I2Cx和NewState。I2Cx可以是I2C1或I2C2。NewState是状态，有ENABLE（开始）或者DISABLE（结束）两个选项。

### 发送七位地址

发送I2C七位地址，函数原型为：

void I2C_Send7bitAddress(I2C_TypeDef* I2Cx, u8 Address, u8  I2C_Direction) ;

参数为I2Cx和Address和I2C_Direction。I2Cx可以是I2C1或I2C2。Address为地址。I2C_Direction可以是I2C_Direction_Transmitter（发送）或I2C_Direction_Receiver（接受）。

### I2C发送一个字节的数据

I2C发送一个字节的数据，函数原型为：

void I2C_SendData(I2C_TypeDef* I2Cx, u8 Data) ;

参数为I2Cx和Data。I2Cx可以是I2C1或I2C2。Data为要发送的数据。

### I2C应答位

2C应答位，函数原型为：

void I2C_AcknowledgeConfig(I2C_TypeDef* I2Cx, FunctionalState NewState) ;

参数为I2Cx和NewState。I2Cx可以是I2C1或I2C2。NewState是状态，有ENABLE（开始）或者DISABLE（结束）两个选项。

### I2C停止

硬件I2C停止，函数原型为：

void I2C_GenerateSTOP(I2C_TypeDef* I2Cx, FunctionalState NewState) ;

参数为I2Cx和NewState。I2Cx可以是I2C1或I2C2。NewState是状态，有ENABLE（开始）或者DISABLE（结束）两个选项。

## CAN
