# 纯理论

1. 通过绝对地址访问内存单元

```Plaintext
/* GPIOA 端口的16个引脚全部输出高电平 */

*(unsigned int*)(0x40020014) = 0xFFFF;
```

说明：

(unsigned int*)的作用是将0x40020014这个立即数强制类型转化为无符号整形地址，告诉编译器这是一个地址。

*(unsigned int*)是相当于*地址，也就是对这个地址对应的内存空间的值，*(unsigned int*)(0x40020014) = 0xFFFF;就是相当于对0x40020014这个内存空间赋值为0xFFFF。



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

### APB1

APB1总线时钟，由HCLK经过低速APB1预分频器得到。由于APB1是低速总线时钟，APB1总线最高频率为42MHz，片上低速的外设就挂载在该总线上，例如有看门狗定时器、定时器2/3/4/5/6/7、RTC时钟、USART2/3/4/5、SPI2(I2S2)与SPI3(I2S3)、I2C1~3、CAN和2个DAC（针对STM32F4系列而言）。

### APB2

APB2总线时钟，由HCLK经过高速APB2预分频器得到。与APB2高速总线连接的外设有定时器1/8/9/10/11、SPI1、USART1和USART6、3个ADC和SDIO接口（针对STM32F4系列而言）。

此外，AHB总线时钟直接作为GPIO(A\B\C\D\E\F\G\H\I\)、以太网、DCMI、FSMC、AHB总线、Cortex内核、存储器和DMA的HCLK时钟，并作为Cortex内核自由运行时钟FCLK。



### AHB



## 时钟树

AHB先进高性能总线，其中HCLK作为时钟线，连接

外设（针对STM32F1系列而言）

> APB1	除定时器外接有分频器，定时器接有倍频器
>
> > USB	I2C	SPI2~3	CAN	UART2~5	通用定时器	基本定时器	
>
> APB2	除定时器外接有分频器，定时器接有倍频器，ADC额外再次外接一个自己的分频器
>
> > SPI1	GPIO	ADC	高级定时器	中断

内存

> 连接分频器，系统滴答

DMA	 处理器



|                          |              |        |            | \|                | ——           | 内核/内存/DMA72MHz    |
| ------------------------ | ------------ | ------ | ---------- | ----------------- | ------------ | --------------------- |
|                          |              |        |            | 分频器/1/8        | ——           | 系统滴答72MHz         |
| HSI	高速内部时钟8MHz  |              |        |            | 分频器/1/2/4/8/16 | ——           | APB1外设除定时器36MHz |
| HSE	高速外部时钟 8MHz | SYSCLK 72MHz | 分频器 | HCLK 72MHz | +                 | 倍频器\*1\*2 | APB1外设定时器72MHz   |
| HSI或HSE之一接PLL锁相环  |              |        |            | 分频器/1/2/4/8/16 | ——           | APB2外设除定时器72MHz |
|                          |              |        |            | \|                | 倍频器\*1\*2 | APB2外设定时器72MHz   |
|                          |              |        |            | \|                | 分频器/6     | APB2 ADC  72MHz       |


