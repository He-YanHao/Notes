# UART

## 串口通信

### 理论

串口通信属于 串行通讯 全双工 异步通讯 。

包含两根数据线，分别是：

* TX：发送数据输出引脚
* RX：接收数据输入引脚

在连接的时候要注意交叉连接，若只需单向的数据传输时，可以只接一根通信线。

串口通信的数据帧包括起始位、有效数据位、校验位、以及停止位。数据帧格式需要我们提前约定好。

- 起始位：标志一个数据帧的开始，固定为低电平，所以说，平时数据线默认高电平。

- 数据位：数据帧的有效载荷，1为高电平，0为低电平。有效数据位是低位（LSB）在前，高位（MSB）在后。

  - 很久之前的串口通信可能发低于8比特的数据，低位先行有助于数据获取。

- 校验位：校验位校验位可以认为是一个特殊的数据位。校验位一般用来判断接收的数据位有无错误，检验方法有：奇检验、偶检验、0检验、1检验以及无检验。

  > * 奇检验：
  > * 偶检验：
  > * 0检验：指不管有效数据中的内容是什么，校验位总为“0”。
  > * 1检验：指不管有效数据中的内容是什么，校验位总为“1”。
  > * 无检验：不进行检验。

- 停止位：数据帧的停止位可以是0.5、1、1.5或2个逻辑1的数据位表示，只要双方约定一致即可。

串口分为两种：USART（即通用同步异步收发器）和UART（即通用异步收发器）。

UART是在USART基础上裁剪掉了同步通信功能，只剩下异步通信功能。简单区分同步和异步就是看通信时需不需要对外提供时钟输出，我们平时用串口通信基本都是异步通信。

在单片机中使用串口通讯时，需要以下步骤：

1. 开启GPIO和串口的时钟。
2. GPIO初始化。TX发送数据输出引脚初始化为复用推挽输出，RX接收数据输入引脚初始化为上拉输入。
3. 串口初始化。
4. 中断初始化。定义串口接收中断。
5. USART使能。

### STM32F1XX硬件串口通讯函数

**USART参数：**

```c
typedef struct 
{ 
u32 USART_BaudRate;             //波特率
u16 USART_WordLength;           //字长
u16 USART_StopBits;             //停止位
u16 USART_Parity;               //奇偶校验
u16 USART_HardwareFlowControl;  //硬件流控制
u16 USART_Mode;                 //模式
u16 USART_Clock;                //
u16 USART_CPOL;                 //
u16 USART_CPHA;                 //
u16 USART_LastBit;              //
} USART_InitTypeDef;
```

- USART_BaudRate：波特率，IntegerDivider = ((APBClock) / (16 * (USART_InitStruct->USART_BaudRate)))  FractionalDivider = ((IntegerDivider - ((u32) IntegerDivider)) * 16) + 0.5。
- USART_WordLength：字长，可取值：
  - USART_WordLength_8b：8位数据。
  - USART_WordLength_9b：9位数据。
- USART_StopBits：停止位，可取值：
  - USART_StopBits_1：在帧结尾传输 1 个停止位。
  - USART_StopBits_0.5：在帧结尾传输 0.5 个停止位。
  - USART_StopBits_2：在帧结尾传输 2 个停止位。
  - USART_StopBits_1.5：在帧结尾传输 1.5 个停止位。
- USART_Parity：奇偶校验，可取值：
  - USART_Parity_No 奇偶失能，也就是不进行校验。
  - USART_Parity_Even 偶模式。
  - USART_Parity_Odd 奇模式。

- USART_HardwareFlowControl：硬件流控制
  - USART_HardwareFlowControl_None 硬件流控制失能
  - USART_HardwareFlowControl_RTS 发送请求 RTS 使能
  - USART_HardwareFlowControl_CTS 清除发送 CTS 使能
  - USART_HardwareFlowControl_RTS_CTS RTS 和 CTS 使能
- USART_Mode：模式。
  - USART_Mode_Tx 发送使能
  - USART_Mode_Rx 接收使能

以下参数在进行UART时不包含。

- USART_Clock：时钟
  - USART_Clock_Enable 时钟使能。
  - USART_Clock_Disable 时钟失能。
- USART_CPOL：
  - USART_CPOL_High 时钟高电平
  - USART_CPOL_Low 时钟低电平
- USART_CPHA：
  - USART_CPHA_1Edge 时钟第一个边沿进行数据捕获
  - USART_CPHA_2Edge 时钟第二个边沿进行数据捕获
- USART_LastBit：
  - USART_LastBit_Disable 最后一位数据的时钟脉冲不从 SCLK 输出
  - USART_LastBit_Enable 最后一位数据的时钟脉冲从 SCLK 输出

写入数据寄存器，硬件自动发。

```c
/**
  * 函    数：串口发送一个字节
  * 参    数：Byte 要发送的一个字节
  * 返 回 值：无
  */
void Serial_SendByte(uint8_t Byte)
{
	USART_SendData(USART1, Byte);		//将字节数据写入数据寄存器，写入后USART自动生成时序波形
	while (USART_GetFlagStatus(USART1, USART_FLAG_TXE) == RESET);	//等待发送完成
	/*下次写入数据寄存器会自动清除发送完成标志位，故此循环后，无需清除标志位*/
}
```

重复上一步即可。

```c
/**
  * 函    数：串口发送一个数组
  * 参    数：Array 要发送数组的首地址
  * 参    数：Length 要发送数组的长度
  * 返 回 值：无
  */
void Serial_SendArray(uint8_t *Array, uint16_t Length)
{
	uint16_t i;
	for (i = 0; i < Length; i ++)		//遍历数组
	{
		Serial_SendByte(Array[i]);		//依次调用Serial_SendByte发送每个字节数据
	}
}
```

直到判断`\0`出现为止。

```c
/**
  * 函    数：串口发送一个字符串
  * 参    数：String 要发送字符串的首地址
  * 返 回 值：无
  */
void Serial_SendString(char *String)
{
	uint8_t i;
	for (i = 0; String[i] != '\0'; i ++)//遍历字符数组（字符串），遇到字符串结束标志位后停止
	{
		Serial_SendByte(String[i]);		//依次调用Serial_SendByte发送每个字节数据
	}
}
```

下面二者共同构成了发送数字的函数。

```c
/**
  * 函    数：次方函数（内部使用）
  * 返 回 值：返回值等于X的Y次方
  */
uint32_t Serial_Pow(uint32_t X, uint32_t Y)
{
	uint32_t Result = 1;	//设置结果初值为1
	while (Y --)			//执行Y次
	{
		Result *= X;		//将X累乘到结果
	}
	return Result;
}
```

```c
/**
  * 函    数：串口发送数字
  * 参    数：Number 要发送的数字，范围：0~4294967295
  * 参    数：Length 要发送数字的长度，范围：0~10
  * 返 回 值：无
  */
void Serial_SendNumber(uint32_t Number, uint8_t Length)
{
	uint8_t i;
	for (i = 0; i < Length; i ++)		//根据数字长度遍历数字的每一位
	{
		Serial_SendByte(Number / Serial_Pow(10, Length - i - 1) % 10 + '0');	//依次调用Serial_SendByte发送每位数字
	}
}
```

printf

```c
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

```c
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

串口发送数据包，内容将加上包头（FF）包尾（FE）后，作为数据包发送出去。

```c
/**
  * 函    数：串口发送数据包
  * 参    数：无
  * 返 回 值：无
  * 说    明：调用此函数后，Serial_TxPacket数组的内容将加上包头（FF）包尾（FE）后，作为数据包发送出去
  */
void Serial_SendPacket(void)
{
	Serial_SendByte(0xFF);
	Serial_SendArray(Serial_TxPacket, 4);
	Serial_SendByte(0xFE);
}
```

获取串口接收数据包标志位

```c
/**
  * 函    数：获取串口接收数据包标志位
  * 参    数：无
  * 返 回 值：串口接收数据包标志位，范围：0~1，接收到数据包后，标志位置1，读取后标志位自动清零
  */
uint8_t Serial_GetRxFlag(void)
{
	if (Serial_RxFlag == 1)			//如果标志位为1
	{
		Serial_RxFlag = 0;
		return 1;					//则返回1，并自动清零标志位
	}
	return 0;						//如果标志位为0，则返回0
}
```

```c
/**
  * 函    数：USART1中断函数
  * 参    数：无
  * 返 回 值：无
  * 注意事项：此函数为中断函数，无需调用，中断触发后自动执行
  *           函数名为预留的指定名称，可以从启动文件复制
  *           请确保函数名正确，不能有任何差异，否则中断函数将不能进入
  */
void USART1_IRQHandler(void)
{
	static uint8_t RxState = 0;		//定义表示当前状态机状态的静态变量
	static uint8_t pRxPacket = 0;	//定义表示当前接收数据位置的静态变量
	if (USART_GetITStatus(USART1, USART_IT_RXNE) == SET)		//判断是否是USART1的接收事件触发的中断
	{
		uint8_t RxData = USART_ReceiveData(USART1);				//读取数据寄存器，存放在接收的数据变量
		/*使用状态机的思路，依次处理数据包的不同部分*/
		/*当前状态为0，接收数据包包头*/
		if (RxState == 0)
		{
			if (RxData == 0xFF)			//如果数据确实是包头
			{
				RxState = 1;			//置下一个状态
				pRxPacket = 0;			//数据包的位置归零
			}
		}
		/*当前状态为1，接收数据包数据*/
		else if (RxState == 1)
		{
			Serial_RxPacket[pRxPacket] = RxData;	//将数据存入数据包数组的指定位置
			pRxPacket ++;				//数据包的位置自增
			if (pRxPacket >= 4)			//如果收够4个数据
			{
				RxState = 2;			//置下一个状态
			}
		}
		/*当前状态为2，接收数据包包尾*/
		else if (RxState == 2)
		{
			if (RxData == 0xFE)			//如果数据确实是包尾部
			{
				RxState = 0;			//状态归0
				Serial_RxFlag = 1;		//接收数据包标志位置1，成功接收一个数据包
			}
		}
		USART_ClearITPendingBit(USART1, USART_IT_RXNE);		//清除标志位
	}
}
```

## 循环缓冲区

