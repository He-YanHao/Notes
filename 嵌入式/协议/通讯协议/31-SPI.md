# SPI

## 基础

SPI 通讯使用3条总线及片选线，3条总线分别为SCK、MOSI、MISO，片选线为SS，它们的作用介绍如下：

* SCK(Serial Clock) ：时钟信号线，用于通讯数据同步。它由通讯主机产生，决定了通讯的速率，不同的设备支持的最高时钟频率不一样，如STM32的SPI时钟频率最大为fpclk/2，两个设备之间通讯时，通讯速率受限于低速设备。

* MOSI(Master Output，Slave Input) ：主设备输出/从设备输入引脚。主机的数据从这条信号线输出，从机由这条信号线读入主机发送的数据，即这条线上数据的方向为主机到从机。

* MISO(Master Input,，Slave Output) ：主设备输入/从设备输出引脚。主机从这条信线读入数据， 从机的数据由这条信号线输出到主机，即在这条线上数据的方向为从机到主机。

* SS(Slave Select)：从设备选择信号线，或片选信号线，也称为NSS、CS。当有多个SPI从设备与SPI主机相连时，设备的其它信号线SCK、MOSI及MISO同时并 联到相同的SPI总线上，即无论有多少个从设备，都共同只使用这3条总线；而每个从设备都有独立的这一条NSS信号线，本信号线独占主机的一个引脚，即有多少个从设备，就 有多少条片选信号线。

  > SPI通信没有设备地址，通过SS线确定，当主机要选择从设备时， 把该从设备的SS信号线设置为低电平，该从设备即被选中，即片选有效，所以SS默认高电平，接着主机开始与被选中的从设备进行SPI通讯。所以SPI通讯以SS线置低电平为开始信号，以SS线 被拉高作为结束信号。

MISO初始化为上拉输入，其余初始化为推挽输出。

SPI交换一个字节有四种模式，由空闲状态时SCK的电平和移入数据移出数据相对于SCK的时机共同决定。

0. SPI模式0：CPOL=0：空闲状态时，SCK为低电平；	CPHA=0：SCK第一个边沿移入数据，第二个边沿移出数据；
1. SPI模式1：CPOL=0：空闲状态时，SCK为低电平；	CPHA=1：SCK第一个边沿移出数据，第二个边沿移入数据；
2. SPI模式2：CPOL=1：空闲状态时，SCK为高电平；	CPHA=0：SCK第一个边沿移入数据，第二个边沿移出数据；
3. SPI模式3：CPOL=1：空闲状态时，SCK为高电平；	CPHA=1：SCK第一个边沿移出数据，第二个边沿移入数据；

**针对CPOL和CPHA的理解：**

CPOL就是 SPI 不进行通讯时也就是发送前和发送后的状态。

CPHA中的边沿可以理解为变化，比如

SCK默认低时，第一个边沿时从低变高的过程，第二个边沿从高变低的过程；

SCK默认高时，第一个边沿时从高变低的过程，第二个边沿从低变高的过程；

移出数据就是写入数据，移入数据就是读取数据；

## SPI时序

初始SCK、MOSI、MISO、SS均为高。

SS拉低作为SPI的开始。

这时候

## SPI流程



## SPI代码

### 软件模拟代码部分

**软件层：**

写入SS引脚，非0即1。

```
void MySPI_W_SS(uint8_t BitValue)//SPI写SS引脚电平
{
	GPIO_WriteBit(GPIOA, GPIO_Pin_4, (BitAction)BitValue);
}
```

写入SCK引脚，非0即1。

```
void MySPI_W_SCK(uint8_t BitValue)//SPI写SCK引脚电平
{
GPIO_WriteBit(GPIOA, GPIO_Pin_5, (BitAction)BitValue);
}
```

写入MOSI引脚，非0即1。

```
void MySPI_W_MOSI(uint8_t BitValue)//SPI写MOSI引脚电平
{
	GPIO_WriteBit(GPIOA, GPIO_Pin_7, (BitAction)BitValue);
}
```

读取MISO引脚。

```
uint8_t MySPI_R_MISO(void)//SPI读MISO引脚电平
{
	return GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_6);//读取MISO电平并返回
}
```

SPI初始化

```
/**
  * 函    数：SPI初始化
  * 参    数：无
  * 返 回 值：无
  * 注意事项：此函数需要用户实现内容，实现SS、SCK、MOSI和MISO引脚的初始化
    */
void MySPI_Init(void)
{
	/*开启时钟*/
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);	//开启GPIOA的时钟

	/*GPIO初始化*/
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_4 | GPIO_Pin_5 | GPIO_Pin_7;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA4、PA5和PA7引脚初始化为推挽输出

	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA6引脚初始化为上拉输入

	/*设置默认电平*/
	MySPI_W_SS(1);											//SS默认高电平
	MySPI_W_SCK(0);											//SCK默认低电平
}
```

**协议层：**

拉低SS引脚就是SPI的开始。

```
void MySPI_Start(void)//SPI起始
{
	MySPI_W_SS(0);//拉低SS，开始时序
}
```

拉高SS引脚就是SPI的终止。

```
void MySPI_Stop(void)//SPI终止
{
	MySPI_W_SS(1);				//拉高SS，终止时序
}
```

SPI交换传输一个字节，使用SPI模式0

```
/**
  * 函    数：SPI交换传输一个字节，使用SPI模式0
  * 参    数：ByteSend 要发送的一个字节
  * 返 回 值：接收的一个字节
  */
uint8_t MySPI_SwapByte(uint8_t ByteSend)
{
	uint8_t i, ByteReceive = 0x00;					//定义接收的数据，并赋初值0x00，此处必须赋初值0x00，后面会用到
	for (i = 0; i < 8; i ++)						//循环8次，依次交换每一位数据
	{
		MySPI_W_MOSI(ByteSend & (0x80 >> i));		//使用掩码的方式取出ByteSend的指定一位数据并写入到MOSI线
		MySPI_W_SCK(1);								//拉高SCK，上升沿移出数据
		if (MySPI_R_MISO() == 1){ByteReceive |= (0x80 >> i);}	//读取MISO数据，并存储到Byte变量
																//当MISO为1时，置变量指定位为1，当MISO为0时，不做处理，指定位为默认的初值0
		MySPI_W_SCK(0);								//拉低SCK，下降沿移入数据
	}
	return ByteReceive;								//返回接收到的一个字节数据
}
```

SPI交换传输一个字节，使用SPI模式1

```
/**
  * 函    数：SPI交换传输一个字节，使用SPI模式1
  * 参    数：ByteSend 要发送的一个字节
  * 返 回 值：接收的一个字节
  */
uint8_t MySPI_SwapByte(uint8_t ByteSend)
{
	uint8_t i, ByteReceive = 0x00;					//定义接收的数据，并赋初值0x00，此处必须赋初值0x00，后面会用到
	for (i = 0; i < 8; i ++)						//循环8次，依次交换每一位数据
	{
		if (MySPI_R_MISO() == 1){ByteReceive |= (0x80 >> i);}	//读取MISO数据，并存储到Byte变量
																//当MISO为1时，置变量指定位为1，当MISO为0时，不做处理，指定位为默认的初值0
		MySPI_W_SCK(1);								//拉高SCK，上升沿移出数据
		MySPI_W_MOSI(ByteSend & (0x80 >> i));		//使用掩码的方式取出ByteSend的指定一位数据并写入到MOSI线
		MySPI_W_SCK(0);								//拉低SCK，下降沿移入数据
	}
	return ByteReceive;								//返回接收到的一个字节数据
}
```

SPI交换传输一个字节，使用SPI模式2

```
/**
  * 函    数：SPI交换传输一个字节，使用SPI模式2
  * 参    数：ByteSend 要发送的一个字节
  * 返 回 值：接收的一个字节
  */
uint8_t MySPI_SwapByte(uint8_t ByteSend)
{
	uint8_t i, ByteReceive = 0x00;					//定义接收的数据，并赋初值0x00，此处必须赋初值0x00，后面会用到
	for (i = 0; i < 8; i ++)						//循环8次，依次交换每一位数据
	{
		MySPI_W_MOSI(ByteSend & (0x80 >> i));		//使用掩码的方式取出ByteSend的指定一位数据并写入到MOSI线
		MySPI_W_SCK(0);								//拉低SCK，下降沿移出数据
		if (MySPI_R_MISO() == 1){ByteReceive |= (0x80 >> i);}	//读取MISO数据，并存储到Byte变量
																//当MISO为1时，置变量指定位为1，当MISO为0时，不做处理，指定位为默认的初值0
		MySPI_W_SCK(1);								//拉高SCK，上升沿移入数据
	}
	return ByteReceive;								//返回接收到的一个字节数据
}
```

SPI交换传输一个字节，使用SPI模式3

```
/**
  * 函    数：SPI交换传输一个字节，使用SPI模式3
  * 参    数：ByteSend 要发送的一个字节
  * 返 回 值：接收的一个字节
  */
uint8_t MySPI_SwapByte(uint8_t ByteSend)
{
	uint8_t i, ByteReceive = 0x00;					//定义接收的数据，并赋初值0x00，此处必须赋初值0x00，后面会用到
	for (i = 0; i < 8; i ++)						//循环8次，依次交换每一位数据
	{
		if (MySPI_R_MISO() == 1){ByteReceive |= (0x80 >> i);}	//读取MISO数据，并存储到Byte变量
																//当MISO为1时，置变量指定位为1，当MISO为0时，不做处理，指定位为默认的初值0
		MySPI_W_SCK(0);								//拉低SCK，下降沿移出数据
		MySPI_W_MOSI(ByteSend & (0x80 >> i));		//使用掩码的方式取出ByteSend的指定一位数据并写入到MOSI线
		MySPI_W_SCK(1);								//拉高SCK，上升沿移入数据
	}
	return ByteReceive;								//返回接收到的一个字节数据
}
```



### 硬件SPI代码部分

硬件SPI中SS片选线仍旧可以使用软件模拟。

```
void MySPI_W_SS(uint8_t BitValue)//SPI写SS引脚电平，仍由软件模拟
{
	GPIO_WriteBit(GPIOA, GPIO_Pin_4, (BitAction)BitValue);		//根据BitValue，设置SS引脚的电平
}
```

```
void MySPI_Start(void)//SPI起始
{
	MySPI_W_SS(0);				//拉低SS，开始时序
}
```

```c
void MySPI_Stop(void)//SPI终止
{
	MySPI_W_SS(1);				//拉高SS，终止时序
}
```

硬件SPI初始化

```c
void MySPI_Init(void)//SPI初始化
{
	/*开启时钟*/
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);	//开启GPIOA的时钟
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_SPI1, ENABLE);	//开启SPI1的时钟

	/*GPIO初始化*/
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_4;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA4SS片选引脚初始化为推挽输出；若为SS不为软件模拟则和MIOS和MOIS一起初始化为复用推挽输出。

	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5 | GPIO_Pin_7;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA5和PA7引脚初始化为复用推挽输出

	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA6引脚初始化为上拉输入

	/*SPI初始化*/
	SPI_InitTypeDef SPI_InitStructure;						//定义结构体变量
	SPI_InitStructure.SPI_Mode = SPI_Mode_Master;			//模式，选择为SPI主模式
	SPI_InitStructure.SPI_Direction = SPI_Direction_2Lines_FullDuplex;	//方向，选择2线全双工
	SPI_InitStructure.SPI_DataSize = SPI_DataSize_8b;		//数据宽度，选择为8位
	SPI_InitStructure.SPI_FirstBit = SPI_FirstBit_MSB;		//先行位，选择高位先行
	SPI_InitStructure.SPI_BaudRatePrescaler = SPI_BaudRatePrescaler_128;	//波特率分频，选择128分频
	SPI_InitStructure.SPI_CPOL = SPI_CPOL_Low;				//SPI极性，选择低极性
	SPI_InitStructure.SPI_CPHA = SPI_CPHA_1Edge;			//SPI相位，选择第一个时钟边沿采样，极性和相位决定选择SPI模式0
	SPI_InitStructure.SPI_NSS = SPI_NSS_Soft;				//NSS，选择由软件控制
	SPI_InitStructure.SPI_CRCPolynomial = 7;				//CRC多项式，暂时用不到，给默认值7
	SPI_Init(SPI1, &SPI_InitStructure);						//将结构体变量交给SPI_Init，配置SPI1

	/*SPI使能*/
	SPI_Cmd(SPI1, ENABLE);									//使能SPI1，开始运行

	/*设置默认电平*/
	MySPI1_W_SS(1);											//SS默认高电平
}
```

SPI交换一个字节

```c
/**
  * 函    数：SPI交换传输一个字节，使用SPI模式0
  * 参    数：ByteSend 要发送的一个字节
  * 返 回 值：接收的一个字节
  */
uint8_t MySPI_SwapByte(uint8_t ByteSend)
{
	while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_TXE) != SET);	//等待发送数据寄存器空
	SPI_I2S_SendData(SPI1, ByteSend);								//写入数据到发送数据寄存器，开始产生时序
	while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_RXNE) != SET);	//等待接收数据寄存器非空
	return SPI_I2S_ReceiveData(SPI1);								//读取接收到的数据并返回
}
```

