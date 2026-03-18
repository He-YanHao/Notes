# STM32F4时钟

可以使用三种不同的时钟源来驱动系统时钟 (SYSCLK)：

- HSI 振荡器时钟
- HSE 振荡器时钟
- 主PLL (PLL) 时钟

器件具有以下两个次级时钟源：

- 32 kHz 低速内部 RC (LSI RC)，该 RC 用于驱动独立看门狗，也可选择提供给 RTC 用于停机/待机模式下的自动唤醒。
- 32.768 kHz 低速外部晶振（LSE 晶振），用于驱动 RTC 时钟 (RTCCLK)。

对于每个时钟源来说，在未使用时都可单独打开或者关闭，以降低功耗。

既然要开启时钟，那我们就要去关于时钟的库函数里去查找，打开STM32f4xx_rcc.h头文件，里面有很详细的关于时钟的函数的声明，到里面去查找我们需要的即可。查找一般可根据函数名去定义它的功能。我们要去开启AHB1总线中的GPIOB的时钟，因为我们的GPIOB是挂载在AHB1总线上面的。

```C
RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_GPIOB, ENABLE);
```

## 时钟树

由于STM32外设较为复杂，所以时钟需求不同，就有了挂载在不同地方且频率不同的时钟。

STM32F4系列有2个外部时钟源：

* 高速外部振荡器HSE (High Speed External Clock signal)外接石英/陶瓷谐振器，频率为4MHz~26MHz。

* 低速外部振荡器LSE (Low Speed External Clock signal)外接32.768kHz石英晶体，主要作用于RTC的时钟源。

2个外部时钟源都是芯片外部晶振产生的时钟频率，故而都有精度高的优点。

和2个内部时钟源：

* 高速内部振荡器HSI(High Speed Internal Clock signal)由内部RC 振荡器产生，频率为16MHz。
* 低速内部振荡器LSI(Low Speed Internal Clock signal)由内部RC振荡器产生，频率为32kHz，可作为独立看门狗的时钟源。

芯片上电时默认由内部的HSI时钟启动，如果用户进行了硬件和软件的配置，芯片才会根据用户配置调试尝试切换到对应的外部时钟源。

STM32的系统时钟SYSCLK为整个芯片提供了时序信号。

片上外设区分为三条总线，根据外设速度的不同，不同总线挂载着不同的外设，APB1挂载低速外设，APB2和AHB挂载高速外设。

相应总线的最低地址我们称为该总线的基地址，总线基地址也是挂载在该总线上的首个外设的地址。其中APB1总线的地址最低，片上外设从这里开始， 也叫外设基地址。

## SysTick定时器

SysTick定时器可用作标准的下行计数器，是一个24位向下计数器，有自动重新装载能力，可屏蔽系统中断发生器。Cortex-M4处理器内部包含了一个简单的定时器，所有基于M4内核的控制器都带有SysTick定时器，这样就方便了程序在不同的器件之间的移植。SysTick定时器可用于操作系统，提供系统必要的时钟节拍，为操作系统的任务调度提供一个有节奏的“心跳”。正因为所有的M4内核的芯片都有Systick定时器，这在移植的时候不像普通定时器那样难以移植。

RCU 通过 AHB 时钟（HCLK）8 分频后作为 Cortex 系统定时器（SysTick）的外部时钟。通过对 SysTick 控制和状态寄存器的设置，可选择上述时钟或 AHB（HCLK）时钟作为 SysTick 时钟。

SysTick定时器设定初值并使能之后，每经过1个系统时钟周期，计数值就减1，减到0时，SysTick计数器自动重新装载初值并继续计数，同时内部的COUNTFLAG标志位被置位，触发中断（前提开启中断）。

## 信号线：

AHB高级高性能总线APB

### AHB1

- GPIOA~GPIOK
- CRC
- FLITF
- SRAM1~3
- BKPSRAM
- CCMDATARAMEN
- DMA1~2 2D
- `ETH_MAC` `ETH_MAC_Tx` `ETH_MAC_Rx` `ETH_MAC_PTP`
- `OTG_HS` `OTG_HS_ULPI`
- RNG
- CLOCK_PERIPH(PERIPH)
- RESET_PERIPH(PERIPH)
- LPMODE_PERIPH(PERIPH)
- 
  

### AHB2

APB2总线时钟，由HCLK经过高速APB2预分频器得到。与APB2高速总线连接的外设有定时器1/8/9/10/11、SPI1、USART1和USART6、3个ADC和SDIO接口（针对STM32F4系列而言）。

此外，AHB总线时钟直接作为GPIO(A\B\C\D\E\F\G\H\I\)、以太网、DCMI、FSMC、AHB总线、Cortex内核、存储器和DMA的HCLK时钟，并作为Cortex内核自由运行时钟FCLK。

- DCMI
- CRYP
- HASH
- RNG
- OTG_FS
- PERIPH(PERIPH)

### AHB3

- FSMC
- PERIPH(PERIPH)
- FMC
- PERIPH(PERIPH)
- FMC
- QSPI
- PERIPH(PERIPH)
- FSMC
- QSPI
- PERIPH(PERIPH)

### APB1

APB1总线时钟，由HCLK经过低速APB1预分频器得到。由于APB1是低速总线时钟，APB1总线最高频率为42MHz，片上低速的外设就挂载在该总线上，例如有看门狗定时器、定时器2/3/4/5/6/7、RTC时钟、USART2/3/4/5、SPI2(I2S2)与SPI3(I2S3)、I2C1~3、CAN和2个DAC（针对STM32F4系列而言）。

- `TIM2~7` `TIM12~14`
- LPTIM1
- WWDG
- SPI2~3
- SPDIFRX
- USART2~3
- UART4~5 `UART7~8`
- I2C1~3
- FMPI2C1
- CAN1~3
- CEC
- PWR
- DAC
- PERIPH(PERIPH)

### APB2

- `TIM1` `TIM8~11`
- USART1 USART6
- UART9~10
- ADC ADC1~3
- SDIO
- SPI1 SPI4~6
- SYSCFG
- EXTIT
- SAI1~2
- LTDC
- DSI
- DFSDM DFSDM1~2
- PERIPH(PERIPH)
- RESET_PERIPH(PERIPH)

### MCO1

- HSI
- LSE
- HSE
- PLLCLK
- Div_1~5
- IS_RCC_MCO1SOURCE(SOURCE)
- IS_RCC_MCO1DIV(DIV)

### MCO2

- SYSCLK
- PLLI2SCLK
- HSE
- PLLCLK
- Div_1~5
- IS_RCC_MCO2SOURCE(SOURCE)
- IS_RCC_MCO2DIV(DIV)




