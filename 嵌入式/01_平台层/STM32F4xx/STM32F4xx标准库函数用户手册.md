# STM32F4xx标准库函数用户手册

## 格式

| 函数名      | 外设函数的名称           |
| ----------- | ------------------------ |
| 函数原形    | 原形声明                 |
| 功能描述    | 简要解释函数是如何执行的 |
| 输入参数{x} | 输入参数描述             |
| 输出参数{x} | 输出参数描述             |
| 返回值      | 函数的返回值             |
| 先决条件    | 调用函数前应满足的要求   |
| 被调用函数  | 其他被该函数调用的库函数 |

## 常见函数

| 函数名                | 作用                                                         |      |
| --------------------- | ------------------------------------------------------------ | ---- |
| PPP_DeInit            | 复位外设 PPP 的所有寄存器至缺省值                            |      |
| PPP_Init              | PPP初始化，根据 PPP_InitTypeDef 中指定的参数，初始化外设 PPP |      |
| PPP_StructInit        | PPP按默认值的 PPP_InitTypeDef 结构中的各种参数来定义外设的功能 |      |
| PPP_Cmd               | 使能或者失能来自外设 PPP                                     |      |
| PPP_ITConfig          | 使能或者失能来自外设 PPP 某中断源                            |      |
| PPP_DMAConfig         | 使能或者失能外设 PPP 的 DMA 接口                             |      |
| PPP_GetFlagStatus     | 检查外设 PPP 某标志位被设置与否                              |      |
| PPP_ClearFlag         | 清除外设 PPP 标志位                                          |      |
| PPP_GetITStatus       | 判断来自外设 PPP 的中断发生与否                              |      |
| PPP_ClearITPendingBit | 清除外设 PPP 中断待处理标志位                                |      |
|                       |                                                              |      |
|                       |                                                              |      |
|                       |                                                              |      |
|                       |                                                              |      |

## 常见数据类型或标志位

固态函数库定义了 24 个变量类型，他们的类型和大小是固定的。在文件 stm32f10x_type.h 中定义了这些变量。

**bool型**

```c
typedef enum 
{ 
	FALSE = 0,		//布尔假
	TRUE = !FALSE	//布尔真
} bool; 
```

**标志位状态类型**

标志位类型（FlagStatus type）的 2 个可能值为“设置”与“重置”（SET  or RESET）

```c
typedef enum
{
	RESET = 0,		//重置
	SET = !RESET	//设置
} FlagStatus;
```

**功能状态类型**

定义功能状态类型（FunctionalState type）的 2 个可能值为“使能”与“失能”（ENABLE or DISABLE）。

```c
typedef enum
{
    DISABLE = 0,		//失能
    ENABLE = !DISABLE	//使能
} FunctionalState;
```

**错误状态类型**

错误状态类型类型（ErrorStatus type）的 2 个可能值为“成功”与“出错” （SUCCESS or ERROR）

```c
typedef enum
{
    ERROR = 0,			//出错       
    SUCCESS = !ERROR	//成功
} ErrorStatus;
```

## 储存器架构 ## 

## 嵌入式Flash接口

## CRC计算单元

## PWR电源控制

## RCC时钟

## GPIO和AFIO



## SYSCFG系统配置控制器



## DMA控制器

```C
/*初始化为默认值*/ 
void DMA_DeInit(DMA_Stream_TypeDef* DMAy_Streamx);
/*初始化与配置*/ 
void DMA_Init(DMA_Stream_TypeDef* DMAy_Streamx, DMA_InitTypeDef* DMA_InitStruct);
void DMA_StructInit(DMA_InitTypeDef* DMA_InitStruct);
void DMA_Cmd(DMA_Stream_TypeDef* DMAy_Streamx, FunctionalState NewState);
/*可选的配置函数*/ 
void DMA_PeriphIncOffsetSizeConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_Pincos);
void DMA_FlowControllerConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_FlowCtrl);
/*数据计算器功能*/ 
void DMA_SetCurrDataCounter(DMA_Stream_TypeDef* DMAy_Streamx, uint16_t Counter);
uint16_t DMA_GetCurrDataCounter(DMA_Stream_TypeDef* DMAy_Streamx);
/*双缓冲模式函数*/ 
void DMA_DoubleBufferModeConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t Memory1BaseAddr,
                                uint32_t DMA_CurrentMemory);
void DMA_DoubleBufferModeCmd(DMA_Stream_TypeDef* DMAy_Streamx, FunctionalState NewState);
void DMA_MemoryTargetConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t MemoryBaseAddr,
                            uint32_t DMA_MemoryTarget);
uint32_t DMA_GetCurrentMemoryTarget(DMA_Stream_TypeDef* DMAy_Streamx);
/*中断与标记管理功能*/ 
FunctionalState DMA_GetCmdStatus(DMA_Stream_TypeDef* DMAy_Streamx);
uint32_t DMA_GetFIFOStatus(DMA_Stream_TypeDef* DMAy_Streamx);
FlagStatus DMA_GetFlagStatus(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_FLAG);
void DMA_ClearFlag(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_FLAG);
void DMA_ITConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_IT, FunctionalState NewState);
ITStatus DMA_GetITStatus(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_IT);
void DMA_ClearITPendingBit(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_IT);
```

**初始化为默认值**

```C
void DMA_DeInit(DMA_Stream_TypeDef* DMAy_Streamx);
```

**初始化与配置**

按照DMA_InitStruct参数初始化指定DMA的DMAy_Streamx数据流

```C
void DMA_Init(DMA_Stream_TypeDef* DMAy_Streamx, DMA_InitTypeDef* DMA_InitStruct);
```

DMA_Streamx数据流包含DMA(X)_Stream(Y)（X为1~2，Y为0~7）共16个。

DMA_InitStruct包含：

- DMA_Channel：通道选择

  - 包含DMA_Channel_(X)（X取值0~7）

- DMA_PeripheralBaseAddr：外设地址。

- DMA_Memory0BaseAddr：内存地址。

- DMA_DIR：DMA传输方向

  - DMA_DIR_PeripheralToMemory：外设到存储器。DMA为外设，目的地一般是内部储存器。
  - DMA_DIR_MemoryToPeripheral：存储器到外设。
  - DMA_DIR_MemoryToMemory：存储器到存储器。

- DMA_BufferSize：设置DMA在传输时缓冲区的长度

- DMA_PeripheralInc：设置DMA的外设地址递增模式

  - DMA_PeripheralInc_Enable：外设地址递增。
  - DMA_PeripheralInc_Disable：外设地址不变。

- DMA_MemoryInc：设置DMA的内存递增模式

  - DMA_MemoryInc_Enable：内存地址递增。
    
  - DMA_MemoryInc_Disable：内存地址不变。
    

- DMA_PeripheralDataSize;//外设数据字长

  - DMA_PeripheralDataSize_Byte：字节 8 位
  - DMA_PeripheralDataSize_HalfWord：半字 16 位
  - DMA_PeripheralDataSize_Word：字 32 位

- DMA_MemoryDataSize;//内存数据字长

  - DMA_MemoryDataSize_Byte：字节 8 位

  - DMA_MemoryDataSize_HalfWord：半字 16 位
  - DMA_MemoryDataSize_Word：字 32 位

- DMA_Mode：设置DMA的传输模式

  - DMA_Mode_Normal：禁止循环模式。或是说  正常模式。
  - DMA_Mode_Circular：使能循环模式。

- DMA_Priority：设置DMA的优先级别

  - DMA_Priority_Low：低。
  - DMA_Priority_Medium：中。
  - DMA_Priority_High：高。
  - DMA_Priority_VeryHigh：非常高。

- DMA_FIFOMode：直接模式禁止

  - DMA_FIFOMode_Disable：使能直接模式。
  - DMA_FIFOMode_Enable：禁止直接模式。

- DMA_FIFOThreshold：FIFO 阈值选择

  - DMA_FIFOThreshold_1QuarterFull：FIFO 容量的 1/4。
  - DMA_FIFOThreshold_HalfFull：FIFO 容量的 1/2。
  - DMA_FIFOThreshold_3QuartersFull：FIFO 容量的 3/4。
  - DMA_FIFOThreshold_Full：FIFO 完整容量。

- DMA_MemoryBurst：存储器突发传输配置。

  - DMA_MemoryBurst_Single：单次传输。
  - DMA_MemoryBurst_INC4：4 个节拍的增量突发传输。
  - DMA_MemoryBurst_INC8：8 个节拍的增量突发传输。
  - DMA_MemoryBurst_INC16：16 个节拍的增量突发传输。

- DMA_PeripheralBurst：外设突发传输配置。

  - DMA_PeripheralBurst_Single：单次传输。
  - DMA_PeripheralBurst_INC4：4 个节拍的增量突发传输。
  - DMA_PeripheralBurst_INC8：8 个节拍的增量突发传输。
  - DMA_PeripheralBurst_INC16：16 个节拍的增量突发传输。



```C
void DMA_StructInit(DMA_InitTypeDef* DMA_InitStruct);
```



```C
void DMA_Cmd(DMA_Stream_TypeDef* DMAy_Streamx, FunctionalState NewState);

```



**可选的配置函数**







```C

void DMA_PeriphIncOffsetSizeConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_Pincos);

```



```C
void DMA_FlowControllerConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_FlowCtrl);


```



**数据计算器功能**





```C
void DMA_SetCurrDataCounter(DMA_Stream_TypeDef* DMAy_Streamx, uint16_t Counter);

```





```C
uint16_t DMA_GetCurrDataCounter(DMA_Stream_TypeDef* DMAy_Streamx);


```



**双缓冲模式函数**





```C
void DMA_DoubleBufferModeConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t Memory1BaseAddr,
                                uint32_t DMA_CurrentMemory);

```





```C
void DMA_DoubleBufferModeCmd(DMA_Stream_TypeDef* DMAy_Streamx, FunctionalState NewState);

```





```C
void DMA_MemoryTargetConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t MemoryBaseAddr,
                            uint32_t DMA_MemoryTarget);

```





```C
uint32_t DMA_GetCurrentMemoryTarget(DMA_Stream_TypeDef* DMAy_Streamx);

```



**中断与标记管理功能**

```C
FunctionalState DMA_GetCmdStatus(DMA_Stream_TypeDef* DMAy_Streamx);

```





```C
uint32_t DMA_GetFIFOStatus(DMA_Stream_TypeDef* DMAy_Streamx);

```





```C
FlagStatus DMA_GetFlagStatus(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_FLAG);

```





```C
void DMA_ClearFlag(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_FLAG);

```





```C
void DMA_ITConfig(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_IT, FunctionalState NewState);

```



```c
ITStatus DMA_GetITStatus(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_IT);

```



```c
void DMA_ClearITPendingBit(DMA_Stream_TypeDef* DMAy_Streamx, uint32_t DMA_IT);
```









## 中断



## ADC模拟数字转化

**ADC公共配置：**

对应结构体成员：

```c
typedef struct //ADC公共配置
{
  uint32_t ADC_Mode;//设置ADC模式
  uint32_t ADC_Prescaler;//设置ADC的预分频器
  uint32_t ADC_DMAAccessMode;//DMA访问模式
  uint32_t ADC_TwoSamplingDelay;//设置两次采样之间的延迟ADC时钟周期
}ADC_CommonInitTypeDef;
```

- ADC_Mode取值范围
  - ADC_Mode_Independent：设置ADC为独立模式
  - ADC_DualMode_RegSimult_InjecSimult
  - ADC_DualMode_RegSimult_AlterTrig
  - ADC_DualMode_InjecSimult
  - ADC_DualMode_RegSimult
  - ADC_DualMode_Interl
  - ADC_DualMode_AlterTrig
  - ADC_TripleMode_RegSimult_InjecSimult
  - ADC_TripleMode_RegSimult_AlterTrig
  - ADC_TripleMode_InjecSimult
  - ADC_TripleMode_RegSimult
  - ADC_TripleMode_Interl
  - ADC_TripleMode_AlterTrig
- ADC_Prescaler：设置ADC的预分频器
  - ADC_Prescaler_Div2：二分频。
  - ADC_Prescaler_Div4：四分频。
  - ADC_Prescaler_Div6：六分频。
  - ADC_Prescaler_Div8：八分频。
  - IS_ADC_PRESCALER(PRESCALER)
- ADC_DMAAccessMode：DMA访问模式
  - ADC_DMAAccessMode_Disabled：禁用DMA访问模式。
  - ADC_DMAAccessMode_（1~3）
  - IS_ADC_DMA_ACCESS_MODE(MODE)
- ADC_TwoSamplingDelay：设置两次采样之间的延迟为（X）个ADC时钟周期
  - ADC_TwoSamplingDelay_（5~20）Cycles
  - IS_ADC_SAMPLING_DELAY(DELAY)

案例：

```c
ADC_DeInit();//ADC复位
ADC_CommonInitTypeDef ADC_CommonInitStruct;//申请结构体
ADC_CommonInitStruct.ADC_Mode=ADC_Mode_Independent;//设置ADC为独立模式
ADC_CommonInitStruct.ADC_Prescaler=ADC_Prescaler_Div4;//四分频。
ADC_CommonInitStruct.ADC_DMAAccessMode=ADC_DMAAccessMode_Disabled;//禁用DMA访问模式。
ADC_CommonInitStruct.ADC_TwoSamplingDelay=ADC_TwoSamplingDelay_5Cycles;//设置两次采样之间的延迟为5个ADC时钟周期
ADC_CommonInit(&ADC_CommonInitStruct);//
```

**ADC特定配置：**

```c
typedef struct //ADC特定配置
{
  uint32_t ADC_Resolution;//设置ADC分辨率
  FunctionalState ADC_ScanConvMode;//扫描模式
  FunctionalState ADC_ContinuousConvMode;//连续转换模式
  uint32_t ADC_ExternalTrigConvEdge;//禁用外部触发转换
  uint32_t ADC_ExternalTrigConv;//
  uint32_t ADC_DataAlign;//设置数据对齐方式
  uint8_t  ADC_NbrOfConversion;//设置转换数量为1
}ADC_InitTypeDef;
```

- ADC_Resolution：设置ADC分辨率
  - ADC_Resolution_12b：12位。
  - ADC_Resolution_10b：10位。
  - ADC_Resolution_8b：8位。
  - ADC_Resolution_6b：6位。
  - IS_ADC_RESOLUTION(RESOLUTION)
- ADC_ScanConvMode：扫描模式的选择
  - ENABLE：使能。
  - DISABLE：失能。
- ADC_ContinuousConvMode：连续转换模式的选择
  - ENABLE：使能。
  - DISABLE：失能。
- ADC_ExternalTrigConvEdge：外部触发转换
  - ADC_ExternalTrigConvEdge_None：禁止外部触发转换，也就是用软件触发ADC转换
  - ADC_ExternalTrigConvEdge_Rising
  - ADC_ExternalTrigConvEdge_Falling
  - ADC_ExternalTrigConvEdge_RisingFalling
  - IS_ADC_EXT_TRIG_EDGE(EDGE)
- ADC_ExternalTrigConv：
  - ADC_ExternalTrigConv_T1_CC1
  - ADC_ExternalTrigConv_T1_CC2
  - ADC_ExternalTrigConv_T1_CC3
  - ADC_ExternalTrigConv_T2_CC2
  - ADC_ExternalTrigConv_T2_CC3
  - ADC_ExternalTrigConv_T2_CC4
  - ADC_ExternalTrigConv_T2_TRGO
  - ADC_ExternalTrigConv_T3_CC1
  - ADC_ExternalTrigConv_T3_TRGO
  - ADC_ExternalTrigConv_T4_CC4
  - ADC_ExternalTrigConv_T5_CC1
  - ADC_ExternalTrigConv_T5_CC2
  - ADC_ExternalTrigConv_T5_CC3
  - ADC_ExternalTrigConv_T8_CC1
  - ADC_ExternalTrigConv_T8_TRGO
  - ADC_ExternalTrigConv_Ext_IT11
  - IS_ADC_EXT_TRIG(REGTRIG)
- ADC_DataAlign：设置数据对齐方式
  - ADC_DataAlign_Right
  - ADC_DataAlign_Left
  - IS_ADC_DATA_ALIGN(ALIGN)
- ADC_NbrOfConversion：设置转换数量
  - ADC_Channel_（0~18）：设置转换数量为0。

**案例：**

```c
ADC_InitTypeDef ADC_InitStruct;
RCC_APB2PeriphClockCmd(RCC_APB2Periph_ADC1, ENABLE);
ADC_InitStruct.ADC_ContinuousConvMode = DISABLE;
ADC_InitStruct.ADC_DataAlign=ADC_DataAlign_Right;
ADC_InitStruct.ADC_ExternalTrigConvEdge=ADC_ExternalTrigConvEdge_None;
ADC_InitStruct.ADC_NbrOfConversion=1;
ADC_InitStruct.ADC_Resolution=ADC_Resolution_12b;
ADC_InitStruct.ADC_ScanConvMode = DISABLE;
ADC_Init(ADC1, &ADC_InitStruct);
```







## DAC数字模拟转换

库函数包括：

```c
/*  Function used to set the DAC configuration to the default reset state *****/  
void DAC_DeInit(void);

/*  DAC channels configuration: trigger, output buffer, data format functions */
void DAC_Init(uint32_t DAC_Channel, DAC_InitTypeDef* DAC_InitStruct);
void DAC_StructInit(DAC_InitTypeDef* DAC_InitStruct);
void DAC_Cmd(uint32_t DAC_Channel, FunctionalState NewState);
void DAC_SoftwareTriggerCmd(uint32_t DAC_Channel, FunctionalState NewState);
void DAC_DualSoftwareTriggerCmd(FunctionalState NewState);
void DAC_WaveGenerationCmd(uint32_t DAC_Channel, uint32_t DAC_Wave, FunctionalState NewState);
void DAC_SetChannel1Data(uint32_t DAC_Align, uint16_t Data);
void DAC_SetChannel2Data(uint32_t DAC_Align, uint16_t Data);
void DAC_SetDualChannelData(uint32_t DAC_Align, uint16_t Data2, uint16_t Data1);
uint16_t DAC_GetDataOutputValue(uint32_t DAC_Channel);

/* DMA management functions ***************************************************/
void DAC_DMACmd(uint32_t DAC_Channel, FunctionalState NewState);

/* Interrupts and flags management functions **********************************/
void DAC_ITConfig(uint32_t DAC_Channel, uint32_t DAC_IT, FunctionalState NewState);
FlagStatus DAC_GetFlagStatus(uint32_t DAC_Channel, uint32_t DAC_FLAG);
void DAC_ClearFlag(uint32_t DAC_Channel, uint32_t DAC_FLAG);
ITStatus DAC_GetITStatus(uint32_t DAC_Channel, uint32_t DAC_IT);
void DAC_ClearITPendingBit(uint32_t DAC_Channel, uint32_t DAC_IT);    
```

**DAC恢复缺省值**

```c
void DAC_DeInit(void);
```

**DAC初始化**

```c
void DAC_Init(uint32_t DAC_Channel, DAC_InitTypeDef* DAC_InitStruct);
```

参数为：DAC_Channel 和 DAC_InitTypeDef 结构体。

```c
typedef struct
{
  uint32_t DAC_Trigger;
  uint32_t DAC_WaveGeneration;
  uint32_t DAC_LFSRUnmask_TriangleAmplitude;
  uint32_t DAC_OutputBuffer;
}DAC_InitTypeDef;
```

- DAC_Trigger：这个字段用于配置DAC的触发源。
  - 例如，DAC的输出可以由一个定时器（TIM）或其他外部事件触发。
    可能的值包括DAC_Trigger_None（无触发）、DAC_Trigger_T2_TRGO（由TIM2的触发输出触发）等。
- DAC_WaveGeneration：
  - 这个字段用于配置DAC是否生成噪声波或三角波。
    	可能的值包括DAC_WaveGeneration_None（不生成波形）、
      	DAC_WaveGeneration_Noise（生成噪声波）和DAC_WaveGeneration_Triangle（生成三角波）。
- DAC_LFSRUnmask_TriangleAmplitude：
  - 当DAC_WaveGeneration设置为DAC_WaveGeneration_Noise时，
    这个字段用于配置线性反馈移位寄存器（LFSR）的未屏蔽位，它决定了噪声波的分辨率。
    当DAC_WaveGeneration设置为DAC_WaveGeneration_Triangle时，这个字段用于配置三角波的幅度。
- DAC_OutputBuffer：
  - 这个字段用于配置DAC的输出缓冲。输出缓冲通常用于减少DAC输出的噪声和失真。可能的值包括DAC_OutputBuffer_Enable（启用输出缓冲）和DAC_OutputBuffer_Disable（禁用输出缓冲）。


DAC初始化为默认值

```c
void DAC_StructInit(DAC_InitTypeDef* DAC_InitStruct);
```

DAC使能或失能

```c
void DAC_Cmd(uint32_t DAC_Channel, FunctionalState NewState);
```

通过软件触发来启动或停止DAC

```c
void DAC_SoftwareTriggerCmd(uint32_t DAC_Channel, FunctionalState NewState);
```

同时启动或停止DAC的两个通道

```c
void DAC_DualSoftwareTriggerCmd(FunctionalState NewState);
```



```c
void DAC_WaveGenerationCmd(uint32_t DAC_Channel, uint32_t DAC_Wave, FunctionalState NewState);
```

该函数用于设置DAC（数字到模拟转换器）通道1的数据

```c
void DAC_SetChannel1Data(uint32_t DAC_Align, uint16_t Data);
```

该函数用于设置DAC（数字到模拟转换器）通道2的数据

```c
void DAC_SetChannel2Data(uint32_t DAC_Align, uint16_t Data);
```

设置DAC的两个通道（通常是DAC通道1和通道2）的数据

```c
void DAC_SetDualChannelData(uint32_t DAC_Align, uint16_t Data2, uint16_t Data1);
```

该函数旨在读取DAC（数字到模拟转换器）特定通道的输出值。然而，需要注意的是，DAC本身并不直接提供一个“数据输出值”的寄存器来读取当前的模拟输出值，因为DAC是一个数字到模拟的转换器，其输出是模拟信号。

```c
uint16_t DAC_GetDataOutputValue(uint32_t DAC_Channel);
```

是用来控制DAC（数字到模拟转换器）的DMA（直接内存访问）功能的。它接受两个参数：`DAC_Channel`（要控制的DAC通道）和`NewState`（表示DMA功能的新状态，如开启或关闭）

```c
/* DMA management functions ***************************************************/
void DAC_DMACmd(uint32_t DAC_Channel, FunctionalState NewState);
```

该函数用于配置DAC（数字到模拟转换器）的中断。它接受三个参数：`DAC_Channel`（要配置的DAC通道）、`DAC_IT`（要配置的中断类型）和`NewState`（表示中断的新状态，如开启或关闭）。

```c
/* Interrupts and flags management functions **********************************/
void DAC_ITConfig(uint32_t DAC_Channel, uint32_t DAC_IT, FunctionalState NewState);
```




```c
FlagStatus DAC_GetFlagStatus(uint32_t DAC_Channel, uint32_t DAC_FLAG);
```



```c
void DAC_ClearFlag(uint32_t DAC_Channel, uint32_t DAC_FLAG);
```



```c
ITStatus DAC_GetITStatus(uint32_t DAC_Channel, uint32_t DAC_IT);
```



```c
void DAC_ClearITPendingBit(uint32_t DAC_Channel, uint32_t DAC_IT);
```





## DCMI数字摄像头接口



## TIM定时器

### 高级定时器

void TIM_ICInit(TIM_TypeDef* TIMx, TIM_ICInitTypeDef* TIM_ICInitStruct);

```c
typedef struct
{
  uint16_t TIM_Channel;
  uint16_t TIM_ICPolarity;
  uint16_t TIM_ICSelection; 
  uint16_t TIM_ICPrescaler; 
  uint16_t TIM_ICFilter;
} TIM_ICInitTypeDef;
```

- TIM_Channel：选择通道
- TIM_ICPolarity：输入活动沿
  - TIM_ICPolarity_Rising TIM 输入捕获上升沿
  - TIM_ICPolarity_Falling TIM 输入捕获下降沿
- TIM_ICSelection：选择输入
  - TIM_ICSelection_DirectTI TIM 输入 2，3 或 4 选择对应地与 IC1 或 IC2 或 IC3 或 IC4 相连
  - TIM_ICSelection_IndirectTI TIM 输入 2，3 或 4 选择对应地与 IC2 或 IC1 或 IC4 或 IC3 相连
  - TIM_ICSelection_TRC TIM 输入 2，3 或 4 选择与 TRC 相连
- TIM_ICPrescaler：设置输入捕获预分频器
  - TIM_ICPSC_DIV1 TIM 捕获在捕获输入上每探测到一个边沿执行一 次
  - TIM_ICPSC_DIV2 TIM 捕获每 2 个事件执行一次
  - TIM_ICPSC_DIV3 TIM 捕获每 3 个事件执行一次
  - TIM_ICPSC_DIV4 TIM 捕获每 4 个事件执行一次
- TIM_ICFilter选择输入比较滤波器。该参数取值在0x0和0xF之间

### 通用定时器

### 基本定时器

## RTC实时时钟



## IWDG独立看门狗 ## 



## WWDG窗口看门狗



## CRYP加密处理器



## RNG随机数发生器



## HASH散列处理器



## RTC实时时钟



## bxCAN控制器局部网



## I2C接口



## USART通用同步异步发射器



## SPI串行外设

```c
/*将SPI配置还原为默认*/ 
void SPI_I2S_DeInit(SPI_TypeDef* SPIx);//

/*初始化与配置SPI函数*/
void SPI_Init(SPI_TypeDef* SPIx, SPI_InitTypeDef* SPI_InitStruct);//SPI初始化
void I2S_Init(SPI_TypeDef* SPIx, I2S_InitTypeDef* I2S_InitStruct);//I2S初始化
void SPI_StructInit(SPI_InitTypeDef* SPI_InitStruct);//SPI按默认值初始化
void I2S_StructInit(I2S_InitTypeDef* I2S_InitStruct);//I2S按默认值初始化
void SPI_Cmd(SPI_TypeDef* SPIx, FunctionalState NewState);//SPI使能或失能
void I2S_Cmd(SPI_TypeDef* SPIx, FunctionalState NewState);//I2S使能或失能
void SPI_DataSizeConfig(SPI_TypeDef* SPIx, uint16_t SPI_DataSize);//设置选定的SPI数据大小
void SPI_BiDirectionalLineConfig(SPI_TypeDef* SPIx, uint16_t SPI_Direction);//选择指定SPI在双向模式下的数据传输方向
void SPI_NSSInternalSoftwareConfig(SPI_TypeDef* SPIx, uint16_t SPI_NSSInternalSoft);//为选定的SPI软件配置内部 NSS管脚
void SPI_SSOutputCmd(SPI_TypeDef* SPIx, FunctionalState NewState);//使能或者失能指定的SPI的SS输出
void SPI_TIModeCmd(SPI_TypeDef* SPIx, FunctionalState NewState);//SPI

void I2S_FullDuplexConfig(SPI_TypeDef* I2Sxext, I2S_InitTypeDef* I2S_InitStruct);//SPI

/*数据传输*/ 
void SPI_I2S_SendData(SPI_TypeDef* SPIx, uint16_t Data);//SPI或I2S发送一个字节
uint16_t SPI_I2S_ReceiveData(SPI_TypeDef* SPIx);//SPII或I2S接收一个字节

/*CRC校验*/
void SPI_CalculateCRC(SPI_TypeDef* SPIx, FunctionalState NewState);//使能或者失能指定SPI的传输字CRC值计算
void SPI_TransmitCRC(SPI_TypeDef* SPIx);//发送 SPIx 的 CRC 值
uint16_t SPI_GetCRC(SPI_TypeDef* SPIx, uint8_t SPI_CRC);//返回指定 SPI 的发送或者接受 CRC 寄存器值
uint16_t SPI_GetCRCPolynomial(SPI_TypeDef* SPIx);//返回指定 SPI 的 CRC 多项式寄存器值

/*DMA传输管理*/
void SPI_I2S_DMACmd(SPI_TypeDef* SPIx, uint16_t SPI_I2S_DMAReq, FunctionalState NewState);//使能或者失能指定 SPI 的 DMA 请求 

/*中断与标志位*/
void SPI_I2S_ITConfig(SPI_TypeDef* SPIx, uint8_t SPI_I2S_IT, FunctionalState NewState);//使能或者失能指定的 SPI 中断
FlagStatus SPI_I2S_GetFlagStatus(SPI_TypeDef* SPIx, uint16_t SPI_I2S_FLAG);//检查指定的 SPI 标志位设置与否
void SPI_I2S_ClearFlag(SPI_TypeDef* SPIx, uint16_t SPI_I2S_FLAG);//清除 SPIx 的待处理标志位
ITStatus SPI_I2S_GetITStatus(SPI_TypeDef* SPIx, uint8_t SPI_I2S_IT);//检查指定的 SPI 中断发生与否
void SPI_I2S_ClearITPendingBit(SPI_TypeDef* SPIx, uint8_t SPI_I2S_IT);//清除 SPIx 的中断待处理位
```

### 将SPI配置还原为默认

**将SPI配置还原为默认**

```c
/*将SPI配置还原为默认*/ 
void SPI_I2S_DeInit(SPI_TypeDef* SPIx);
```

参数为：

### 初始化与配置SPI函数

**初始化SPI**

```c
void SPI_Init(SPI_TypeDef* SPIx, SPI_InitTypeDef* SPI_InitStruct);
```

参数为一个结构体，成员为：

```c
typedef struct
{
  uint16_t SPI_Direction;//方向
  uint16_t SPI_Mode;//工作模式
  uint16_t SPI_DataSize;//数据宽度
  uint16_t SPI_CPOL;//SPI极性
  uint16_t SPI_CPHA;//SPI相位
  uint16_t SPI_NSS;//片选信号线
  uint16_t SPI_BaudRatePrescaler;//波特率分频
  uint16_t SPI_FirstBit;//先行位
  uint16_t SPI_CRCPolynomial;//CRC多项式
}SPI_InitTypeDef;
```

取值范围是为：

- SPI_Direction方向：
  - SPI_Direction_2Lines_FullDuplex SPI：设置为双线双向全双工；
  - SPI_Direction_2Lines_RxOnly SPI：设置为双线单向接收；
  - SPI_Direction_1Line_Rx SPI：设置为单线双向接收；
  - SPI_Direction_1Line_Tx SPI：设置为单线双向发送；
- SPI_Mode工作模式：
  - SPI_Mode_Master：设置为主 SPI；
  - SPI_Mode_Slave：设置为从 SPI；
- SPI_DataSize数据宽度：
  - SPI_DataSize_16b：SPI 发送接收 16 位帧结构；
  - SPI_DataSize_8b：SPI 发送接收 8 位帧结构；
- SPI_CPOL;SPI极性：
  - SPI_CPOL_High：时钟悬空高；
  - SPI_CPOL_Low：时钟悬空低；
- SPI_CPHASPI相位：
  - SPI_CPHA_2Edge：数据捕获于第二个时钟沿；
  - SPI_CPHA_1Edge：数据捕获于第一个时钟沿；
- SPI_NSS片选信号线：
  - SPI_NSS_Hard：NSS由外部管脚管理；
  - SPI_NSS_Soft：内部 NSS 信号有 SSI 位控制；
- SPI_BaudRatePrescaler波特率分频：
  - SPI_BaudRatePrescaler(X)：X为2~256任意一个2的次方。
- SPI_FirstBit先行位：
  - SPI_FisrtBit_MSB：数据传输从 MSB 位开始，高位先行；
  - SPI_FisrtBit_LSB：数据传输从 LSB 位开始，低位先行；
- SPI_CRCPolynomialCRC多项式：
  - 计算CRC的多项式，不使用CRC填7。

**I2S初始化**

```c
void I2S_Init(SPI_TypeDef* SPIx, I2S_InitTypeDef* I2S_InitStruct);
```

**SPI默认值初始化**

```c
void SPI_StructInit(SPI_InitTypeDef* SPI_InitStruct);
```

**I2S默认值初始化**

```c
void I2S_StructInit(I2S_InitTypeDef* I2S_InitStruct);
```

**SPI使能或失能**

```c
void SPI_Cmd(SPI_TypeDef* SPIx, FunctionalState NewState);
```

**I2S使能或失能**

```c
void I2S_Cmd(SPI_TypeDef* SPIx, FunctionalState NewState);
```



```c
void SPI_DataSizeConfig(SPI_TypeDef* SPIx, uint16_t SPI_DataSize);
```



```c
void SPI_BiDirectionalLineConfig(SPI_TypeDef* SPIx, uint16_t SPI_Direction);
```





```c
void SPI_NSSInternalSoftwareConfig(SPI_TypeDef* SPIx, uint16_t SPI_NSSInternalSoft);
```





```c
void SPI_SSOutputCmd(SPI_TypeDef* SPIx, FunctionalState NewState);

```





```c
void SPI_TIModeCmd(SPI_TypeDef* SPIx, FunctionalState NewState);


```





```c
void I2S_FullDuplexConfig(SPI_TypeDef* I2Sxext, I2S_InitTypeDef* I2S_InitStruct);
```









```c

/* Data transfers functions ***************************************************/ 
void SPI_I2S_SendData(SPI_TypeDef* SPIx, uint16_t Data);

```





```c
uint16_t SPI_I2S_ReceiveData(SPI_TypeDef* SPIx);


```





```c
/* Hardware CRC Calculation functions *****************************************/
void SPI_CalculateCRC(SPI_TypeDef* SPIx, FunctionalState NewState);

```





```c
void SPI_TransmitCRC(SPI_TypeDef* SPIx);

```





```c
uint16_t SPI_GetCRC(SPI_TypeDef* SPIx, uint8_t SPI_CRC);

```





```c
uint16_t SPI_GetCRCPolynomial(SPI_TypeDef* SPIx);

```





```c

/* DMA transfers management functions *****************************************/
void SPI_I2S_DMACmd(SPI_TypeDef* SPIx, uint16_t SPI_I2S_DMAReq, FunctionalState NewState);

```





```c

/* Interrupts and flags management functions **********************************/
void SPI_I2S_ITConfig(SPI_TypeDef* SPIx, uint8_t SPI_I2S_IT, FunctionalState NewState);

```





```c
FlagStatus SPI_I2S_GetFlagStatus(SPI_TypeDef* SPIx, uint16_t SPI_I2S_FLAG);

```





```c
void SPI_I2S_ClearFlag(SPI_TypeDef* SPIx, uint16_t SPI_I2S_FLAG);

```








```c
ITStatus SPI_I2S_GetITStatus(SPI_TypeDef* SPIx, uint8_t SPI_I2S_IT);

```








```c
void SPI_I2S_ClearITPendingBit(SPI_TypeDef* SPIx, uint8_t SPI_I2S_IT);
```










## SDIO接口



## MAC以太网 

## USB全速

## USB高速

## FSMC静态储存控制器

## 器件签名

## DBG调试支持





