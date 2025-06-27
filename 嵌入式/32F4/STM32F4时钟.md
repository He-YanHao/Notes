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



```c
typedef struct
{
  uint32_t SYSCLK_Frequency; /*!<  SYSCLK clock frequency expressed in Hz */
  uint32_t HCLK_Frequency;   /*!<  HCLK clock frequency expressed in Hz   */
  uint32_t PCLK1_Frequency;  /*!<  PCLK1 clock frequency expressed in Hz  */
  uint32_t PCLK2_Frequency;  /*!<  PCLK2 clock frequency expressed in Hz  */
}RCC_ClocksTypeDef;
#define RCC_HSE_OFF                      ((uint8_t)0x00)
#define RCC_HSE_ON                       ((uint8_t)0x01)
#define RCC_HSE_Bypass                   ((uint8_t)0x05)
#define IS_RCC_HSE(HSE) (((HSE) == RCC_HSE_OFF) || ((HSE) == RCC_HSE_ON) || \
                         ((HSE) == RCC_HSE_Bypass))
#define RCC_LSE_LOWPOWER_MODE           ((uint8_t)0x00)
#define RCC_LSE_HIGHDRIVE_MODE          ((uint8_t)0x01)
#define IS_RCC_LSE_MODE(MODE)           (((MODE) == RCC_LSE_LOWPOWER_MODE) || \
                                         ((MODE) == RCC_LSE_HIGHDRIVE_MODE))
#define RCC_PLLSAIDivR_Div2                ((uint32_t)0x00000000)
#define RCC_PLLSAIDivR_Div4                ((uint32_t)0x00010000)
#define RCC_PLLSAIDivR_Div8                ((uint32_t)0x00020000)
#define RCC_PLLSAIDivR_Div16               ((uint32_t)0x00030000)
#define IS_RCC_PLLSAI_DIVR_VALUE(VALUE) (((VALUE) == RCC_PLLSAIDivR_Div2) ||\
                                        ((VALUE) == RCC_PLLSAIDivR_Div4)  ||\
                                        ((VALUE) == RCC_PLLSAIDivR_Div8)  ||\
                                        ((VALUE) == RCC_PLLSAIDivR_Div16))
#define RCC_PLLSource_HSI                ((uint32_t)0x00000000)
#define RCC_PLLSource_HSE                ((uint32_t)0x00400000)
#define IS_RCC_PLL_SOURCE(SOURCE) (((SOURCE) == RCC_PLLSource_HSI) || \
                                   ((SOURCE) == RCC_PLLSource_HSE))
#define IS_RCC_PLLM_VALUE(VALUE) ((VALUE) <= 63)
#define IS_RCC_PLLN_VALUE(VALUE) ((50 <= (VALUE)) && ((VALUE) <= 432))
#define IS_RCC_PLLP_VALUE(VALUE) (((VALUE) == 2) || ((VALUE) == 4) || ((VALUE) == 6) || ((VALUE) == 8))
#define IS_RCC_PLLQ_VALUE(VALUE) ((4 <= (VALUE)) && ((VALUE) <= 15))
#if defined(STM32F410xx) || defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx) || defined(STM32F469_479xx)
#define IS_RCC_PLLR_VALUE(VALUE) ((2 <= (VALUE)) && ((VALUE) <= 7))
#endif /* STM32F410xx || STM32F412xG || STM32F413_423xx || STM32F446xx || STM32F469_479xx */

#define IS_RCC_PLLI2SN_VALUE(VALUE) ((50 <= (VALUE)) && ((VALUE) <= 432))
#define IS_RCC_PLLI2SR_VALUE(VALUE) ((2 <= (VALUE))  && ((VALUE) <= 7))
#define IS_RCC_PLLI2SM_VALUE(VALUE) ((2 <= (VALUE))  && ((VALUE) <= 63))
#define IS_RCC_PLLI2SQ_VALUE(VALUE) ((2 <= (VALUE))  && ((VALUE) <= 15))
#if defined(STM32F446xx) 
#define IS_RCC_PLLI2SP_VALUE(VALUE) (((VALUE) == 2) || ((VALUE) == 4) || ((VALUE) == 6) || ((VALUE) == 8))
#define IS_RCC_PLLSAIM_VALUE(VALUE) ((VALUE) <= 63)
#elif  defined(STM32F412xG) || defined(STM32F413_423xx)
#define IS_RCC_PLLI2SP_VALUE(VALUE) (((VALUE) == 2) || ((VALUE) == 4) || ((VALUE) == 6) || ((VALUE) == 8))
#else
#endif /* STM32F446xx */
#define IS_RCC_PLLSAIN_VALUE(VALUE) ((50 <= (VALUE)) && ((VALUE) <= 432))
#if defined(STM32F446xx) || defined(STM32F469_479xx)
#define IS_RCC_PLLSAIP_VALUE(VALUE) (((VALUE) == 2) || ((VALUE) == 4) || ((VALUE) == 6) || ((VALUE) == 8))
#endif /* STM32F446xx || STM32F469_479xx */
#define IS_RCC_PLLSAIQ_VALUE(VALUE) ((2 <= (VALUE)) && ((VALUE) <= 15))
#define IS_RCC_PLLSAIR_VALUE(VALUE) ((2 <= (VALUE)) && ((VALUE) <= 7))  

#define IS_RCC_PLLSAI_DIVQ_VALUE(VALUE) ((1 <= (VALUE)) && ((VALUE) <= 32))
#define IS_RCC_PLLI2S_DIVQ_VALUE(VALUE) ((1 <= (VALUE)) && ((VALUE) <= 32))

#if defined(STM32F413_423xx)
#define IS_RCC_PLLI2S_DIVR_VALUE(VALUE) ((1 <= (VALUE)) && ((VALUE) <= 32))
#define IS_RCC_PLL_DIVR_VALUE(VALUE)    ((1 <= (VALUE)) && ((VALUE) <= 32))
#endif /* STM32F413_423xx */
#if  defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx)
#define RCC_SYSCLKSource_HSI             ((uint32_t)0x00000000)
#define RCC_SYSCLKSource_HSE             ((uint32_t)0x00000001)
#define RCC_SYSCLKSource_PLLPCLK         ((uint32_t)0x00000002)
#define RCC_SYSCLKSource_PLLRCLK         ((uint32_t)0x00000003)
#define IS_RCC_SYSCLK_SOURCE(SOURCE) (((SOURCE) == RCC_SYSCLKSource_HSI) || \
                                      ((SOURCE) == RCC_SYSCLKSource_HSE) || \
                                      ((SOURCE) == RCC_SYSCLKSource_PLLPCLK) || \
                                      ((SOURCE) == RCC_SYSCLKSource_PLLRCLK))
/* Add legacy definition */
#define  RCC_SYSCLKSource_PLLCLK    RCC_SYSCLKSource_PLLPCLK  
#endif /* STM32F446xx */

#if defined(STM32F40_41xxx) || defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F401xx) || defined(STM32F410xx) || defined(STM32F411xE) || defined(STM32F469_479xx)
#define RCC_SYSCLKSource_HSI             ((uint32_t)0x00000000)
#define RCC_SYSCLKSource_HSE             ((uint32_t)0x00000001)
#define RCC_SYSCLKSource_PLLCLK          ((uint32_t)0x00000002)
#define IS_RCC_SYSCLK_SOURCE(SOURCE) (((SOURCE) == RCC_SYSCLKSource_HSI) || \
                                      ((SOURCE) == RCC_SYSCLKSource_HSE) || \
                                      ((SOURCE) == RCC_SYSCLKSource_PLLCLK))
#define RCC_SYSCLK_Div1                  ((uint32_t)0x00000000)
#define RCC_SYSCLK_Div2                  ((uint32_t)0x00000080)
#define RCC_SYSCLK_Div4                  ((uint32_t)0x00000090)
#define RCC_SYSCLK_Div8                  ((uint32_t)0x000000A0)
#define RCC_SYSCLK_Div16                 ((uint32_t)0x000000B0)
#define RCC_SYSCLK_Div64                 ((uint32_t)0x000000C0)
#define RCC_SYSCLK_Div128                ((uint32_t)0x000000D0)
#define RCC_SYSCLK_Div256                ((uint32_t)0x000000E0)
#define RCC_SYSCLK_Div512                ((uint32_t)0x000000F0)
#define IS_RCC_HCLK(HCLK) (((HCLK) == RCC_SYSCLK_Div1) || ((HCLK) == RCC_SYSCLK_Div2) || \
                           ((HCLK) == RCC_SYSCLK_Div4) || ((HCLK) == RCC_SYSCLK_Div8) || \
                           ((HCLK) == RCC_SYSCLK_Div16) || ((HCLK) == RCC_SYSCLK_Div64) || \
                           ((HCLK) == RCC_SYSCLK_Div128) || ((HCLK) == RCC_SYSCLK_Div256) || \
                           ((HCLK) == RCC_SYSCLK_Div512))
#define RCC_HCLK_Div1                    ((uint32_t)0x00000000)
#define RCC_HCLK_Div2                    ((uint32_t)0x00001000)
#define RCC_HCLK_Div4                    ((uint32_t)0x00001400)
#define RCC_HCLK_Div8                    ((uint32_t)0x00001800)
#define RCC_HCLK_Div16                   ((uint32_t)0x00001C00)
#define IS_RCC_PCLK(PCLK) (((PCLK) == RCC_HCLK_Div1) || ((PCLK) == RCC_HCLK_Div2) || \
                           ((PCLK) == RCC_HCLK_Div4) || ((PCLK) == RCC_HCLK_Div8) || \
                           ((PCLK) == RCC_HCLK_Div16))
#define RCC_IT_LSIRDY                    ((uint8_t)0x01)
#define RCC_IT_LSERDY                    ((uint8_t)0x02)
#define RCC_IT_HSIRDY                    ((uint8_t)0x04)
#define RCC_IT_HSERDY                    ((uint8_t)0x08)
#define RCC_IT_PLLRDY                    ((uint8_t)0x10)
#define RCC_IT_PLLI2SRDY                 ((uint8_t)0x20) 
#define RCC_IT_PLLSAIRDY                 ((uint8_t)0x40)
#define RCC_IT_CSS                       ((uint8_t)0x80)

#define IS_RCC_IT(IT) ((((IT) & (uint8_t)0x80) == 0x00) && ((IT) != 0x00))
#define IS_RCC_GET_IT(IT) (((IT) == RCC_IT_LSIRDY) || ((IT) == RCC_IT_LSERDY) || \
                           ((IT) == RCC_IT_HSIRDY) || ((IT) == RCC_IT_HSERDY) || \
                           ((IT) == RCC_IT_PLLRDY) || ((IT) == RCC_IT_CSS) || \
                           ((IT) == RCC_IT_PLLSAIRDY) || ((IT) == RCC_IT_PLLI2SRDY))
#define IS_RCC_CLEAR_IT(IT)((IT) != 0x00)
#define RCC_LSE_OFF                      ((uint8_t)0x00)
#define RCC_LSE_ON                       ((uint8_t)0x01)
#define RCC_LSE_Bypass                   ((uint8_t)0x04)
#define IS_RCC_LSE(LSE) (((LSE) == RCC_LSE_OFF) || ((LSE) == RCC_LSE_ON) || \
                         ((LSE) == RCC_LSE_Bypass))
#define RCC_RTCCLKSource_LSE             ((uint32_t)0x00000100)
#define RCC_RTCCLKSource_LSI             ((uint32_t)0x00000200)
#define RCC_RTCCLKSource_HSE_Div2        ((uint32_t)0x00020300)
#define RCC_RTCCLKSource_HSE_Div3        ((uint32_t)0x00030300)
#define RCC_RTCCLKSource_HSE_Div4        ((uint32_t)0x00040300)
#define RCC_RTCCLKSource_HSE_Div5        ((uint32_t)0x00050300)
#define RCC_RTCCLKSource_HSE_Div6        ((uint32_t)0x00060300)
#define RCC_RTCCLKSource_HSE_Div7        ((uint32_t)0x00070300)
#define RCC_RTCCLKSource_HSE_Div8        ((uint32_t)0x00080300)
#define RCC_RTCCLKSource_HSE_Div9        ((uint32_t)0x00090300)
#define RCC_RTCCLKSource_HSE_Div10       ((uint32_t)0x000A0300)
#define RCC_RTCCLKSource_HSE_Div11       ((uint32_t)0x000B0300)
#define RCC_RTCCLKSource_HSE_Div12       ((uint32_t)0x000C0300)
#define RCC_RTCCLKSource_HSE_Div13       ((uint32_t)0x000D0300)
#define RCC_RTCCLKSource_HSE_Div14       ((uint32_t)0x000E0300)
#define RCC_RTCCLKSource_HSE_Div15       ((uint32_t)0x000F0300)
#define RCC_RTCCLKSource_HSE_Div16       ((uint32_t)0x00100300)
#define RCC_RTCCLKSource_HSE_Div17       ((uint32_t)0x00110300)
#define RCC_RTCCLKSource_HSE_Div18       ((uint32_t)0x00120300)
#define RCC_RTCCLKSource_HSE_Div19       ((uint32_t)0x00130300)
#define RCC_RTCCLKSource_HSE_Div20       ((uint32_t)0x00140300)
#define RCC_RTCCLKSource_HSE_Div21       ((uint32_t)0x00150300)
#define RCC_RTCCLKSource_HSE_Div22       ((uint32_t)0x00160300)
#define RCC_RTCCLKSource_HSE_Div23       ((uint32_t)0x00170300)
#define RCC_RTCCLKSource_HSE_Div24       ((uint32_t)0x00180300)
#define RCC_RTCCLKSource_HSE_Div25       ((uint32_t)0x00190300)
#define RCC_RTCCLKSource_HSE_Div26       ((uint32_t)0x001A0300)
#define RCC_RTCCLKSource_HSE_Div27       ((uint32_t)0x001B0300)
#define RCC_RTCCLKSource_HSE_Div28       ((uint32_t)0x001C0300)
#define RCC_RTCCLKSource_HSE_Div29       ((uint32_t)0x001D0300)
#define RCC_RTCCLKSource_HSE_Div30       ((uint32_t)0x001E0300)
#define RCC_RTCCLKSource_HSE_Div31       ((uint32_t)0x001F0300)
#define IS_RCC_RTCCLK_SOURCE(SOURCE) (((SOURCE) == RCC_RTCCLKSource_LSE) || \
                                      ((SOURCE) == RCC_RTCCLKSource_LSI) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div2) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div3) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div4) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div5) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div6) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div7) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div8) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div9) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div10) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div11) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div12) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div13) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div14) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div15) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div16) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div17) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div18) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div19) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div20) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div21) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div22) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div23) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div24) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div25) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div26) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div27) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div28) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div29) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div30) || \
                                      ((SOURCE) == RCC_RTCCLKSource_HSE_Div31))
#if defined(STM32F410xx) || defined(STM32F413_423xx)
#define RCC_LPTIM1CLKSOURCE_PCLK            ((uint32_t)0x00000000)
#define RCC_LPTIM1CLKSOURCE_HSI            ((uint32_t)RCC_DCKCFGR2_LPTIM1SEL_0)
#define RCC_LPTIM1CLKSOURCE_LSI            ((uint32_t)RCC_DCKCFGR2_LPTIM1SEL_1)
#define RCC_LPTIM1CLKSOURCE_LSE            ((uint32_t)RCC_DCKCFGR2_LPTIM1SEL_0 | RCC_DCKCFGR2_LPTIM1SEL_1)

#define IS_RCC_LPTIM1_CLOCKSOURCE(SOURCE) (((SOURCE) == RCC_LPTIM1CLKSOURCE_PCLK) || ((SOURCE) == RCC_LPTIM1CLKSOURCE_HSI) || \
                                           ((SOURCE) == RCC_LPTIM1CLKSOURCE_LSI) || ((SOURCE) == RCC_LPTIM1CLKSOURCE_LSE))
/* Legacy Defines */
#define IS_RCC_LPTIM1_SOURCE           IS_RCC_LPTIM1_CLOCKSOURCE
#if defined(STM32F410xx)
#define RCC_I2SAPBCLKSOURCE_PLLR            ((uint32_t)0x00000000)
#define RCC_I2SAPBCLKSOURCE_EXT             ((uint32_t)RCC_DCKCFGR_I2SSRC_0)
#define RCC_I2SAPBCLKSOURCE_PLLSRC          ((uint32_t)RCC_DCKCFGR_I2SSRC_1)
#define IS_RCC_I2SCLK_SOURCE(SOURCE) (((SOURCE) == RCC_I2SAPBCLKSOURCE_PLLR) || ((SOURCE) == RCC_I2SAPBCLKSOURCE_EXT) || \
                                      ((SOURCE) == RCC_I2SAPBCLKSOURCE_PLLSRC)) 
#if defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx)
#define RCC_I2SCLKSource_PLLI2S             ((uint32_t)0x00)
#define RCC_I2SCLKSource_Ext                ((uint32_t)RCC_DCKCFGR_I2S1SRC_0)
#define RCC_I2SCLKSource_PLL                ((uint32_t)RCC_DCKCFGR_I2S1SRC_1)
#define RCC_I2SCLKSource_HSI_HSE            ((uint32_t)RCC_DCKCFGR_I2S1SRC_0 | RCC_DCKCFGR_I2S1SRC_1)

#define IS_RCC_I2SCLK_SOURCE(SOURCE) (((SOURCE) == RCC_I2SCLKSource_PLLI2S) || ((SOURCE) == RCC_I2SCLKSource_Ext) || \
                                      ((SOURCE) == RCC_I2SCLKSource_PLL) || ((SOURCE) == RCC_I2SCLKSource_HSI_HSE))                                
#define RCC_I2SBus_APB1             ((uint8_t)0x00)
#define RCC_I2SBus_APB2             ((uint8_t)0x01)
#define IS_RCC_I2S_APBx(BUS) (((BUS) == RCC_I2SBus_APB1) || ((BUS) == RCC_I2SBus_APB2))                                
/**
  * @}
  */
#if defined(STM32F446xx)    
/** @defgroup RCC_SAI_Clock_Source
  * @{
  */
#define RCC_SAICLKSource_PLLSAI             ((uint32_t)0x00)
#define RCC_SAICLKSource_PLLI2S             ((uint32_t)RCC_DCKCFGR_SAI1SRC_0)
#define RCC_SAICLKSource_PLL                ((uint32_t)RCC_DCKCFGR_SAI1SRC_1)
#define RCC_SAICLKSource_HSI_HSE            ((uint32_t)RCC_DCKCFGR_SAI1SRC_0 | RCC_DCKCFGR_SAI1SRC_1)

#define IS_RCC_SAICLK_SOURCE(SOURCE) (((SOURCE) == RCC_SAICLKSource_PLLSAI) || ((SOURCE) == RCC_SAICLKSource_PLLI2S) || \
                                      ((SOURCE) == RCC_SAICLKSource_PLL) || ((SOURCE) == RCC_SAICLKSource_HSI_HSE))                                
/**
  * @}
  */    
    
/** @defgroup RCC_SAI_Instance
  * @{
  */
#define RCC_SAIInstance_SAI1             ((uint8_t)0x00)
#define RCC_SAIInstance_SAI2             ((uint8_t)0x01)
#define IS_RCC_SAI_INSTANCE(BUS) (((BUS) == RCC_SAIInstance_SAI1) || ((BUS) == RCC_SAIInstance_SAI2))                                
/**
  * @}
  */
#endif /* STM32F446xx */
#if defined(STM32F413_423xx)    

/** @defgroup RCC_SAI_BlockA_Clock_Source
  * @{
  */
#define RCC_SAIACLKSource_PLLI2S_R             ((uint32_t)0x00000000)
#define RCC_SAIACLKSource_I2SCKIN              ((uint32_t)RCC_DCKCFGR_SAI1ASRC_0)
#define RCC_SAIACLKSource_PLLR                 ((uint32_t)RCC_DCKCFGR_SAI1ASRC_1)
#define RCC_SAIACLKSource_HSI_HSE              ((uint32_t)RCC_DCKCFGR_SAI1ASRC_0 | RCC_DCKCFGR_SAI1ASRC_1)

#define IS_RCC_SAIACLK_SOURCE(SOURCE) (((SOURCE) == RCC_SAIACLKSource_PLLI2S_R) || ((SOURCE) == RCC_SAIACLKSource_I2SCKIN) || \
                                      ((SOURCE) == RCC_SAIACLKSource_PLLR) || ((SOURCE) == RCC_SAIACLKSource_HSI_HSE))
/**
  * @}
  */

/** @defgroup RCC_SAI_BlockB_Clock_Source
  * @{
  */
#define RCC_SAIBCLKSource_PLLI2S_R             ((uint32_t)0x00000000)
#define RCC_SAIBCLKSource_I2SCKIN              ((uint32_t)RCC_DCKCFGR_SAI1BSRC_0)
#define RCC_SAIBCLKSource_PLLR                 ((uint32_t)RCC_DCKCFGR_SAI1BSRC_1)
#define RCC_SAIBCLKSource_HSI_HSE              ((uint32_t)RCC_DCKCFGR_SAI1BSRC_0 | RCC_DCKCFGR_SAI1BSRC_1)

#define IS_RCC_SAIBCLK_SOURCE(SOURCE) (((SOURCE) == RCC_SAIBCLKSource_PLLI2S_R) || ((SOURCE) == RCC_SAIBCLKSource_I2SCKIN) || \
                                      ((SOURCE) == RCC_SAIBCLKSource_PLLR) || ((SOURCE) == RCC_SAIBCLKSource_HSI_HSE))
/**
  * @}
  */
#endif /* STM32F413_423xx */
#endif /* STM32F412xG || STM32F413_423xx || STM32F446xx */

#if defined(STM32F40_41xxx) || defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F401xx) || defined(STM32F411xE) || defined(STM32F469_479xx)
/** @defgroup RCC_I2S_Clock_Source
  * @{
  */
#define RCC_I2S2CLKSource_PLLI2S             ((uint8_t)0x00)
#define RCC_I2S2CLKSource_Ext                ((uint8_t)0x01)

#define IS_RCC_I2SCLK_SOURCE(SOURCE) (((SOURCE) == RCC_I2S2CLKSource_PLLI2S) || ((SOURCE) == RCC_I2S2CLKSource_Ext))                                
/**
  * @}
  */ 

/** @defgroup RCC_SAI_BlockA_Clock_Source
  * @{
  */
#define RCC_SAIACLKSource_PLLSAI             ((uint32_t)0x00000000)
#define RCC_SAIACLKSource_PLLI2S             ((uint32_t)0x00100000)
#define RCC_SAIACLKSource_Ext                ((uint32_t)0x00200000)

#define IS_RCC_SAIACLK_SOURCE(SOURCE) (((SOURCE) == RCC_SAIACLKSource_PLLI2S) ||\
                                       ((SOURCE) == RCC_SAIACLKSource_PLLSAI) ||\
                                       ((SOURCE) == RCC_SAIACLKSource_Ext))
/**
  * @}
  */ 

/** @defgroup RCC_SAI_BlockB_Clock_Source
  * @{
  */
#define RCC_SAIBCLKSource_PLLSAI             ((uint32_t)0x00000000)
#define RCC_SAIBCLKSource_PLLI2S             ((uint32_t)0x00400000)
#define RCC_SAIBCLKSource_Ext                ((uint32_t)0x00800000)

#define IS_RCC_SAIBCLK_SOURCE(SOURCE) (((SOURCE) == RCC_SAIBCLKSource_PLLI2S) ||\
                                       ((SOURCE) == RCC_SAIBCLKSource_PLLSAI) ||\
                                       ((SOURCE) == RCC_SAIBCLKSource_Ext))
/**
  * @}
  */ 
#endif /* STM32F40_41xxx || STM32F427_437xx || STM32F429_439xx || STM32F401xx || STM32F411xE || STM32F469_479xx */

/** @defgroup RCC_TIM_PRescaler_Selection
  * @{
  */
#define RCC_TIMPrescDesactivated             ((uint8_t)0x00)
#define RCC_TIMPrescActivated                ((uint8_t)0x01)

#define IS_RCC_TIMCLK_PRESCALER(VALUE) (((VALUE) == RCC_TIMPrescDesactivated) || ((VALUE) == RCC_TIMPrescActivated))
/**
  * @}
  */

#if defined(STM32F469_479xx)
/** @defgroup RCC_DSI_Clock_Source_Selection
  * @{
  */
#define RCC_DSICLKSource_PHY                ((uint8_t)0x00)
#define RCC_DSICLKSource_PLLR               ((uint8_t)0x01)
#define IS_RCC_DSI_CLOCKSOURCE(CLKSOURCE)   (((CLKSOURCE) == RCC_DSICLKSource_PHY) || \
                                             ((CLKSOURCE) == RCC_DSICLKSource_PLLR))
/**
  * @}
  */
#endif /* STM32F469_479xx */

#if  defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx) || defined(STM32F469_479xx)
/** @defgroup RCC_SDIO_Clock_Source_Selection
  * @{
  */
#define RCC_SDIOCLKSource_48MHZ              ((uint8_t)0x00)
#define RCC_SDIOCLKSource_SYSCLK             ((uint8_t)0x01)
#define IS_RCC_SDIO_CLOCKSOURCE(CLKSOURCE)   (((CLKSOURCE) == RCC_SDIOCLKSource_48MHZ) || \
                                              ((CLKSOURCE) == RCC_SDIOCLKSource_SYSCLK))
/**
  * @}
  */


/** @defgroup RCC_48MHZ_Clock_Source_Selection
  * @{
  */
#if  defined(STM32F446xx) || defined(STM32F469_479xx)
#define RCC_48MHZCLKSource_PLL                ((uint8_t)0x00)
#define RCC_48MHZCLKSource_PLLSAI             ((uint8_t)0x01)
#define IS_RCC_48MHZ_CLOCKSOURCE(CLKSOURCE)   (((CLKSOURCE) == RCC_48MHZCLKSource_PLL) || \
                                               ((CLKSOURCE) == RCC_48MHZCLKSource_PLLSAI))
#endif /* STM32F446xx || STM32F469_479xx */
#if defined(STM32F412xG) || defined(STM32F413_423xx)
#define RCC_CK48CLKSOURCE_PLLQ                ((uint8_t)0x00)
#define RCC_CK48CLKSOURCE_PLLI2SQ             ((uint8_t)0x01) /* Only for STM32F412xG and STM32F413_423xx Devices */    
#define IS_RCC_48MHZ_CLOCKSOURCE(CLKSOURCE)   (((CLKSOURCE) == RCC_CK48CLKSOURCE_PLLQ) || \
                                               ((CLKSOURCE) == RCC_CK48CLKSOURCE_PLLI2SQ))
#endif /* STM32F412xG || STM32F413_423xx */
/**
  * @}
  */
#endif /* STM32F412xG || STM32F413_423xx || STM32F446xx || STM32F469_479xx */

#if defined(STM32F446xx) 
/** @defgroup RCC_SPDIFRX_Clock_Source_Selection
  * @{
  */
#define RCC_SPDIFRXCLKSource_PLLR                 ((uint8_t)0x00)
#define RCC_SPDIFRXCLKSource_PLLI2SP              ((uint8_t)0x01)
#define IS_RCC_SPDIFRX_CLOCKSOURCE(CLKSOURCE)     (((CLKSOURCE) == RCC_SPDIFRXCLKSource_PLLR) || \
                                                   ((CLKSOURCE) == RCC_SPDIFRXCLKSource_PLLI2SP))
/**
  * @}
  */

/** @defgroup RCC_CEC_Clock_Source_Selection
  * @{
  */
#define RCC_CECCLKSource_HSIDiv488            ((uint8_t)0x00)
#define RCC_CECCLKSource_LSE                  ((uint8_t)0x01)
#define IS_RCC_CEC_CLOCKSOURCE(CLKSOURCE)     (((CLKSOURCE) == RCC_CECCLKSource_HSIDiv488) || \
                                               ((CLKSOURCE) == RCC_CECCLKSource_LSE))
#define RCC_AHB1ClockGating_APB1Bridge         ((uint32_t)0x00000001)
#define RCC_AHB1ClockGating_APB2Bridge         ((uint32_t)0x00000002)
#define RCC_AHB1ClockGating_CM4DBG             ((uint32_t)0x00000004)
#define RCC_AHB1ClockGating_SPARE              ((uint32_t)0x00000008)
#define RCC_AHB1ClockGating_SRAM               ((uint32_t)0x00000010)
#define RCC_AHB1ClockGating_FLITF              ((uint32_t)0x00000020)
#define RCC_AHB1ClockGating_RCC                ((uint32_t)0x00000040)

#define IS_RCC_AHB1_CLOCKGATING(PERIPH) ((((PERIPH) & 0xFFFFFF80) == 0x00) && ((PERIPH) != 0x00))

/**
  * @}
  */
#endif /* STM32F446xx */

#if defined(STM32F410xx) || defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx)
/** @defgroup RCC_FMPI2C1_Clock_Source
  * @{
  */
#define RCC_FMPI2C1CLKSource_APB1            ((uint32_t)0x00)
#define RCC_FMPI2C1CLKSource_SYSCLK          ((uint32_t)RCC_DCKCFGR2_FMPI2C1SEL_0)
#define RCC_FMPI2C1CLKSource_HSI             ((uint32_t)RCC_DCKCFGR2_FMPI2C1SEL_1)
    
#define IS_RCC_FMPI2C1_CLOCKSOURCE(SOURCE) (((SOURCE) == RCC_FMPI2C1CLKSource_APB1) || ((SOURCE) == RCC_FMPI2C1CLKSource_SYSCLK) || \
                                            ((SOURCE) == RCC_FMPI2C1CLKSource_HSI))
/**
  * @}
  */
#endif /* STM32F410xx || STM32F412xG || STM32F413_423xx || STM32F446xx */

#if defined(STM32F412xG) || defined(STM32F413_423xx)
/** @defgroup RCC_DFSDM_Clock_Source
 * @{
 */
#define RCC_DFSDMCLKSource_APB             ((uint8_t)0x00)
#define RCC_DFSDMCLKSource_SYS             ((uint8_t)0x01)
#define IS_RCC_DFSDMCLK_SOURCE(SOURCE) (((SOURCE) == RCC_DFSDMCLKSource_APB) || ((SOURCE) == RCC_DFSDMCLKSource_SYS))

/* Legacy Defines */
#define RCC_DFSDM1CLKSource_APB   RCC_DFSDMCLKSource_APB
#define RCC_DFSDM1CLKSource_SYS   RCC_DFSDMCLKSource_SYS
#define IS_RCC_DFSDM1CLK_SOURCE   IS_RCC_DFSDMCLK_SOURCE
/**
  * @}
  */

/** @defgroup RCC_DFSDM_Audio_Clock_Source  RCC DFSDM Audio Clock Source
  * @{
  */
#define RCC_DFSDM1AUDIOCLKSOURCE_I2SAPB1          ((uint32_t)0x00000000)
#define RCC_DFSDM1AUDIOCLKSOURCE_I2SAPB2          ((uint32_t)RCC_DCKCFGR_CKDFSDM1ASEL)
#define IS_RCC_DFSDM1ACLK_SOURCE(SOURCE) (((SOURCE) == RCC_DFSDM1AUDIOCLKSOURCE_I2SAPB1) || ((SOURCE) == RCC_DFSDM1AUDIOCLKSOURCE_I2SAPB2))

/* Legacy Defines */
#define IS_RCC_DFSDMACLK_SOURCE      IS_RCC_DFSDM1ACLK_SOURCE
/**
  * @}
  */

#if defined(STM32F413_423xx)
/** @defgroup RCC_DFSDM_Audio_Clock_Source  RCC DFSDM Audio Clock Source
  * @{
  */
#define RCC_DFSDM2AUDIOCLKSOURCE_I2SAPB1          ((uint32_t)0x00000000)
#define RCC_DFSDM2AUDIOCLKSOURCE_I2SAPB2          ((uint32_t)RCC_DCKCFGR_CKDFSDM2ASEL)
#define IS_RCC_DFSDM2ACLK_SOURCE(SOURCE) (((SOURCE) == RCC_DFSDM2AUDIOCLKSOURCE_I2SAPB1) || ((SOURCE) == RCC_DFSDM2AUDIOCLKSOURCE_I2SAPB2))
/**
  * @}
  */
#endif /* STM32F413_423xx */
#endif /* STM32F412xG || STM32F413_423xx */
#define RCC_FLAG_HSIRDY                  ((uint8_t)0x21)
#define RCC_FLAG_HSERDY                  ((uint8_t)0x31)
#define RCC_FLAG_PLLRDY                  ((uint8_t)0x39)
#define RCC_FLAG_PLLI2SRDY               ((uint8_t)0x3B)
#define RCC_FLAG_PLLSAIRDY               ((uint8_t)0x3D)
#define RCC_FLAG_LSERDY                  ((uint8_t)0x41)
#define RCC_FLAG_LSIRDY                  ((uint8_t)0x61)
#define RCC_FLAG_BORRST                  ((uint8_t)0x79)
#define RCC_FLAG_PINRST                  ((uint8_t)0x7A)
#define RCC_FLAG_PORRST                  ((uint8_t)0x7B)
#define RCC_FLAG_SFTRST                  ((uint8_t)0x7C)
#define RCC_FLAG_IWDGRST                 ((uint8_t)0x7D)
#define RCC_FLAG_WWDGRST                 ((uint8_t)0x7E)
#define RCC_FLAG_LPWRRST                 ((uint8_t)0x7F)

#define IS_RCC_FLAG(FLAG) (((FLAG) == RCC_FLAG_HSIRDY)   || ((FLAG) == RCC_FLAG_HSERDY) || \
                           ((FLAG) == RCC_FLAG_PLLRDY)   || ((FLAG) == RCC_FLAG_LSERDY) || \
                           ((FLAG) == RCC_FLAG_LSIRDY)   || ((FLAG) == RCC_FLAG_BORRST) || \
                           ((FLAG) == RCC_FLAG_PINRST)   || ((FLAG) == RCC_FLAG_PORRST) || \
                           ((FLAG) == RCC_FLAG_SFTRST)   || ((FLAG) == RCC_FLAG_IWDGRST)|| \
                           ((FLAG) == RCC_FLAG_WWDGRST)  || ((FLAG) == RCC_FLAG_LPWRRST)|| \
                           ((FLAG) == RCC_FLAG_PLLI2SRDY)|| ((FLAG) == RCC_FLAG_PLLSAIRDY))

#define IS_RCC_CALIBRATION_VALUE(VALUE) ((VALUE) <= 0x1F)
/**
  * @}
  */ 

/**
  * @}
  */ 

/* Exported macro ------------------------------------------------------------*/
/* Exported functions --------------------------------------------------------*/ 

/* Function used to set the RCC clock configuration to the default reset state */
void        RCC_DeInit(void);

/* Internal/external clocks, PLL, CSS and MCO configuration functions *********/
void        RCC_HSEConfig(uint8_t RCC_HSE);
ErrorStatus RCC_WaitForHSEStartUp(void);
void        RCC_AdjustHSICalibrationValue(uint8_t HSICalibrationValue);
void        RCC_HSICmd(FunctionalState NewState);
void        RCC_LSEConfig(uint8_t RCC_LSE);
void        RCC_LSICmd(FunctionalState NewState);

void        RCC_PLLCmd(FunctionalState NewState);

#if defined(STM32F410xx) || defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx) || defined(STM32F469_479xx)
void        RCC_PLLConfig(uint32_t RCC_PLLSource, uint32_t PLLM, uint32_t PLLN, uint32_t PLLP, uint32_t PLLQ, uint32_t PLLR);
#endif /* STM32F410xx || STM32F412xG || STM32F413_423xx || STM32F446xx || STM32F469_479xx */

#if defined(STM32F40_41xxx) || defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F401xx) || defined(STM32F411xE)
void        RCC_PLLConfig(uint32_t RCC_PLLSource, uint32_t PLLM, uint32_t PLLN, uint32_t PLLP, uint32_t PLLQ);
#endif /* STM32F40_41xxx || STM32F427_437xx || STM32F429_439xx || STM32F401xx || STM32F411xE */

void        RCC_PLLI2SCmd(FunctionalState NewState);

#if defined(STM32F40_41xxx) || defined(STM32F401xx)
void        RCC_PLLI2SConfig(uint32_t PLLI2SN, uint32_t PLLI2SR);
#endif /* STM32F40_41xxx || STM32F401xx */
#if defined(STM32F411xE)
void        RCC_PLLI2SConfig(uint32_t PLLI2SN, uint32_t PLLI2SR, uint32_t PLLI2SM);
#endif /* STM32F411xE */
#if defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F469_479xx)
void        RCC_PLLI2SConfig(uint32_t PLLI2SN, uint32_t PLLI2SQ, uint32_t PLLI2SR);
#endif /* STM32F427_437xx || STM32F429_439xx || STM32F469_479xx */
#if defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx)
void        RCC_PLLI2SConfig(uint32_t PLLI2SM, uint32_t PLLI2SN, uint32_t PLLI2SP, uint32_t PLLI2SQ, uint32_t PLLI2SR);
#endif /* STM32F412xG || STM32F413_423xx || STM32F446xx */

void        RCC_PLLSAICmd(FunctionalState NewState);
#if defined(STM32F469_479xx)
void        RCC_PLLSAIConfig(uint32_t PLLSAIN, uint32_t PLLSAIP, uint32_t PLLSAIQ, uint32_t PLLSAIR);
#endif /* STM32F469_479xx */
#if defined(STM32F446xx)
void        RCC_PLLSAIConfig(uint32_t PLLSAIM, uint32_t PLLSAIN, uint32_t PLLSAIP, uint32_t PLLSAIQ);
#endif /* STM32F446xx */
#if defined(STM32F40_41xxx) || defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F401xx) || defined(STM32F411xE)
void        RCC_PLLSAIConfig(uint32_t PLLSAIN, uint32_t PLLSAIQ, uint32_t PLLSAIR);
#endif /* STM32F40_41xxx || STM32F427_437xx || STM32F429_439xx || STM32F401xx || STM32F411xE */

void        RCC_ClockSecuritySystemCmd(FunctionalState NewState);
void        RCC_MCO1Config(uint32_t RCC_MCO1Source, uint32_t RCC_MCO1Div);
void        RCC_MCO2Config(uint32_t RCC_MCO2Source, uint32_t RCC_MCO2Div);

/* System, AHB and APB busses clocks configuration functions ******************/
void        RCC_SYSCLKConfig(uint32_t RCC_SYSCLKSource);
uint8_t     RCC_GetSYSCLKSource(void);
void        RCC_HCLKConfig(uint32_t RCC_SYSCLK);
void        RCC_PCLK1Config(uint32_t RCC_HCLK);
void        RCC_PCLK2Config(uint32_t RCC_HCLK);
void        RCC_GetClocksFreq(RCC_ClocksTypeDef* RCC_Clocks);

/* Peripheral clocks configuration functions **********************************/
void        RCC_RTCCLKConfig(uint32_t RCC_RTCCLKSource);
void        RCC_RTCCLKCmd(FunctionalState NewState);
void        RCC_BackupResetCmd(FunctionalState NewState);

#if defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx)  
void        RCC_I2SCLKConfig(uint32_t RCC_I2SAPBx, uint32_t RCC_I2SCLKSource);
#if defined(STM32F446xx)
void        RCC_SAICLKConfig(uint32_t RCC_SAIInstance, uint32_t RCC_SAICLKSource);
#endif /* STM32F446xx */
#if defined(STM32F413_423xx)
void RCC_SAIBlockACLKConfig(uint32_t RCC_SAIBlockACLKSource);
void RCC_SAIBlockBCLKConfig(uint32_t RCC_SAIBlockBCLKSource);
#endif /* STM32F413_423xx */
#endif /* STM32F412xG || STM32F413_423xx || STM32F446xx */

#if defined(STM32F40_41xxx) || defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F401xx) || defined(STM32F410xx) || defined(STM32F411xE) || defined(STM32F469_479xx)
void        RCC_I2SCLKConfig(uint32_t RCC_I2SCLKSource);
#endif /* STM32F40_41xxx || STM32F427_437xx || STM32F429_439xx || STM32F401xx || STM32F410xx || STM32F411xE || STM32F469_479xx */

#if defined(STM32F40_41xxx) || defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F469_479xx)
void        RCC_SAIBlockACLKConfig(uint32_t RCC_SAIBlockACLKSource);
void        RCC_SAIBlockBCLKConfig(uint32_t RCC_SAIBlockBCLKSource);
#endif /* STM32F40_41xxx || STM32F427_437xx || STM32F429_439xx || STM32F469_479xx */

void        RCC_SAIPLLI2SClkDivConfig(uint32_t RCC_PLLI2SDivQ);
void        RCC_SAIPLLSAIClkDivConfig(uint32_t RCC_PLLSAIDivQ);

#if defined(STM32F413_423xx)
void        RCC_SAIPLLI2SRClkDivConfig(uint32_t RCC_PLLI2SDivR);
void        RCC_SAIPLLRClkDivConfig(uint32_t RCC_PLLDivR);
#endif /* STM32F413_423xx */

void        RCC_LTDCCLKDivConfig(uint32_t RCC_PLLSAIDivR);
void        RCC_TIMCLKPresConfig(uint32_t RCC_TIMCLKPrescaler);

void        RCC_AHB1PeriphClockCmd(uint32_t RCC_AHB1Periph, FunctionalState NewState);
void        RCC_AHB2PeriphClockCmd(uint32_t RCC_AHB2Periph, FunctionalState NewState);
void        RCC_AHB3PeriphClockCmd(uint32_t RCC_AHB3Periph, FunctionalState NewState);
void        RCC_APB1PeriphClockCmd(uint32_t RCC_APB1Periph, FunctionalState NewState);
void        RCC_APB2PeriphClockCmd(uint32_t RCC_APB2Periph, FunctionalState NewState);

void        RCC_AHB1PeriphResetCmd(uint32_t RCC_AHB1Periph, FunctionalState NewState);
void        RCC_AHB2PeriphResetCmd(uint32_t RCC_AHB2Periph, FunctionalState NewState);
void        RCC_AHB3PeriphResetCmd(uint32_t RCC_AHB3Periph, FunctionalState NewState);
void        RCC_APB1PeriphResetCmd(uint32_t RCC_APB1Periph, FunctionalState NewState);
void        RCC_APB2PeriphResetCmd(uint32_t RCC_APB2Periph, FunctionalState NewState);

void        RCC_AHB1PeriphClockLPModeCmd(uint32_t RCC_AHB1Periph, FunctionalState NewState);
void        RCC_AHB2PeriphClockLPModeCmd(uint32_t RCC_AHB2Periph, FunctionalState NewState);
void        RCC_AHB3PeriphClockLPModeCmd(uint32_t RCC_AHB3Periph, FunctionalState NewState);
void        RCC_APB1PeriphClockLPModeCmd(uint32_t RCC_APB1Periph, FunctionalState NewState);
void        RCC_APB2PeriphClockLPModeCmd(uint32_t RCC_APB2Periph, FunctionalState NewState);

/* Features available only for STM32F410xx/STM32F411xx/STM32F446xx/STM32F469_479xx devices */
void        RCC_LSEModeConfig(uint8_t RCC_Mode);

/* Features available only for STM32F469_479xx devices */
#if defined(STM32F469_479xx)
void        RCC_DSIClockSourceConfig(uint8_t RCC_ClockSource);
#endif /*  STM32F469_479xx */

/* Features available only for STM32F412xG/STM32F413_423xx/STM32F446xx/STM32F469_479xx devices */
#if defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx) || defined(STM32F469_479xx)
void        RCC_48MHzClockSourceConfig(uint8_t RCC_ClockSource);
void        RCC_SDIOClockSourceConfig(uint8_t RCC_ClockSource);
#endif /* STM32F412xG || STM32F413_423xx || STM32F446xx || STM32F469_479xx */

/* Features available only for STM32F446xx devices */
#if defined(STM32F446xx)
void        RCC_AHB1ClockGatingCmd(uint32_t RCC_AHB1ClockGating, FunctionalState NewState);
void        RCC_SPDIFRXClockSourceConfig(uint8_t RCC_ClockSource);
void        RCC_CECClockSourceConfig(uint8_t RCC_ClockSource);
#endif /* STM32F446xx */

/* Features available only for STM32F410xx/STM32F412xG/STM32F446xx devices */
#if defined(STM32F410xx) || defined(STM32F412xG) || defined(STM32F413_423xx) || defined(STM32F446xx)
void        RCC_FMPI2C1ClockSourceConfig(uint32_t RCC_ClockSource);
#endif /* STM32F410xx || STM32F412xG || STM32F413_423xx || STM32F446xx */

/* Features available only for STM32F410xx devices */
#if defined(STM32F410xx) || defined(STM32F413_423xx)
void        RCC_LPTIM1ClockSourceConfig(uint32_t RCC_ClockSource);
#if defined(STM32F410xx)
void        RCC_MCO1Cmd(FunctionalState NewState);
void        RCC_MCO2Cmd(FunctionalState NewState);
#endif /* STM32F410xx */
#endif /* STM32F410xx || STM32F413_423xx */

#if defined(STM32F412xG) || defined(STM32F413_423xx)
void RCC_DFSDMCLKConfig(uint32_t RCC_DFSDMCLKSource);
void RCC_DFSDM1ACLKConfig(uint32_t RCC_DFSDM1ACLKSource);
#if defined(STM32F413_423xx)
void RCC_DFSDM2ACLKConfig(uint32_t RCC_DFSDMACLKSource);
#endif /* STM32F413_423xx */
/* Legacy Defines */
#define RCC_DFSDM1CLKConfig      RCC_DFSDMCLKConfig
#endif /* STM32F412xG || STM32F413_423xx */
/* Interrupts and flags management functions **********************************/
void        RCC_ITConfig(uint8_t RCC_IT, FunctionalState NewState);
FlagStatus  RCC_GetFlagStatus(uint8_t RCC_FLAG);
void        RCC_ClearFlag(void);
ITStatus    RCC_GetITStatus(uint8_t RCC_IT);
void        RCC_ClearITPendingBit(uint8_t RCC_IT);

#ifdef __cplusplus
}
#endif

#endif /* __STM32F4xx_RCC_H */
```


