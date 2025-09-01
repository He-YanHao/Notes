# GPIO口

## 简介

决定GPIO口模式的有两个MOS管和一个上拉电阻。

### **STM32的八种工作模式**

| 代码                  | 模式名称     | 性质     | 特征                                                        |
| --------------------- | ------------ | -------- | ----------------------------------------------------------- |
| GPIO_Mode_IN_FLOATING | 浮空输入     | 数字输入 | 可读取电平，若引脚悬空，则电平不稳定。                      |
| GPIO_Mode_IPU         | 上拉输入     | 数字输入 | 可读取引脚电平，内部接上拉电阻，悬空默认高电平。            |
| GPIO_Mode_IPD         | 下拉输入     | 数字输入 | 可读取引脚电平，内部接下拉电阻，悬空默认低电平。            |
| GPIO_Mode_AIN         | 模拟输入     | 模拟输入 | GPIO无效，接入内部ADC。                                     |
| GPIO_Mode_Out_OD      | 开漏输出     | 数字输出 | 可输出引脚电平，高电平为高阻态，低电平接VCC。驱动能力较小。 |
| GPIO_Mode_Out_PP      | 推挽输出     | 数字输出 | 可输出引脚电平，高电平为VDD，低电平接VCC。驱动能力较大。    |
| GPIO_Mode_AF_OD       | 复用开漏输出 | 数字输出 | 有片上外设控制，高电平为高阻态，低电平接VCC。               |
| GPIO_Mode_AF_PP       | 复用推挽输出 | 数字输出 | 有片上外设控制，高电平为VDD，低电平接VCC。                  |

- 输入浮空
- 输入上拉
- 输入下拉
- 模拟功能
- 具有上拉或下拉功能的开漏输出
- 具有上拉或下拉功能的推挽输出
- 具有上拉或下拉功能的复用功能推挽 
- 具有上拉或下拉功能的复用功能开漏



## 浮空输入 上拉输入 下拉输入

根据肖特基触发器前街VDD或VSS来区分。

接VDD拉高电压，为上拉输入。

接VSS拉低电压，为下拉输入。

都不接为浮空输入。

## 模拟输入

直接接入内部ADC。

## 开漏输出 推挽输出

开漏输出，可输出引脚电平，高电平为高阻态，低电平接VCC。驱动能力较小。  

推挽输出，可输出引脚电平，高电平为VDD，低电平接VCC。驱动能力较大。

## 复用开漏输出 复用推挽输出



## F4GPIO

```c
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_OUT;
GPIO_InitStructure.GPIO_OType = GPIO_OType_PP;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_100MHz;
GPIO_InitStructure.GPIO_PuPd = GPIO_PuPd_NOPULL;
```
- GPIO_Mode
  - GPIO_Mode_IN			 //输入
  - GPIO_Mode_OUT		 //输出
  - GPIO_Mode_AF			//复用模式
  - GPIO_Mode_AN	       //模拟输入

- GPIO_OType
  - GPIO_OType_PP		   //推挽输出
  - GPIO_OType_OD		  //开漏输出

- GPIO_PuPd
  - GPIO_PuPd_NOPULL	//无上下拉
  - GPIO_PuPd_UP			  //上拉
  - GPIO_PuPd_DOWN	   //下拉

- GPIO_Speed
  - GPIO_Low_Speed          //慢速
  - GPIO_Medium_Speed   //中速
  - GPIO_Fast_Speed          //快速
  - GPIO_High_Speed         //最快速



每个通用 I/O 端口包括 

4 个 32 位配置寄存器（GPIOx_MODER、GPIOx_OTYPER、 GPIOx_OSPEEDR 和 GPIOx_PUPDR）、

2 个 32 位数据寄存器（GPIOx_IDR 和 GPIOx_ODR）

1 个 32 位置位/复位寄存器 (GPIOx_BSRR)

1 个 32 位锁定寄存器 (GPIOx_LCKR) 

2 个 32 位复用功能选择寄存器（GPIOx_AFRH 和 GPIOx_AFRL）。

可以将GPIO口配置成：

- 输入浮空
- 输入上拉
- 输入下拉
- 模拟功能
- 具有上拉或下拉功能的开漏输出
- 具有上拉或下拉功能的推挽输出
- 具有上拉或下拉功能的复用功能推挽
- 具有上拉或下拉功能的复用功能开漏



1. 配置GPIO模式

- 第一步就是通过控制寄存器（**GPIOx_MODER**）配置为输入功能，输出功能，复用功能还是模拟功能。
- 第二步就是通过 GPIO 上/下拉寄存器（**GPIOx_PUPDR**）配置GPIO的上下拉模式或者浮空。

2. 配置为浮空模式，无上拉下拉

* 一般输出模式我们都配置为浮空模式，输入模式我们才需要考虑上拉还是下拉，根据默认电平状态进行判断。

- 第一步配置好为输出模式之后，我们还需要进行第二步配置，配置PB2为浮空模式，无上拉和下拉。

3. 配置GPIO的输出

- 配置GPIO的输出也分为两步，第一步配置端口输出模式寄存器**GPIOx_MODER**，第二步配置端口速度寄存器**GPIOx_OSPEEDR** 。

配置为推挽输出

- 当我们配置为输出模式的时候，可以有两种输出模式选择，一种是推挽输出模式，一种是开漏输出模式。

> - **00: 低速（Low speed）**：适用于功耗敏感的应用或者那些不需要高速切换的场合。
> - **01: 中速（Medium speed）**：提供了一种平衡的速度和功耗的选项，适合大多数应用。
> - **10: 高速（High speed）**：用于需要较快数据传输或较高频率切换的应用。
> - **11: 极高速（Very high speed）**：提供最快的GPIO切换速度，适用于高速通信或快速信号处理的场合。

   开漏是需要外接上拉电阻才可以输出高电平，这里并不适合，所以需要设置为推挽输出。

   端口输出模式寄存器如图1-3-1所示。

   从图1-3-1可以看到端口输出模式寄存器的地址偏移为0x04，那**GPIOB_MODER**寄存器的地址就是0x4002 0400 + 0x04。我们接着往下看这个寄存器的介绍，如图1-3-2所示。

   从图1-3-2可以看到每一个引脚由1位进行控制，要配置为推挽输出，只需要往对应的寄存器写0即可。转化为代码为GPIOB_MODER &= ~(0x01 << 2)。

配置速度

   我们配置引脚输出模式之后，我们还要选择这个引脚对应的速度，端口输出速度寄存器如图1-3-3所示。

1. ### 端口输出控制寄存器

   关于端口输出控制寄存器如图2-1-1所示。

![img](https://lceda001.feishu.cn/space/api/box/stream/download/asynccode/?code=YThmMTFlOTcwZmJlY2RhZmI0YTc1NTMwMDI4M2I2YWFfbjZ5S01IcmxKZWxLTDZ6aWxqVU80cW1hTGhZWmxpdUpfVG9rZW46RG9YamJMcFREbzEwSWp4aTZ6WmNtM2wybmxpXzE3MjQ2OTE1Njk6MTcyNDY5NTE2OV9WNA)

图2-1-1

   从图2-1-1可以看到端口输出控制寄存器的地址偏移为0x14，那么对应的**GPIOx_ODR** 寄存器的地址为0x4002 0400 + 0x14。我们接着往下看这个寄存器的介绍，如图2-1-2所示。

![img](https://lceda001.feishu.cn/space/api/box/stream/download/asynccode/?code=ZGY0ZmE3Nzk4MWI3MDIxMDgxY2RiMzgyZTc4ZThkOTlfa1lzNFJNRm5lUTFacWdCVUJZbzNJNjlTZVhNRllLVTJfVG9rZW46VUVDS2IzOGhEb2tOVE94RnpydmNPMVc0bnVoXzE3MjQ2OTE1Njk6MTcyNDY5NTE2OV9WNA)

图2-1-2

   从图2-1-2可以了解到**GPIOB_ODR**寄存器的低16位有效，每一个引脚对应一位，往对应的位写0就是输出低电平，写1就输出高电平。

> 在STM32的头文件中已经将大部分的地址定义了，所以我们只需要调用这些地址的定义就可以了。

```Plaintext
输出低电平转换为代码为GPIOB->ODR &= ~ (0x01 << 2);
输出高电平转换为代码为GPIOB->ODR |= ( 0x01 << 2);
```

1. ### 端口位操作寄存器

   关于端口位操作寄存器如图2-2-1所示。

![img](https://lceda001.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDg4ZWYyNDU4NmRlYTE5NGZkYzk1NzY4NzVlZGZmMDlfaVRMaHluS3lEbmlYdU9FU2FWNmtxV1VQbHo5dGdDSkxfVG9rZW46V0VSNGIyMzlwb29QMHl4MnB0MGNJYU9kbkFFXzE3MjQ2OTE1Njk6MTcyNDY5NTE2OV9WNA)

图2-2-1

   从图2-2-1可以看到端口输出控制寄存器的地址偏移为0x18，那么对应的**GPIOB_BSRR**寄存器的地址为0x4002 0400 + 0x18。我们接着往下看这个寄存器的介绍，如图2-2-2所示。



图2-2-2

   从图2-2-2可以了解到**GPIOB_BSRR**寄存器的低16位是置1位，高16位是清0位。对于低16位每一个引脚对应一位，往对应的位写1就是输出高电平，写0电平状态不改变。对于高16位每一个引脚对应一位，往对应的位写1就是输出低电平，写0电平状态不改变。

> 在STM32的头文件中已经将大部分的地址定义了，所以我们只需要调用这些地址的定义就可以了。

```Plaintext
输出低电平转换为代码为GPIOB->BSRR |= (0x01 << (2 + 16));
输出高电平转换为代码为GPIOB->BSRR |= (0x01 << 2);
```

1. ### 端口位翻转寄存器

   使用端口位翻转寄存器也有类似的功能，从名称就可以知道这个寄存器是把对应的位电平进行翻转，这是一个很不错的操作，使用起来也很方便。但前提是我们要知道当下的引脚电平是一个什么状态，然后我们翻转之后又是一个什么状态。关于这一部分就不过多说明，大家可以自行研究一下。

1. ## 实验现象

   如果用上面的代码需要注意一下，像RCC这种宏定义其实在STM32的头文件中已经定义了，可以直接使用原来定义的，这样我们就不用再编写，也可以把我们编写的名称稍微修改一下，比如在它们前面加上一个LED或者BSP等等都可。

   关于这一章节的代码，在立创·梁山派·天空星STM32F407VET6开发板资料/第03章软件资料/代码例程/里面的001寄存器点灯。

   烧写我们的代码之后，可以看到开发板的LED这个灯将被点亮。

# 六、库函数点亮LED灯

1. ## 配置流程