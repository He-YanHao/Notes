# SD卡

## 概述

### SD卡分类

- 标准的SD卡，这种卡比较大，在有些相机或者PC电脑上会使用；
- 第二种是miniSD，比较少见，不作详述；
- 最后一种是叫TF卡，也称mirco SD，这种卡比较小，是我们最常接触的，像我们的手机里面使用的就是这种卡。很多人基本上都管我们手机使用的那种卡叫SD卡，这样的叫法实际上不够准确，更准确应该是叫TF卡，但是不管怎样，都没人会去计较，能理解就行。

### mirco SD（TF）卡的工作模式

SD卡默认SD模式，可以通过发送RESTE命令（CMD0） 时，拉低CS进入SPI模式。

| mirco SD |         | SD Mode  |                  |        |      | SPI Mode |                 |
| -------- | ------- | -------- | ---------------- | ------ | ---- | -------- | --------------- |
| 引脚编号 | 引脚名  | 引脚类型 | 功能描述         | 引脚名 | 模块 | 引脚类型 | 功能描述        |
| 1        | DAT2    | I/O/PP   | 数据线位2        | RSV    |      |          |                 |
| 2        | CD/DAT3 | I/O/PP   | 卡识别/数据线位3 | CS     | 1    | I        | 片选 低电平有效 |
| 3        | CMD     | PP       | 命令/响应        | DI     | 3    | I        | 数据输入        |
| 4        | VDD     | S        | DC电源正极       | VDD    | 5    | S        | DC 电源正极     |
| 5        | CLK     | I        | 时钟             | SCLK   | 2    | I        | 时钟            |
| 6        | VSS     | S        | 电源地           | VSS    | 6    | S        | 电源地          |
| 7        | DAT0    | I/O/PP   | 数据线位0        | DO     | 4    | O/PP     | 数据输出        |
| 8        | DAT1    | I/O/PP   | 数据线位1        | RSV    |      |          |                 |

> S:电源I：输入O：推挽输出PP：推挽

一张SD卡包括有存储单元、存储单元接口、电源检测、卡及接口控制器和接口驱动器5个部分。

**存储单元**是存储数据部件，存储单元通过**存储单元接口**与**卡控制单元**进行数据传输；

**电源检测单元**保证SD卡工作在合适的电压下，如出现掉电或上状态时，它会使控制单元和存储单元接口复位；

**卡及接口控制单元**控制SD卡的运行状态，它包括有8个寄存器；

**接口驱动器**控制SD卡引脚的输入输出。

## 命令及相关

### 寄存器

SD卡总共有8个寄存器，用于设定或表示SD卡信息。

这些寄存器只能通过对应的命令访问，对SD卡进行控制操作并不是像操作控制器GPIO相关寄存器那样一次读写一个寄存器的，它是通过命令来控制，SDIO定义了64个命令，每个命令都有特殊意义，可以实现某一特定功能，SD卡接收到命令后，根据命令要求对SD卡内部寄存器进行修改，程序控制中只需要发送组合命令就可以实现SD卡的控制以及读写操作。

| 名称 | bit宽度 |                             描述                             |
| :--: | :-----: | :----------------------------------------------------------: |
| CID  |   128   | 卡识别号(Card identification number):用来识别的卡的个体号码(唯一的) |
| RCA  |   16    | 相对地址(Relative card address):卡的本地系统地址，初始化时，动态地由卡建议，主机核准。 |
| DSR  |   16    |     驱动级寄存器(Driver Stage Register):配置卡的输出驱动     |
| CSD  |   128   |      卡的特定数据(Card Specific Data):卡的操作条件信息       |
| SCR  |   64    |   SD配置寄存器(SD Configuration Register):SD卡特殊特性信息   |
| OCR  |   32    |        操作条件寄存器(Operation conditions register)         |
| SSR  |   512   |             SD状态(SD Status):SD卡专有特征的信息             |
| CSR  |   32    |                卡状态(Card Status):卡状态信息                |



### 命令

SD命令由主机发出，以广播命令和寻址命令为例，广播命令是针对与SD主机总线连接的所有从设备发送的，寻址命令是指定某个地址设备进行命令传输。

SD命令有4种类型：

- 无响应广播命令(bc)，发送到所有卡，不返回任务响应；
- 带响应广播命令(bcr)，发送到所有卡，同时接收来自所有卡响应；
- 寻址命令(ac)，发送到选定卡，DAT线无数据传输；
- 寻址数据传输命令(adtc)，发送到选定卡，DAT线有数据传输。

SD卡的命令固定为48位，由6个字节组成。

0X XX XX XX XX XX XX;

0B 0000 0000  0000 0000  0000 0000  0000 0000  0000 0000  0000 0000;

- 字节1：
  - 字节1的最高位固定为0。
  - 第二高位为1时表示命令，方向为主机传输到SD卡，该位为0时表示响应，方向为SD卡传输到主机。
  - 低6位为命令号，共有64个命令(代号：CMD0~CMD63)，。
- 字节2~5为命令参数，有些命令是没有参数的。
- 字节6：
  - 字节6的高七位为CRC校验值，用于验证命令传输内容正确性，如果发生外部干扰导致传输数据个别位状态改变将导致校准失败，也意味着命令传输失败，SD卡不执行命令。
  - 最低位恒定为1。

> - 例如CMD16: 0B 0101 0000  0000 0000  0000 0000  0000 0000  0000 0000  0000 0000;

| 命令         | 参数             | 响应 | 描述                                |
| ------------ | ---------------- | ---- | ----------------------------------- |
| CMD0(0X00)   | NONE             | 无   | 复位SD卡                            |
| CMD1(0X)     | NONE             | R1   | 激活初始化                          |
| CMD2(0X02)   | NONE             | R2   | 读取SD卡的CID寄存器值               |
| CMD3(0X03)   | NONE             | R6   | 要求SD卡发布新的相对地址            |
| CMD7(0X07)   | RCA              | R1b  | 选中SD卡                            |
| CMD8(0X08)   | VHS+Checkpattern | R7   | 发送接口状态命令                    |
| CMD9(0X09)   | RCA              | R2   | 获得选定卡的CSD寄存器的内容         |
| CMD13(0x0D)  | RCA              | R1   | 被选中的卡返回其状态                |
| CMD16(0X10)  | 块大小           | R1   | 设置块大小（字节数）                |
| CMD17(0X11)  | 地址             | R1   | 读取一个块的数据                    |
| CMD24(0X18)  | 地址             | R1   | 写入一个块的数据                    |
| CMD55(0X37)  | NONE             | R1   | 告诉SD卡，下一个是特定应用命令      |
| ACMD41(0X29) | HCS+VDD电压      | R3   | 主机发送容量支持信息和OCR寄存器内容 |

上表中，大部分的命令是初始化的时候用的。

### 参数

### 响应

SD卡共有七个响应类型（R1~R7）。

在主机发送有响应的命令后，SD卡都会给出相对应的应答，以告知主机该命令的执行情况，或者返回主机需要获取的数据。

与命令一样，SD卡的响应也是通过CMD线连续传输的。

SD的响应大体分为短响应48bit和长响应136bit，每个响应也有规定好的格式。R1、R1b、R3、R6和R7属于短响应，而R2属于长响应。

| 响应 | 长度   | 描述                                        |
| ---- | ------ | ------------------------------------------- |
| R1   | 48bit  | 正常响应，告知卡的状态。                    |
| R1b  | 48bit  | 格式与R1相同，可选增加忙信号(数据线0上传输) |
| R2   | 136bit | 根据命令返回CID/CSD寄存器值                 |
| R3   | 48bit  | ACMD41命令的响应，返回OCR寄存器的值         |
| R6   | 48bit  | 已发布的RCA响应                             |
| R7   | 48bit  | CMD8命令的响应                              |

SD卡有多种命令和响应，发送命令时主机只能通过CMD引脚发送给SD卡，串行逐位发送时先发送最高位(MSB)，然后是次高位这样类推。

### 卡状态

卡状态分别存放在下面两个区域：

卡状态（Card Status），存放在一个 32 位状态寄存器，在卡响应主机命令 时作为数据传送给主机。

SD 状态（SD_Status），当主机使用 SD_STATUS（ACMD13）命令时，512 位以一个数据块的方式发送给主机。SD_STATUS 还包括了和 BUS_WIDTH、安 全相关位和扩展位等的扩展状态位。 

## SPI Mode

SD卡默认SD模式，可以通过发送RESTE命令（CMD0） 时，拉低CS进入SPI模式。

| mirco SD |        |      | SPI Mode |                 |
| -------- | ------ | ---- | -------- | --------------- |
| 引脚编号 | 引脚名 | 模块 | 引脚类型 | 功能描述        |
| 1        | RSV    |      |          |                 |
| 2        | CS     | 1    | I        | 片选 低电平有效 |
| 3        | DI     | 3    | I        | 数据输入        |
| 4        | VDD    | 5    | S        | DC 电源正极     |
| 5        | SCLK   | 2    | I        | 时钟            |
| 6        | VSS    | 6    | S        | 电源地          |
| 7        | DO     | 4    | O/PP     | 数据输出        |
| 8        | RSV    |      |          |                 |

> S:电源			I：输入			O：推挽输出			PP：推挽

```c
#include "stm32f10x.h"
#include "delay.h"
#include <stdio.h>
#include <stdlib.h>

#include "sd.h"
#include "usart.h"

/* Private macro -------------------------------------------------------------*/
/* Private variables ---------------------------------------------------------*/
USART_InitTypeDef USART_InitStructure;
GPIO_InitTypeDef GPIO_InitStructure;

/* Private function prototypes -----------------------------------------------*/

#ifdef __GNUC__
  /* With GCC/RAISONANCE, small printf (option LD Linker->Libraries->Small printf
     set to 'Yes') calls __io_putchar() */
  #define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
  #define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif /* __GNUC__ */
  

/**
  * @brief  Main program
  * @param  None
  * @retval None
  */
u8 SD_Buffer[512];//SD卡数据缓存区
int main(void)
{
  u8 tmp;
  u16 mm,nn;
  u32 nummber,nummber_bak;

  //设置优先级分组:抢占优先级和响应优先级各2位
  NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
  //设置向量表的位置和偏移:在FLASH中偏移为0
  NVIC_SetVectorTable(NVIC_VectTab_FLASH,0);

  //USART1初始化
  USART1_Init();


  //检测当前系统时钟SystemCoreClock
  SystemCoreClockUpdate ();



  //检测SD卡,失败则2秒后继续检测
  while(SD_Init()!=0)
  {
     printf("\r\n未检测到SD卡！");
     delay_ms(SystemCoreClock,1000);
     delay_ms(SystemCoreClock,1000);
  }

  printf("\r\n初始化SD卡成功！\r\n");

  //逻辑0扇区的物理扇区号
  nummber_bak=SD_GetLogic0();

  while(1)
  {
    RxCounter1=0;
    printf("\r\n请发送要读取的逻辑扇区号\r\n");

	do
	{  //等待接收停止100mS
	  tmp = RxCounter1;
	  delay_ms(SystemCoreClock,100);
    }while(RxCounter1==0 || tmp!=RxCounter1);

	
	//将接收到的字符串转换成数值
    RxBuffer1[RxCounter1]='\0';
    nummber=atol((const char *)RxBuffer1);

    if(SD_ReadBlock(SD_Buffer,nummber_bak+nummber,512)==0)//读指定扇区
		
    printf("\r\n第%d逻辑扇区数据:",nummber);

    for(mm=0;mm<32;mm++)
    {
      printf("\r\n%03xH  ",mm<<4);
		
      for(nn=0;nn<16;nn++)
	    printf("%02x ",SD_Buffer[(mm<<3)+nn]);
	}
    printf("\r\n");
  }





//  while (1)
//  {
//  	delay_ms(SystemCoreClock,1000);
//	printf("\r\n串口1测试程序");	   
//  }
}

/**
  * @brief  Retargets the C library printf function to the USART.
  * @param  None
  * @retval None
  */
PUTCHAR_PROTOTYPE
{
  /* Place your implementation of fputc here */
  /* e.g. write a character to the USART */
  USART_SendData(USART1, (uint8_t) ch); /*发送一个字符函数*/ 

  /* Loop until the end of transmission */
  while (USART_GetFlagStatus(USART1, USART_FLAG_TC) == RESET)/*等待发送完成*/
  {
  
  }
  return ch;
}

#ifdef  USE_FULL_ASSERT

/**
  * @brief  Reports the name of the source file and the source line number
  *         where the assert_param error has occurred.
  * @param  file: pointer to the source file name
  * @param  line: assert_param error line source number
  * @retval None
  */
void assert_failed(uint8_t* file, uint32_t line)
{ 
  /* User can add his own implementation to report the file name and line number,
     ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */

  /* Infinite loop */
  while (1)
  {
  }
}
#endif

```



```c
#include "stm32f10x.h"
#include "gpioband.h"
#include "sd.h"
#include "spi.h"
#include "delay.h" 							   

u8  SD_Type=0;//SD卡类型																		 
//---------------------------------------------------------------------------------------------------------
//从指定扇区的起始处读len(<=512)字节
//buffer:数据存储区地址
//sector:扇区地址(物理扇区）
//len:字节数
//返回值0:成功,返回非0:失败
u8 SD_ReadBlock(u8 *buffer,u32 sector,u16 len)
{
  u8 tmp;
  u16 i;

  //除SDHC外,其他卡均需扇区地址转换成字节地址
  if(SD_Type!=V2HC)
    sector = sector<<9;
  tmp=SD_SendCommand(17,sector,0,1);//读命令
  if(tmp!=0x00)
    return tmp;
  // 启动一次传输
  SD_CS=0;				  	  
  if(SD_GetResponse(0xfe))//等待SD卡发回0xfe
  {	  
    SD_CS=1;  //错误退出
    return 1;
  }
  
  i=512;
  while(i>0)
  {
    *buffer=SPI1_ReadWriteByte(0xff);
    buffer++;
	i--;
  }
  
  SPI1_ReadWriteByte(0xff);//丢弃2个CRC
  SPI1_ReadWriteByte(0xff);
  
  SD_CS=1;

  SPI1_ReadWriteByte(0xff); //再送8个时钟以保证操作完成
  
  return 0;
}
//---------------------------------------------------------------------------------------------------------
//获取逻辑0扇区的物理扇区号
u32 SD_GetLogic0(void)
{
  u32 tmp=0;
  u8 buftmp[512];

  SD_ReadBlock(buftmp,0,512);
  tmp+=buftmp[0x1c6];
  tmp+=buftmp[0x1c6+1]<<8;
  tmp+=buftmp[0x1c6+2]<<16;
  tmp+=buftmp[0x1c6+3]<<24;

  return tmp;
}
//---------------------------------------------------------------------------------------------------------
//获取根目录的物理扇区号
u32 SD_GetRoot(u32 nummber)
{
  u32 tmp=0;
  u8 buftmp[512];

  SD_ReadBlock(buftmp,nummber,512);
  
  if(buftmp[0x52]=='F' && buftmp[0x53]=='A' && buftmp[0x54]=='T' && buftmp[0x55]=='3' && buftmp[0x56]=='2') //FAT32
    tmp+=buftmp[14]+(buftmp[15]<<8)+buftmp[16]*(buftmp[36]+(buftmp[37]<<8));
  else if(buftmp[0x36]=='F' && buftmp[0x37]=='A' && buftmp[0x38]=='T' && buftmp[0x39]=='1' && buftmp[0x3a]=='6') //FAT16
    tmp+=buftmp[14]+(buftmp[15]<<8)+buftmp[16]*(buftmp[22]+(buftmp[23]<<8));

  return tmp;
}
//---------------------------------------------------------------------------------------------------------
//初始化SD卡
//初始化成功返回0,失败返回非0
u8 SD_Init(void)
{								 
  u8 tmp;
  u16 i;
  u8 buf[6];



  SPI1_GPIOInit(); //SPI1相关端口初始化
  
  SD_CS=1;	
  
  //为适应MMC卡要求时钟<400KHz
  //暂用256分频(实际281.25KHz)
  SPI1_Init(256);

  delay_ms(SystemCoreClock,100); //延时100mS等待卡上电
    
  for(i=0;i<10;i++)  //输出SD卡要求的至少74个初始化脉冲
    SPI1_ReadWriteByte(0xFF); 

  i=0;
  do
  {	//发送CMD0,进入SPI模式
    tmp = SD_SendCommand(0,0,0x95,1);
    i++;
  }while((tmp!=0x01)&&(i<200));//等待回应0x01

  if(tmp==200)
    return 1; //失败退出
  



  //获取卡的版本信息
  SD_CS=0;	
  tmp=SD_SendCommand(8, 0x1aa,0x87,0);

  if(tmp==0x05)
  {	//v1.0版和MMC
    SD_Type=V1;  //预设SDV1.0
    
    SD_CS=1;
        
    SPI1_ReadWriteByte(0xff); //增加8个时钟确保本次操作完成

	
    i=0;
    do
    {
      tmp=SD_SendCommand(55,0,0,1); //发送CMD55,应返回0x01
      if(tmp==0xff)
        return tmp;	 //返回0xff表明无卡,退出
        
      tmp=SD_SendCommand(41,0,0,1); //再发送CMD41,应返回0x00
      i++;
	  //回应正确,则卡类型预设成立
    }while((tmp!=0x00) && (i<400));
	
    if(i==400)
    {	//无回应,是MMC卡
      i=0;
            
      do
      {	//MMC卡初始化
        tmp=SD_SendCommand(1,0,0,1);
        i++;
      }while((tmp!=0x00)&& (i<400));

      if(i==400)
        return 1;   //MMC卡初始化失败
            
      SD_Type=MMC;
    }
    
	SPI1_Init(4); //SPI时钟改用4分频(18MHz)

    SPI1_ReadWriteByte(0xff);//输出8个时钟确保前次操作结束

    //禁用CRC校验
    tmp=SD_SendCommand(59,0,0x95,1);
    if(tmp!=0x00)
	  return tmp;  //错误返回
    
	//设置扇区宽度
    tmp=SD_SendCommand(16,512,0x95,1);
    if(tmp!=0x00)
	  return tmp;//错误返回
  }
  else if(tmp==0x01)
  {	//V2.0和V2.0HC版
    //忽略V2.0卡的后续4字节
    SPI1_ReadWriteByte(0xff);
    SPI1_ReadWriteByte(0xff);
    SPI1_ReadWriteByte(0xff);
    SPI1_ReadWriteByte(0xff);
        
    SD_CS=1;	  
    
	SPI1_ReadWriteByte(0xff); //增加8个时钟确保本次操作完成
        
    {	  
      i=0;
      do
      {
        tmp=SD_SendCommand(55,0,0,1);
        if(tmp!=0x01)
		  return tmp;	 //错误返回  
        
		tmp=SD_SendCommand(41,0x40000000,0,1);
        if(i>200)
		  return tmp;  //超时返回
      }while(tmp!=0);		  

      tmp=SD_SendCommand(58,0,0,0);
      if(tmp!=0x00)
      {
        SD_CS=1;  //失能SD
        return tmp;  //错误返回
      }
      
	  //接收OCR信息
	  buf[0]=SPI1_ReadWriteByte(0xff);
      buf[1]=SPI1_ReadWriteByte(0xff); 
      buf[2]=SPI1_ReadWriteByte(0xff);
      buf[3]=SPI1_ReadWriteByte(0xff);		 

      SD_CS=1;

	  SPI1_ReadWriteByte(0xff); //增加8个时钟确保本次操作完成

      if(buf[0]&0x40)
        SD_Type=V2HC;
      else 
        SD_Type=V2;	    

      SPI1_Init(4);
    }	    
  }
  
  return tmp;
}	 																			   
//---------------------------------------------------------------------------------------------------------

```

```
#include "stm32f10x.h"

vu8 TxBuffer1[255];  //串口1发送缓冲区
vu8 RxBuffer1[255];  //串口1接收缓冲区
vu8 TxCounter1 = 0;  //串口1发送计数器
vu8 RxCounter1 = 0;  //串口1接收计数器

  
void USART1_Init(void)
{
  USART_InitTypeDef USART_InitStructure;     //定义USART结构变量
  GPIO_InitTypeDef GPIO_InitStructure;		//定义GPIO结构变量
  NVIC_InitTypeDef NVIC_InitStructure;		//定义向量中断结构变量
  
  //使能A口和复用AFIO时钟
  RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA | RCC_APB2Periph_AFIO, ENABLE);
  //使能USART1时钟
  RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE); 

  //USART1的Tx口初始化:推挽输出,50MHz
  GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
  GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9;
  GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
  GPIO_Init(GPIOA, &GPIO_InitStructure);

  //USART1的Rx口初始化:浮空输入
  GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
  GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;
  GPIO_Init(GPIOA, &GPIO_InitStructure);

  //USART1参数配置
  USART_InitStructure.USART_BaudRate = 9600;                   //波特率9600
  USART_InitStructure.USART_WordLength = USART_WordLength_8b;  //数据位为8位
  USART_InitStructure.USART_StopBits = USART_StopBits_1;       //停止位为1位
  USART_InitStructure.USART_Parity = USART_Parity_No;          //无奇偶校验
  USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None; //无硬件流控
  USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;      //发送与接收
  USART_Init(USART1, &USART_InitStructure);  
  //USART1使能
  USART_Cmd(USART1, ENABLE);

  //USART1中断优先级初始化与中断使能
  NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;          //使用USART1
  NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0;  //抢占优先级0
  NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0;		  //响应优先级0
  NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;			  //使能
  NVIC_Init(&NVIC_InitStructure);
  
  //使能串口1的发送和接收中断
  USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);
  USART_ITConfig(USART1, USART_IT_TXE, ENABLE);

}

void USART1_IRQHandler(void)
{
  static u8 tmp=0;

  if(USART_GetITStatus(USART1, USART_IT_RXNE) != RESET)
  {	//接收中断处理
    if(RxCounter1 < sizeof(RxBuffer1)-1)
    { //未超出数组RxBuffer1最大容量则将接收到的数据保存到数组RxBuffer1
      RxBuffer1[RxCounter1++] = USART_ReceiveData(USART1);
    }
  }
  
  if(USART_GetITStatus(USART1, USART_IT_TXE) != RESET)
  { //发送中断处理  
    if((TxCounter1 > 0) && (tmp < TxCounter1))
	{ //待发送数未完则送出
	  USART_SendData(USART1, TxBuffer1[tmp++]);
    }    
    else
    { //已发送完毕则关闭发送
      TxCounter1=0;
	  tmp=0;
      USART_ITConfig(USART1, USART_IT_TXE, DISABLE);
    }
  }
}

```

```

```

## SD模式F4

### 时钟

SDIO总共有3个时钟，分别是：

1. 卡时钟（SDIO_CK）：每个时钟周期在命令和数据线上传输1位命令或数据。
   1. 对于多媒体卡V3.31协议，时钟频率可以在0MHz至20MHz间变化；
   2. 对于多媒体卡V4.0/4.2协议，时钟频率可以在0MHz至48MHz间变化；
   3. 对于SD或SDI/O卡，时钟频率可以在0MHz至25MHz间变化。
2. SDIO适配器时钟（SDIOCLK）：该时钟用于驱动SDIO适配器，来自OLL48CK，其频率一般为48Mhz，并用于产生SDIO_CK时钟。
3. APB2总线接口时钟（PCLK2）：该时钟用于驱动SDIO的APB2总线接口，其频率为HCLK/2，一般为84Mhz。

前面提到，我们的SD卡时钟（SDIO_CK），根据卡的不同，可能有好几个区间，这就涉及到时钟频率的设置，SDIO_CK与SDIOCLK的关系为：SDIO_CK=SDIOCLK/(2+CLKDIV)

其中，SDIOCLK为PLL48CK，一般是48Mhz，而CLKDIV则是分配系数，可以通过SDIO的SDIO_CLKCR寄存器进行设置（确保SDIO_CK不超过卡的最大操作频率）。

这里要提醒大家，在SD卡刚刚初始化的时候，其时钟频率（SDIO_CK）是不能超过400Khz的，否则可能无法完成初始化。在初始化以后，就可以设置时钟频率到最大了（但不可超过SD卡的最大操作时钟频率）。

### 寄存器V4805页

**SDIO电源控制寄存器（SDIO_POWER）**

我们要启用SDIO，第一步就是要设置该寄存器最低2个位均为1，让SDIO上电，开启卡时钟。

**SDIO时钟控制寄存器（SDIO_CLKCR）**

SDIO时钟控制寄存器（SDIO_CLKCR），该寄存器主要用于设置SDIO_CK的分配系数，开关等，并可以设置SDIO的数据位宽。

12-11位：WIDBUS用于设置SDIO总线位宽，正常使用的时候，设置为1，即4位宽度。

10位：BYPASS用于设置分频器是否旁路，我们一般要使用分频器，所以这里设置为0，禁止旁路。

9位：

8位：CLKEN则用于设置是否使能SDIO_CK，我们设置为1。

7-0位：CLKDIV则用于控制SDIO_CK的分频，设置为1，即可得到24Mhz的SDIO_CK频率。

**SDIO参数寄存器（SDIO_ARG）**

SDIO参数寄存器（SDIO_ARG），该寄存器包含一个32位命令参数，该参数作为命令消息的一部分发送到卡，如果命令包含参数，则在将命令写入到命令寄存器之前，必须将参数加载到此寄存器中。

**SDIO命令响应寄存器（SDIO_RESPCMD）**

SDIO命令响应寄存器（SDIO_RESPCMD），该寄存器为32位，但只有低6位有效，比较简单，用于存储最后收到的命令响应中的命令索引。如果传输的命令响应不包含命令索引（长响应或OCR响应），则该寄存器的内容不可预知。

SDIO数据FIFO寄存器（SDIO_FIFO）

数据FIFO寄存器包括接收和发送FIFO，他们由一组连续的32个地址上的32个寄存器组成，CPU可以使用FIFO读写多个操作数。例如我们要从SD卡读数据，就必须读SDIO_FIFO寄存器，要写数据到SD卡，则要写SDIO_FIFO寄存器。SDIO将这32个地址分为16个一组，发送接收各占一半。而我们每次读写的时候，最多就是读取发送FIFO或写入接收FIFO的一半大小的数据，也就是8个字（32个字节），这里特别提醒，我们**操作SDIO_FIFO（不论读出还是写入）必须是以4字节对齐的内存进行操作，否则将导致出错！**



## SD Mode

SD卡使用9-pin接口通信，其中3根电源线、1根时钟线、1根命令线和4根数据线，具体说明如下：

- CLK：时钟线，由SDIO主机产生，即由STM32控制器输出；
- CMD：命令控制线，SDIO主机通过该线发送命令控制SD卡，如果命令要求SD卡提供应答(响应)，SD卡也是通过该线传输应答信息；
- D0-3：数据线，传输读写数据；SD卡可将D0拉低表示忙状态；
- VDD、VSS1、VSS2：电源和地信号。

| mirco SD |         |        | SD Mode  |                  |
| -------- | ------- | ------ | -------- | ---------------- |
| 引脚编号 | 引脚名  | 天空星 | 引脚类型 | 功能描述         |
| 1        | DAT2    | C10    | I/O/PP   | 数据线位2        |
| 2        | CD/DAT3 | C11    | I/O/PP   | 卡识别/数据线位3 |
| 3        | CMD     | D02    | PP       | 命令/响应        |
| 4        | VDD     |        | S        | DC电源正极       |
| 5        | CLK     | C12    | I        | 时钟             |
| 6        | VSS     |        | S        | 电源地           |
| 7        | DAT0    | C08    | I/O/PP   | 数据线位0        |
| 8        | DAT1    | C09    | I/O/PP   | 数据线位1        |
| 9        | CD      |        |          |                  |

SD卡数据包有两种格式。

**第一种方式：**常规数据(8bit宽)，它先发低字节再发高字节，而每个字节则是先发高位再发低位。

4线同步发送，每根线发送一个字节的其中两个位，数据位在四线顺序排列发送，DAT3数据线发较高位，DAT0数据线发较低位。

举例，发送B[8]：

1. 先发起始位0。
2. 遵循先发低字节再发高字节的规则选择先发送B[0]，再遵循每个字节则是先发高位再发低位的规则发送每一位：
   1. DAT3发送B[0]的第8位，DAT2发送B[0]的第7位，DAT1发送B[0]的第6位，DAT0发送B[0]的第5位；
   2. DAT3发送B[0]的第4位，DAT2发送B[0]的第3位，DAT1发送B[0]的第2位，DAT0发送B[0]的第1位；
3. 重复步骤二发送剩下的字节。
4. 发送CRC位。
5. 发送结束位1。

**第二种方式：**

另外一种数据包发送格式是宽位数据包格式，对SD卡而言宽位数据包发送方式是针对SD卡SSR(SD状态)寄存器内容发送的，SSR寄存器总共有512bit，在主机发出ACMD13命令后SD卡将SSR寄存器内容通过DAT线发送给主机。

举例，发送B[512]：

1. 先发起始位0。
2. DAT3发送B[511]，DAT2发送B[510]，DAT1发送B[509]，DAT0发送B[508]；
3. 按步骤二依次类推。
4. 发送CRC位。
5. 发送结束位1。

