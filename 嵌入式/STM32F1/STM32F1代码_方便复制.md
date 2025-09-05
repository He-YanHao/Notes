# STM32

## BKP备份寄存器

```c
/*开启时钟*/
RCC_APB1PeriphClockCmd(RCC_APB1Periph_PWR, ENABLE);		//开启PWR的时钟
RCC_APB1PeriphClockCmd(RCC_APB1Periph_BKP, ENABLE);		//开启BKP的时钟

PWR_BackupAccessCmd(ENABLE);							//使用PWR开启对备份寄存器的访问

BKP_WriteBackupRegister(BKP_DR1, ArrayWrite[0]);	//写入测试数据到备份寄存器
BKP_WriteBackupRegister(BKP_DR2, ArrayWrite[1]);

ArrayRead[0] = BKP_ReadBackupRegister(BKP_DR1);		//读取备份寄存器的数据
ArrayRead[1] = BKP_ReadBackupRegister(BKP_DR2);
```

## GPIO口

```
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);	//开启GPIO口的时钟

GPIO_InitTypeDef GPIO_InitStructure;					//定义结构体变量
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_;			//配置工作模式
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_All;				//配置GPIO口的位置
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;		//配置速度。
GPIO_Init(GPIOA, &GPIO_InitStructure);					//初始化GPIOA，并将其放在GPIO_InitStructure里
```

其中：

- GPIO_SetBits(GPIOX, GPIO_Pin_X);可以将指定端口配置为高电平

- GPIO_ResetBits(GPIOX, GPIO_Pin_X);可以将指定端口配置为低电平

- GPIO_Write(GPIOX,0x00XX);将整个GPIO口进行配置高低电平，通过第二个参数的十六进制转二进制配置。

- GPIO_ReadInputDataBit(GPIOX, GPIO_Pin_X);//读取引脚电平

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

## 中断

### NVIC

```
NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);				//配置NVIC分组
															//NVIC初始化
NVIC_InitTypeDef NVIC_InitStructure;						//定义结构体变量
NVIC_InitStructure.NVIC_IRQChannel = EXTI0_IRQn;		 	//选择中断函数名字
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;				//指定NVIC线路使能
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;	//指定NVIC线路的抢占优先级为1
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;			//指定NVIC线路的响应优先级为1
NVIC_Init(&NVIC_InitStructure);								//将结构体变量交给NVIC_Init，配置NVIC外设
```

* 中断函数名字：GPIO0-4中断函数名字为EXTI(X)_IRQHandler，GPIO5-9特通过一个入口EXTI9_5_IRQHandler 然后进入中断后在通过比较来判断是那路触发了中断 ;同理GPIO10-15通过EXTI15_10_IRQHandler进入中断，依旧采用在中断中判断那路触发了中断。
* NVIC线路使能
* 指定抢占优先级和响应优先级。

### EXTI

```
RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO, ENABLE);		//开启AFIO的时钟，外部中断必须开启AFIO的时钟。
GPIO_EXTILineConfig(GPIO_PortSourceGPIOX, GPIO_PinSourceX);	//将外部中断的X号线映射到GPIO口，即选择X为外部中断引脚。
															//EXTI初始化
EXTI_InitTypeDef EXTI_InitStructure;						//定义结构体变量

EXTI_InitStructure.EXTI_Line = EXTI_Line(X);				//选择配置外部中断的X号线
EXTI_InitStructure.EXTI_LineCmd = ENABLE;					//指定外部中断线使能
EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt;			//指定外部中断线为中断模式
EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Falling;		//指定外部中断线为下降沿触发
EXTI_Init(&EXTI_InitStructure);								//将结构体变量交给EXTI_Init
```

* 中断线的标号取值范围为EXTI_Line0~EXTI_Line15。
* 中断线使能
* 中断模式，可选值为中断 EXTI_Mode_Interrupt 和事件 EXTI_Mode_Event。
* 触发方式，可以是
  * 下降沿触发 EXTI_Trigger_Falling
  * 上升沿触发EXTI_Trigger_Rising
  * 任意电平（上升沿和下降沿）触发EXTI_Trigger_Rising_Falling

```
void EXTI(x)_IRQHandler(void)
{
if (EXTI_GetITStatus(EXTI_Line(X)) == SET)		//判断是否是外部中断(X)号线触发的中断
{
	if (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_14) == 0)
	{
		CountSensor_Count ++;					//计数值自增一次
	}
	
	EXTI_ClearITPendingBit(EXTI_Line14);		//清除外部中断14号线的中断标志位
												//中断标志位必须清除
												//否则中断将连续不断地触发，导致主程序卡死
}
}
```



## 定时器

```
RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM(X), ENABLE);//开启TIM(X)的时钟
TIM_InternalClockConfig(TIM(X));//选择TIM(X)为内部时钟，若不调用此函数，TIM默认也为内部时钟。

TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;//定义结构体变量
TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;//时钟分频，选择不分频，此参数用于配置滤波器时钟，不影响时基单元功能
TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;//计数器模式，选择向上计数
TIM_TimeBaseInitStructure.TIM_Period = 10000 - 1;//计数周期，即ARR的值
TIM_TimeBaseInitStructure.TIM_Prescaler = 7200 - 1;//预分频器，即PSC的值
TIM_TimeBaseInitStructure.TIM_RepetitionCounter = 0;//重复计数器，高级定时器才会用到
TIM_TimeBaseInit(TIM(X), &TIM_TimeBaseInitStructure);//将结构体变量交给TIM_TimeBaseInit，配置TIM(X)的时基单元	

TIM_ClearFlag(TIM(X), TIM_FLAG_Update);//清除定时器更新标志位。TIM_TimeBaseInit函数末尾，手动产生了更新事件，若不清除此标志位，则开启中断后，会立刻进入一次中断，如果不介意此问题，则不清除此标志位也可。
TIM_ITConfig(TIM(X), TIM_IT_Update, ENABLE);//开启TIM(X)的更新中断
TIM_Cmd(TIM(X), ENABLE);//使能TIM(X)，定时器开始运行
```

* TIM_ClockDivision时钟分频：可以选择1，2，4分频。

* TIM_CounterMode计数器模式：

  | TIM_CounterMode_Up                     | TIM 向上计数模式                  |
  | -------------------------------------- | --------------------------------- |
  | TIM_CounterMode_Down                   | TIM 向下计数模式                  |
  | TIM_CounterMode_CenterAligned(1)(2)(3) | TIM 中央对齐模式(1)(2)(3)计数模式 |

* TIM_Period：设置了在下一个更新事件装入活动的自动重装载寄存器周期的值。它的取值必须在0x0000和 0xFFFF 之间。

* TIM_Prescaler：设置了用来作为TIMx时钟频率除数的预分频值。它的取值必须在0x0000和0xFFFF之间。

* TIM_RepetitionCounter：似乎可以重复计数，设置成x计数时间就会变成原来的(x+1)倍。

```
NVIC_InitTypeDef NVIC_InitStructure;//定义结构体变量
NVIC_InitStructure.NVIC_IRQChannel = TIM2_IRQn;//选择配置NVIC的TIM2线
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;//指定NVIC线路使能
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 2;//指定NVIC线路的抢占优先级为2
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;//指定NVIC线路的响应优先级为1
NVIC_Init(&NVIC_InitStructure);//将结构体变量交给NVIC_Init，配置NVIC外设
```

```
void TIM2_IRQHandler(void)
{
	if (TIM_GetITStatus(TIM2, TIM_IT_Update) == SET)
	{
		TIM_ClearITPendingBit(TIM2, TIM_IT_Update);
	}
}
```

## PWM

	//GPIO口使用复用推挽输出GPIO_Mode_AF_PP
	//配置定时器
	//输出比较初始化 
	TIM_OCInitTypeDef TIM_OCInitStructure;							//定义结构体变量
	TIM_OCStructInit(&TIM_OCInitStructure);                         //结构体初始化，若结构体没有完整赋值，则最好执行此函数，给结构体所有成员都赋一个默认值，避免结构体初值不确定的问题
	TIM_OCInitStructure.TIM_OCMode = TIM_OCMode_PWM1;               //输出比较模式，选择PWM模式1
	TIM_OCInitStructure.TIM_OCPolarity = TIM_OCPolarity_High;       //输出极性，选择为高，若选择极性为低，则输出高低电平取反
	TIM_OCInitStructure.TIM_OutputState = TIM_OutputState_Enable;   //输出使能
	TIM_OCInitStructure.TIM_Pulse = 0;								//初始的CCR值
	TIM_OC2Init(TIM2, &TIM_OCInitStructure);                        //将结构体变量交给TIM_OC2Init，配置TIM2的输出比较通道2
	/*TIM使能*/
	TIM_Cmd(TIM2, ENABLE);			//使能TIM2，定时器开始运行
* 比较模式：
  * PWM模式1－ 在向上计数时，一旦TIMx_CNT<TIMx_CCR1时通道1为有效电平，否则为
    无效电平；在向下计数时，一旦TIMx_CNT>TIMx_CCR1时通道1为无效电平(OC1REF=0)，否
    则为有效电平(OC1REF=1)。
  * PWM模式2－ 在向上计数时，一旦TIMx_CNT<TIMx_CCR1时通道1为无效电平，否则为有效电平；在向下计数时，一旦TIMx_CNT>TIMx_CCR1时通道1为有效电平，否则为无效电
    平。
* 输出极性，选择为高，若选择极性为低，则输出高低电平取反
* 输出使能
* 初始的CCR值

```
void PWM_SetCompare2(uint16_t Compare)
{
TIM_SetCompare2(TIM2, Compare);		//设置CCR2的值
}
```

## AD

```
void AD_Init(void)//AD初始化
{
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_ADC1, ENABLE);	//开启ADC1的时钟
/*设置ADC时钟*/
    RCC_ADCCLKConfig(RCC_PCLK2_Div6);						//选择时钟6分频，ADCCLK = 72MHz / 6 = 12MHz
    ADC_RegularChannelConfig(ADC1, ADC_Channel_0, 1, ADC_SampleTime_55Cycles5);		//规则组序列1的位置，配置为通道0
    														//ADC初始化
    ADC_InitTypeDef ADC_InitStructure;						//定义结构体变量
    
    ADC_InitStructure.ADC_Mode = ADC_Mode_Independent;		//模式，选择独立模式，即单独使用ADC1
    ADC_InitStructure.ADC_DataAlign = ADC_DataAlign_Right;	//数据对齐，选择右对齐
    ADC_InitStructure.ADC_ExternalTrigConv = ADC_ExternalTrigConv_None;	//外部触发，使用软件触发，不需要外部触发
    ADC_InitStructure.ADC_ContinuousConvMode = DISABLE;		//连续转换，失能，每转换一次规则组序列后停止
    ADC_InitStructure.ADC_ScanConvMode = DISABLE;			//扫描模式，失能，只转换规则组的序列1这一个位置
    ADC_InitStructure.ADC_NbrOfChannel = 1;					//通道数，为1，仅在扫描模式下，才需要指定大于1的数，在非扫描模式下，只能是1
    ADC_Init(ADC1, &ADC_InitStructure);						//将结构体变量交给ADC_Init，配置ADC1

    														//ADC使能
    ADC_Cmd(ADC1, ENABLE);									//使能ADC1，ADC开始运行

    														//ADC校准*/
    ADC_ResetCalibration(ADC1);								//固定流程，内部有电路会自动执行校准
    while (ADC_GetResetCalibrationStatus(ADC1) == SET);
    ADC_StartCalibration(ADC1);
    while (ADC_GetCalibrationStatus(ADC1) == SET);
}

uint16_t AD_GetValue(void)//获取AD转换的值
{
    ADC_SoftwareStartConvCmd(ADC1, ENABLE);					//软件触发AD转换一次
    while (ADC_GetFlagStatus(ADC1, ADC_FLAG_EOC) == RESET);	//等待EOC标志位，即等待AD转换结束
    return ADC_GetConversionValue(ADC1);					//读数据寄存器，得到AD转换的结果
}
```

## DAM

## DMA直接存储器读取

### 软件DMA

```
/**
  * 函    数：DMA初始化
  * 参    数：AddrA 原数组的首地址
  * 参    数：AddrB 目的数组的首地址
  * 参    数：Size 转运的数据大小（转运次数）
  */
uint16_t MyDMA_Size;					//定义全局变量，用于记住Init函数的Size，供Transfer函数使用
void MyDMA_Init(uint32_t AddrA, uint32_t AddrB, uint16_t Size)
{
	MyDMA_Size = Size;					//将Size写入到全局变量，记住参数Size
	/*开启时钟*/
	RCC_AHBPeriphClockCmd(RCC_AHBPeriph_DMA1, ENABLE);						//开启DMA的时钟

	/*DMA初始化*/
	DMA_InitTypeDef DMA_InitStructure;										//定义结构体变量
	DMA_InitStructure.DMA_PeripheralBaseAddr = AddrA;						//外设基地址，给定形参AddrA
	DMA_InitStructure.DMA_MemoryBaseAddr = AddrB;							//存储器基地址，给定形参AddrB
	DMA_InitStructure.DMA_DIR = DMA_DIR_PeripheralSRC;						//数据传输方向，选择由外设到存储器
	DMA_InitStructure.DMA_BufferSize = Size;								//转运的数据大小（转运次数）
	DMA_InitStructure.DMA_PeripheralInc = DMA_PeripheralInc_Enable;			//外设地址自增，选择使能
	DMA_InitStructure.DMA_MemoryInc = DMA_MemoryInc_Enable;					//存储器地址自增，选择使能
	DMA_InitStructure.DMA_PeripheralDataSize = DMA_PeripheralDataSize_Byte;	//外设数据宽度，选择字节
	DMA_InitStructure.DMA_MemoryDataSize = DMA_MemoryDataSize_Byte;			//存储器数据宽度，选择字节
	DMA_InitStructure.DMA_Mode = DMA_Mode_Normal;							//模式，选择正常模式
	DMA_InitStructure.DMA_Priority = DMA_Priority_Medium;					//优先级，选择中等
	DMA_InitStructure.DMA_M2M = DMA_M2M_Enable;								//存储器到存储器，选择使能
	DMA_Init(DMA1_Channel1, &DMA_InitStructure);							//将结构体变量交给DMA_Init，配置DMA1的通道1
	/*DMA使能*/
	DMA_Cmd(DMA1_Channel1, DISABLE);	//这里先不给使能，初始化后不会立刻工作，等后续调用Transfer后，再开始
}
```

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

```
void MyDMA_Transfer(void)//启动DMA数据转运
{
	DMA_Cmd(DMA1_Channel1, DISABLE);					//DMA失能，在写入传输计数器之前，需要DMA暂停工作
	DMA_SetCurrDataCounter(DMA1_Channel1, MyDMA_Size);	//写入传输计数器，指定将要转运的次数
	DMA_Cmd(DMA1_Channel1, ENABLE);						//DMA使能，开始工作
	while (DMA_GetFlagStatus(DMA1_FLAG_TC1) == RESET);	//等待DMA工作完成
	DMA_ClearFlag(DMA1_FLAG_TC1);						//清除工作完成标志位
}
```



## 串口

拥有**TX发送端口**和**RX接收端口**，二者交叉连接，若电平标准不一需要进行转化。

```
#include <stdio.h>
#include <stdarg.h>

char Serial_RxPacket[100];				//定义接收数据包数组，数据包格式"@MSG\r\n"
uint8_t Serial_RxFlag;					//定义接收数据包标志位
	
void Serial_Init(void)//串口初始化
{
	/*开启时钟*/
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);	//开启USART1的时钟
                            								//开启GPIOA的时钟
															//将PA9引脚初始化为复用推挽输出
															//将PA10引脚初始化为上拉输入
															//USART初始化
	USART_InitTypeDef USART_InitStructure;					//定义结构体变量
	
	USART_InitStructure.USART_BaudRate = 9600;				//波特率
	USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;	//硬件流控制，不需要
	USART_InitStructure.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;	//模式，发送模式和接收模式均选择
	USART_InitStructure.USART_Parity = USART_Parity_No;			//奇偶校验，不需要
	USART_InitStructure.USART_StopBits = USART_StopBits_1;		//停止位，选择1位
	USART_InitStructure.USART_WordLength = USART_WordLength_8b;	//字长，选择8位
	USART_Init(USART1, &USART_InitStructure);					//将结构体变量交给USART_Init，配置USART1
	
															//中断输出配置
	USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);			//开启串口接收数据的中断
	
    														//NVIC中断分组
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);			//配置NVIC为分组2
    
    														//NVIC配置
    NVIC_InitTypeDef NVIC_InitStructure;					//定义结构体变量
    NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;		//选择配置NVIC的USART1线
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;			//指定NVIC线路使能
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;		//指定NVIC线路的抢占优先级为1
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;		//指定NVIC线路的响应优先级为1
    NVIC_Init(&NVIC_InitStructure);							//将结构体变量交给NVIC_Init，配置NVIC外设
    														//USART使能*/
    USART_Cmd(USART1, ENABLE);								//使能USART1，串口开始运行
}

```

 ```
void Serial_SendByte(uint8_t Byte)							//串口发送一个字节
{
    USART_SendData(USART1, Byte);							//将字节数据写入数据寄存器，写入后USART自动生成时序波形
    while (USART_GetFlagStatus(USART1, USART_FLAG_TXE) == RESET);	//等待发送完成
    /*下次写入数据寄存器会自动清除发送完成标志位，故此循环后，无需清除标志位*/
}
 ```

```
void Serial_SendArray(uint8_t *Array, uint16_t Length)//串口发送一个数组
{
    uint16_t i;
    for (i = 0; i < Length; i ++)		//遍历数组
    {
    Serial_SendByte(Array[i]);		//依次调用Serial_SendByte发送每个字节数据
    }
}
```

```
void Serial_SendString(char *String)//串口发送一个字符串
{
     uint8_t i;
     for (i = 0; String[i] != '\0'; i ++)//遍历字符数组（字符串），遇到字符串结束标志位后停止
     {
      	Serial_SendByte(String[i]);		//依次调用Serial_SendByte发送每个字节数据
     }
}
```

```
void Serial_SendNumber(uint32_t Number, uint8_t Length)//串口发送数字
{
     uint8_t i;
     for (i = 0; i < Length; i ++)		//根据数字长度遍历数字的每一位
     {
       Serial_SendByte(Number / Serial_Pow(10, Length - i - 1) % 10 + '0');	//依次调用Serial_SendByte发送每位数字
     }
}
```

```
    /**
    
      * 函    数：使用printf需要重定向的底层函数
      * 参    数：保持原始格式即可，无需变动
      * 返 回 值：保持原始格式即可，无需变动
        */
        int fputc(int ch, FILE *f)
        {
        Serial_SendByte(ch);			//将printf的底层重定向到自己的发送字节函数
        return ch;
        }
    

```

```
    /**
    
      * 函    数：自己封装的prinf函数
      * 参    数：format 格式化字符串
      * 参    数：... 可变的参数列表
      * 返 回 值：无
        */
        void Serial_Printf(char *format, ...)
        {
        char String[100];				//定义字符数组
        va_list arg;					//定义可变参数列表数据类型的变量arg
        va_start(arg, format);			//从format开始，接收参数列表到arg变量
        vsprintf(String, format, arg);	//使用vsprintf打印格式化字符串和参数列表到字符数组中
        va_end(arg);					//结束变量arg
        Serial_SendString(String);		//串口发送字符数组（字符串）
        }
    
 
```

```
   /**
    
      * 函    数：USART1中断函数
    
      * 参    数：无
    
      * 返 回 值：无
    
      * 注意事项：此函数为中断函数，无需调用，中断触发后自动执行
    
      * 函数名为预留的指定名称，可以从启动文件复制
    
      * 请确保函数名正确，不能有任何差异，否则中断函数将不能进入
        */
        void USART1_IRQHandler(void)
        {
        static uint8_t RxState = 0;		//定义表示当前状态机状态的静态变量
        static uint8_t pRxPacket = 0;	//定义表示当前接收数据位置的静态变量
        if (USART_GetITStatus(USART1, USART_IT_RXNE) == SET)	//判断是否是USART1的接收事件触发的中断
        {
        	uint8_t RxData = USART_ReceiveData(USART1);			//读取数据寄存器，存放在接收的数据变量
        	
    
        	/*使用状态机的思路，依次处理数据包的不同部分*/
        	
        	/*当前状态为0，接收数据包包头*/
        	if (RxState == 0)
        	{
        		if (RxData == '@' && Serial_RxFlag == 0)		//如果数据确实是包头，并且上一个数据包已处理完毕
        		{
        			RxState = 1;			//置下一个状态
        			pRxPacket = 0;			//数据包的位置归零
        		}
        	}
        	/*当前状态为1，接收数据包数据，同时判断是否接收到了第一个包尾*/
        	else if (RxState == 1)
        	{
        		if (RxData == '\r')			//如果收到第一个包尾
        		{
        			RxState = 2;			//置下一个状态
        		}
        		else						//接收到了正常的数据
        		{
        			Serial_RxPacket[pRxPacket] = RxData;		//将数据存入数据包数组的指定位置
        			pRxPacket ++;			//数据包的位置自增
        		}
        	}
        	/*当前状态为2，接收数据包第二个包尾*/
        	else if (RxState == 2)
        	{
        		if (RxData == '\n')			//如果收到第二个包尾
        		{
        			RxState = 0;			//状态归0
        			Serial_RxPacket[pRxPacket] = '\0';			//将收到的字符数据包添加一个字符串结束标志
        			Serial_RxFlag = 1;		//接收数据包标志位置1，成功接收一个数据包
        		}
        	}
        	
        	USART_ClearITPendingBit(USART1, USART_IT_RXNE);		//清除标志位
    
        }
        }
```

## I2C

### 软件I2C

```c
void I2C_W_SCL(uint8_t BitValue)//I2C写SCL引脚电平
{
	GPIO_WriteBit(GPIOB, GPIO_Pin_10, (BitAction)BitValue);//根据BitValue，设置SCL引脚的电平
	Delay_us(10);//延时10us，防止时序频率超过要求
}
```

```c
void I2C_W_SDA(uint8_t BitValue)//I2C写SDA引脚电平
{
	GPIO_WriteBit(GPIOB, GPIO_Pin_11, (BitAction)BitValue);//根据BitValue，设置SDA引脚的电平，BitValue要实现非0即1的特性
	Delay_us(10);//延时10us，防止时序频率超过要求
}
```

```c
uint8_t I2C_R_SDA(void)//I2C读SDA引脚电平
{
	uint8_t BitValue;
	BitValue = GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_11);//读取SDA电平
	Delay_us(10);//延时10us，防止时序频率超过要求
	return BitValue;//返回SDA电平
}
```

```c
void MyI2C_Init(void)//GPIO口初始化
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);//开启GPIOB的时钟
	GPIO_InitTypeDef GPIO_InitStructure;//GPIO初始化
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10 | GPIO_Pin_11;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOB, &GPIO_InitStructure);//将PB10和PB11引脚初始化为开漏输出
	/*设置默认电平*/
	GPIO_SetBits(GPIOB, GPIO_Pin_10 | GPIO_Pin_11);//设置PB10和PB11引脚初始化后默认为高电平（释放总线状态）
}
```

```c
void MyI2C_Start(void)//I2C开始
{
	I2C_W_SDA(1);//释放SDA，确保SDA为高电平
	I2C_W_SCL(1);//释放SCL，确保SCL为高电平
	I2C_W_SDA(0);//在SCL高电平期间，拉低SDA，产生起始信号
	I2C_W_SCL(0);//起始后把SCL也拉低，即为了占用总线，也为了方便总线时序的拼接
}
```

```c
void MyI2C_Stop(void)//I2C终止
{
	I2C_W_SDA(0);//拉低SDA，确保SDA为低电平
	I2C_W_SCL(1);//释放SCL，使SCL呈现高电平
	I2C_W_SDA(1);//在SCL高电平期间，释放SDA，产生终止信号
}
```

```c
void MyI2C_SendByte(uint8_t Byte)//I2C发送一个字节，范围：0x00~0xFF。
{
	uint8_t i;
	for (i = 0; i < 8; i ++)//循环8次，主机依次发送数据的每一位
	{
		I2C_W_SDA(Byte & (0x80 >> i));//使用掩码的方式取出Byte的指定一位数据并写入到SDA线
		I2C_W_SCL(1);//释放SCL，从机在SCL高电平期间读取SDA
		I2C_W_SCL(0);//拉低SCL，主机开始发送下一位数据
	}
}
```

```c
uint8_t MyI2C_ReceiveByte(void)//I2C接收一个字节
{
	uint8_t i, Byte = 0x00;//定义接收的数据，并赋初值0x00，此处必须赋初值0x00，后面会用到
	I2C_W_SDA(1);//接收前，主机先确保释放SDA，避免干扰从机的数据发送
	for (i = 0; i < 8; i ++)//循环8次，主机依次接收数据的每一位
	{
		I2C_W_SCL(1);//释放SCL，主机机在SCL高电平期间读取SDA
		if (I2C_R_SDA() == 1){Byte |= (0x80 >> i);}//读取SDA数据，并存储到Byte变量。当SDA为1时，置变量指定位为1，当SDA为0时，不做处理，指定位为默认的初值0
		I2C_W_SCL(0);//拉低SCL，从机在SCL低电平期间写入SDA
	}
	return Byte;//返回接收到的一个字节数据
}
```

```c
void MyI2C_SendAck(uint8_t AckBit)//I2C发送应答位AckBit，0表示应答，1表示非应答。
{
	I2C_W_SDA(AckBit);//主机把应答位数据放到SDA线
	I2C_W_SCL(1);//释放SCL，从机在SCL高电平期间，读取应答位
	I2C_W_SCL(0);//拉低SCL，开始下一个时序模块
}
```

```c
uint8_t MyI2C_ReceiveAck(void)//I2C接收应答位，0表示应答，1表示非应答
{
	uint8_t AckBit;//定义应答位变量
	I2C_W_SDA(1);//接收前，主机先确保释放SDA，避免干扰从机的数据发送
	I2C_W_SCL(1);//释放SCL，主机机在SCL高电平期间读取SDA
	AckBit = I2C_R_SDA();//将应答位存储到变量里
	I2C_W_SCL(0);//拉低SCL，开始下一个时序模块
	return AckBit;//返回定义应答位变量
}
```

### 硬件I2C

```
/*I2C初始化*/
I2C_InitTypeDef I2C_InitStructure;//定义结构体变量
I2C_InitStructure.I2C_Mode = I2C_Mode_I2C;//模式，选择为I2C模式	包含I2C模式和SMBUS模式两种，其中SMBUS为I2C的一个子集
I2C_InitStructure.I2C_ClockSpeed = 50000;//时钟速度，选择为50KHz
I2C_InitStructure.I2C_DutyCycle = I2C_DutyCycle_2;//时钟占空比，选择Tlow/Thigh = 2	该配置有两个选择，分别为低电平时间比高电平时间为2：1 ( I2C_DutyCycle_2)和16：9 (I2C_DutyCycle_16_9)。一般不严格要求。
I2C_InitStructure.I2C_Ack = I2C_Ack_Enable;//应答，包含 使能I2C_Ack_Enable 和 禁止应答I2C_Ack_Disable，一般为了兼容性选择使能I2C_Ack_Enable。
I2C_InitStructure.I2C_AcknowledgedAddress = I2C_AcknowledgedAddress_7bit;//应答地址，包含7位或10位，此处选择7位，从机模式下才有效，
I2C_InitStructure.I2C_OwnAddress1 = 0x00;//自身地址，从机模式下才有效
I2C_Init(I2C2, &I2C_InitStructure);//将结构体变量交给I2C_Init，配置I2C2
/*I2C使能*/
I2C_Cmd(I2C2, ENABLE);//使能I2C2，开始运行
```

```

```

```

```



## SPI

### 软件SPI

```
/*引脚配置层*/

void MySPI_W_SS(uint8_t BitValue)//SPI写SS引脚电平
{
	GPIO_WriteBit(GPIOA, GPIO_Pin_4, (BitAction)BitValue);//根据BitValue，设置SS引脚的电平
}

void MySPI_W_SCK(uint8_t BitValue)//SPI写SCK引脚电平
{
	GPIO_WriteBit(GPIOA, GPIO_Pin_5, (BitAction)BitValue);//根据BitValue，设置SCK引脚的电平
}

void MySPI_W_MOSI(uint8_t BitValue)//SPI写MOSI引脚电平
{
	GPIO_WriteBit(GPIOA, GPIO_Pin_7, (BitAction)BitValue);//根据BitValue，设置MOSI引脚的电平，BitValue要实现非0即1的特性
}

uint8_t MySPI_R_MISO(void)//I2C读MISO引脚电平
{
	return GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_6);//读取MISO电平并返回
}
```

```
void MySPI_Init(void)//SPI初始化
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);	//开启GPIOA的时钟
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;//初始化为推挽输出
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_4 | GPIO_Pin_5 | GPIO_Pin_7;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);//将SPI通讯中除MISO引脚外初始化为推挽输出
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;//初始化为上拉输入
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);//将MISO引脚初始化为上拉输入
	/*设置默认电平*/
	MySPI_W_SS(1);											//SS默认高电平
	MySPI_W_SCK(0);											//SCK默认低电平
}
```

```
void MySPI_Start(void)//SPI起始
{
	MySPI_W_SS(0);//拉低SS，开始时序
}
```

```
void MySPI_Stop(void)//SPI终止
{
	MySPI_W_SS(1);//拉高SS，终止时序
}
```

```
uint8_t MySPI_SwapByte(uint8_t ByteSend)//使用SPI模式0交换传输一个字节
{
	uint8_t i, ByteReceive = 0x00;//定义接收的数据，并赋初值0x00，此处必须赋初值0x00，后面会用到
	for (i = 0; i < 8; i ++)//循环8次，依次交换每一位数据
	{
		MySPI_W_MOSI(ByteSend & (0x80 >> i));//使用掩码的方式取出ByteSend的指定一位数据并写入到MOSI线
		MySPI_W_SCK(1);//拉高SCK，上升沿移出数据
		if (MySPI_R_MISO() == 1){ByteReceive |= (0x80 >> i);}//读取MISO数据，并存储到Byte变量;当MISO为1时，置变量指定位为1，当MISO为0时，不做处理，指定位为默认的初值0
		MySPI_W_SCK(0);//拉低SCK，下降沿移入数据
	}
	return ByteReceive;//返回接收到的一个字节数据
}
```

```
/**
  * 函    数：MPU6050读取ID号
  * 参    数：MID 工厂ID，使用输出参数的形式返回
  * 参    数：DID 设备ID，使用输出参数的形式返回
  * 返 回 值：无
    */
void W25Q64_ReadID(uint8_t *MID, uint16_t *DID)
{
	MySPI_Start();								//SPI起始
	MySPI_SwapByte(W25Q64_JEDEC_ID);			//交换发送读取ID的指令
	*MID = MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//交换接收MID，通过输出参数返回
	*DID = MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//交换接收DID高8位
	*DID <<= 8;									//高8位移到高位
	*DID |= MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//或上交换接收DID的低8位，通过输出参数返回
	MySPI_Stop();								//SPI终止
}

```

```

/**
  * 函    数：W25Q64写使能
  * 参    数：无
  * 返 回 值：无
    */
void W25Q64_WriteEnable(void)
{
	MySPI_Start();								//SPI起始
	MySPI_SwapByte(W25Q64_WRITE_ENABLE);		//交换发送写使能的指令
	MySPI_Stop();								//SPI终止
}


```

```
/**
  * 函    数：W25Q64等待忙
  * 参    数：无
  * 返 回 值：无
    */
void W25Q64_WaitBusy(void)
{
	uint32_t Timeout;
	MySPI_Start();								//SPI起始
	MySPI_SwapByte(W25Q64_READ_STATUS_REGISTER_1);				//交换发送读状态寄存器1的指令
	Timeout = 100000;							//给定超时计数时间
	while ((MySPI_SwapByte(W25Q64_DUMMY_BYTE) & 0x01) == 0x01)	//循环等待忙标志位
	{
		Timeout --;								//等待时，计数值自减
		if (Timeout == 0)						//自减到0后，等待超时
		{
			/*超时的错误处理代码，可以添加到此处*/
			break;								//跳出等待，不等了
		}
	}
	MySPI_Stop();								//SPI终止
}


```

```
/**
  * 函    数：W25Q64页编程
  * 参    数：Address 页编程的起始地址，范围：0x000000~0x7FFFFF
  * 参    数：DataArray	用于写入数据的数组
  * 参    数：Count 要写入数据的数量，范围：0~256
  * 返 回 值：无
  * 注意事项：写入的地址范围不能跨页
    */
void W25Q64_PageProgram(uint32_t Address, uint8_t *DataArray, uint16_t Count)
{
	uint16_t i;
	
	W25Q64_WriteEnable();						//写使能
	
	MySPI_Start();								//SPI起始
	MySPI_SwapByte(W25Q64_PAGE_PROGRAM);		//交换发送页编程的指令
	MySPI_SwapByte(Address >> 16);				//交换发送地址23~16位
	MySPI_SwapByte(Address >> 8);				//交换发送地址15~8位
	MySPI_SwapByte(Address);					//交换发送地址7~0位
	for (i = 0; i < Count; i ++)				//循环Count次
	{
		MySPI_SwapByte(DataArray[i]);			//依次在起始地址后写入数据
	}
	MySPI_Stop();								//SPI终止
	
	W25Q64_WaitBusy();							//等待忙
}

```

```

/**
  * 函    数：W25Q64扇区擦除（4KB）
  * 参    数：Address 指定扇区的地址，范围：0x000000~0x7FFFFF
  * 返 回 值：无
    */
void W25Q64_SectorErase(uint32_t Address)
{
	W25Q64_WriteEnable();						//写使能
	
	MySPI_Start();								//SPI起始
	MySPI_SwapByte(W25Q64_SECTOR_ERASE_4KB);	//交换发送扇区擦除的指令
	MySPI_SwapByte(Address >> 16);				//交换发送地址23~16位
	MySPI_SwapByte(Address >> 8);				//交换发送地址15~8位
	MySPI_SwapByte(Address);					//交换发送地址7~0位
	MySPI_Stop();								//SPI终止
	
	W25Q64_WaitBusy();							//等待忙
}


```

```
/**
  * 函    数：W25Q64读取数据
  * 参    数：Address 读取数据的起始地址，范围：0x000000~0x7FFFFF
  * 参    数：DataArray 用于接收读取数据的数组，通过输出参数返回
  * 参    数：Count 要读取数据的数量，范围：0~0x800000
  * 返 回 值：无
    */
void W25Q64_ReadData(uint32_t Address, uint8_t *DataArray, uint32_t Count)
{
	uint32_t i;
	MySPI_Start();								//SPI起始
	MySPI_SwapByte(W25Q64_READ_DATA);			//交换发送读取数据的指令
	MySPI_SwapByte(Address >> 16);				//交换发送地址23~16位
	MySPI_SwapByte(Address >> 8);				//交换发送地址15~8位
	MySPI_SwapByte(Address);					//交换发送地址7~0位
	for (i = 0; i < Count; i ++)				//循环Count次
	{
		DataArray[i] = MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//依次在起始地址后读取数据
	}
	MySPI_Stop();								//SPI终止
}
```



### 硬件SPI

	void MySPI_Init(void)
	{
		RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);	//开启GPIOA的时钟
		RCC_APB1PeriphClockCmd(RCC_APB1Periph_SPI1, ENABLE);//开启SPI1的时钟
		
		GPIO_InitTypeDef GPIO_InitStructure;
		GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
		GPIO_InitStructure.GPIO_Pin = GPIO_Pin_4;
		GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
		GPIO_Init(GPIOA, &GPIO_InitStructure);//将SS引脚初始化为推挽输出
		GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
		GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5 | GPIO_Pin_7;
		GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
		GPIO_Init(GPIOA, &GPIO_InitStructure);//将SCK和MOSI引脚初始化为复用推挽输出
		GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
		GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
		GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
		GPIO_Init(GPIOA, &GPIO_InitStructure);//将MISO引脚初始化为上拉输入
	    
		SPI_InitTypeDef SPI_InitStructure;//SPI初始化定义结构体变量
		SPI_InitStructure.SPI_Mode = SPI_Mode_Master;//模式，选择为SPI主模式
		SPI_InitStructure.SPI_Direction = SPI_Direction_2Lines_FullDuplex;//方向，选择2线全双工
		SPI_InitStructure.SPI_DataSize = SPI_DataSize_8b;//数据宽度，选择为8位
		SPI_InitStructure.SPI_FirstBit = SPI_FirstBit_MSB;//先行位，选择高位先行
		SPI_InitStructure.SPI_BaudRatePrescaler = SPI_BaudRatePrescaler_128;//波特率分频，选择128分频
		SPI_InitStructure.SPI_CPOL = SPI_CPOL_Low;//SPI极性，选择低极性
		SPI_InitStructure.SPI_CPHA = SPI_CPHA_1Edge;//SPI相位，选择第一个时钟边沿采样，极性和相位决定选择SPI模式0
		SPI_InitStructure.SPI_NSS = SPI_NSS_Soft;//NSS，选择由软件控制
		SPI_InitStructure.SPI_CRCPolynomial = 7;//CRC多项式，暂时用不到，给默认值7
		SPI_Init(SPI1, &SPI_InitStructure);//将结构体变量交给SPI_Init，配置SPI1
	
		SPI_Cmd(SPI1, ENABLE);//使能SPI1，开始运行
	    MySPI_W_SS(1);//设置默认电平,SS默认高电平
	}
* SPI_Mode：

  | SPI_Mode        | 描述        |      |
  | --------------- | ----------- | ---- |
  | SPI_Mode_Master | 设置为主SPI |      |
  | SPI_Mode_Slave  | 设置为从SPI |      |
  
* SPI_Direction：

  | SPI_Direction                   | 描述                     |      |
  | ------------------------------- | ------------------------ | ---- |
  | SPI_Direction_2Lines_FullDuplex | SPI 设置为双线双向全双工 |      |
  | SPI_Direction_2Lines_RxOnly     | SPI 设置为双线单向接收   |      |
  | SPI_Direction_1Line_Rx          | SPI 设置为单线双向接收   |      |
  | SPI_Direction_1Line_Tx          | SPI 设置为单线双向发送   |      |

* SPI_DataSize：
  
  | SPI_DataSize     | 描述                   |      |
  | ---------------- | ---------------------- | ---- |
  | SPI_DataSize_16b | SPI 发送接收16位帧结构 |      |
  | SPI_DataSize_8b  | SPI 发送接收8位帧结构  |      |
  
* SPI_FirstBit：

  | SPI_FirstBit     | 描述                            |      |
  | ---------------- | ------------------------------- | ---- |
  | SPI_FisrtBit_MSB | 数据传输从MSB位开始（高位先行） |      |
  | SPI_FisrtBit_LSB | 数据传输从LSB位开始（低位先行） |      |

* SPI_BaudRatePrescaler：波特率预分频的值，可选2到256之间的2的次方数，如：SPI_BaudRatePrescaler128

* SPI_CPOL：

  | SPI_CPOL      | 描述       |      |
  | ------------- | ---------- | ---- |
  | SPI_CPOL_High | 时钟悬空高 |      |
  | SPI_CPOL_Low  | 时钟悬空低 |      |

* SPI_CPHA：

  | SPI_CPHA       | 描述                   |      |
  | -------------- | ---------------------- | ---- |
  | SPI_CPHA_2Edge | 数据捕获于第二个时钟沿 |      |
  | SPI_CPHA_1Edge | 数据捕获于第一个时钟沿 |      |

* SPI_CPHA SPI_NSS：

  | SPI_CPHA SPI_NSS | 描述                   |      |
  | ---------------- | ---------------------- | ---- |
  | SPI_NSS_Hard     | NSS 由外部管脚管理     |      |
  | SPI_NSS_Soft     | 内部NSS信号有SSI位控制 |      |

* SPI_CRCPolynomial：默认7，暂时不改。

```
uint8_t MySPI_SwapByte(uint8_t ByteSend)//使用SPI模式0交换传输一个字节
{
	while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_TXE) != SET);//等待发送数据寄存器空
	SPI_I2S_SendData(SPI1, ByteSend);//写入数据到发送数据寄存器，开始产生时序
	while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_RXNE) != SET);//等待接收数据寄存器非空
	return SPI_I2S_ReceiveData(SPI1);//读取接收到的数据并返回
}
```

```
/**

  * 函    数：MPU6050读取ID号
  * 参    数：MID 工厂ID，使用输出参数的形式返回
  * 参    数：DID 设备ID，使用输出参数的形式返回
  * 返 回 值：无
    */
    void W25Q64_ReadID(uint8_t *MID, uint16_t *DID)
    {
    MySPI_Start();								//SPI起始
    MySPI_SwapByte(W25Q64_JEDEC_ID);			//交换发送读取ID的指令
    *MID = MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//交换接收MID，通过输出参数返回
    *DID = MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//交换接收DID高8位
    *DID <<= 8;									//高8位移到高位
    *DID |= MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//或上交换接收DID的低8位，通过输出参数返回
    MySPI_Stop();								//SPI终止
    }


```

```
/**

  * 函    数：W25Q64写使能
  * 参    数：无
  * 返 回 值：无
    */
    void W25Q64_WriteEnable(void)
    {
    MySPI_Start();								//SPI起始
    MySPI_SwapByte(W25Q64_WRITE_ENABLE);		//交换发送写使能的指令
    MySPI_Stop();								//SPI终止
    }


```

```
/**

  * 函    数：W25Q64等待忙
  * 参    数：无
  * 返 回 值：无
    */
    void W25Q64_WaitBusy(void)
    {
    uint32_t Timeout;
    MySPI_Start();								//SPI起始
    MySPI_SwapByte(W25Q64_READ_STATUS_REGISTER_1);				//交换发送读状态寄存器1的指令
    Timeout = 100000;							//给定超时计数时间
    while ((MySPI_SwapByte(W25Q64_DUMMY_BYTE) & 0x01) == 0x01)	//循环等待忙标志位
    {
    	Timeout --;								//等待时，计数值自减
    	if (Timeout == 0)						//自减到0后，等待超时
    	{
    		/*超时的错误处理代码，可以添加到此处*/
    		break;								//跳出等待，不等了
    	}
    }
    MySPI_Stop();								//SPI终止
    }


```

```
/**

  * 函    数：W25Q64页编程

  * 参    数：Address 页编程的起始地址，范围：0x000000~0x7FFFFF

  * 参    数：DataArray	用于写入数据的数组

  * 参    数：Count 要写入数据的数量，范围：0~256

  * 返 回 值：无

  * 注意事项：写入的地址范围不能跨页
    */
    void W25Q64_PageProgram(uint32_t Address, uint8_t *DataArray, uint16_t Count)
    {
    uint16_t i;

    W25Q64_WriteEnable();						//写使能

    MySPI_Start();								//SPI起始
    MySPI_SwapByte(W25Q64_PAGE_PROGRAM);		//交换发送页编程的指令
    MySPI_SwapByte(Address >> 16);				//交换发送地址23~16位
    MySPI_SwapByte(Address >> 8);				//交换发送地址15~8位
    MySPI_SwapByte(Address);					//交换发送地址7~0位
    for (i = 0; i < Count; i ++)				//循环Count次
    {
    	MySPI_SwapByte(DataArray[i]);			//依次在起始地址后写入数据
    }
    MySPI_Stop();								//SPI终止

    W25Q64_WaitBusy();							//等待忙
    }


```

```
/**

  * 函    数：W25Q64扇区擦除（4KB）

  * 参    数：Address 指定扇区的地址，范围：0x000000~0x7FFFFF

  * 返 回 值：无
    */
    void W25Q64_SectorErase(uint32_t Address)
    {
    W25Q64_WriteEnable();						//写使能

    MySPI_Start();								//SPI起始
    MySPI_SwapByte(W25Q64_SECTOR_ERASE_4KB);	//交换发送扇区擦除的指令
    MySPI_SwapByte(Address >> 16);				//交换发送地址23~16位
    MySPI_SwapByte(Address >> 8);				//交换发送地址15~8位
    MySPI_SwapByte(Address);					//交换发送地址7~0位
    MySPI_Stop();								//SPI终止

    W25Q64_WaitBusy();							//等待忙
    }


```

```
/**

  * 函    数：W25Q64读取数据
  * 参    数：Address 读取数据的起始地址，范围：0x000000~0x7FFFFF
  * 参    数：DataArray 用于接收读取数据的数组，通过输出参数返回
  * 参    数：Count 要读取数据的数量，范围：0~0x800000
  * 返 回 值：无
    */
    void W25Q64_ReadData(uint32_t Address, uint8_t *DataArray, uint32_t Count)
    {
    uint32_t i;
    MySPI_Start();								//SPI起始
    MySPI_SwapByte(W25Q64_READ_DATA);			//交换发送读取数据的指令
    MySPI_SwapByte(Address >> 16);				//交换发送地址23~16位
    MySPI_SwapByte(Address >> 8);				//交换发送地址15~8位
    MySPI_SwapByte(Address);					//交换发送地址7~0位
    for (i = 0; i < Count; i ++)				//循环Count次
    {
    	DataArray[i] = MySPI_SwapByte(W25Q64_DUMMY_BYTE);	//依次在起始地址后读取数据
    }
    MySPI_Stop();								//SPI终止
    }
```



## 看门狗

```
IWDG_WriteAccessCmd(IWDG_WriteAccess_Enable);//独立看门狗写使能
IWDG_SetPrescaler(IWDG_Prescaler_16);//设置预分频为16，可以是4~256中任意一个2的次方
IWDG_SetReload();//设置重装值，独立看门狗的超时时间为[1s/(频率/分频数)]*(重装值+1) 秒
IWDG_ReloadCounter();//重装计数器，喂狗
IWDG_Enable();//独立看门狗使能
```

