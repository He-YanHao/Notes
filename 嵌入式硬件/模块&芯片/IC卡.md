# IC卡

## 基础

协议为近场通信(NEAR FIELD COMMUNICATION)技术（即 NFC技术），由免接触式射频识别(RFID)演变而来，并向下兼容RFID。

使用 SPI 通信，非接触式点对点通信。

## 数据分区

S50非接触式IC卡，分为16个扇区，每个扇区由4块（块0、块1、块2、块3）组成，每个块有 16 个字节。将16个扇区的64个块按绝对地址编号为0~63。

其中每个扇区又分为：

### 数据块

每个扇区的块0、块1、块2为**数据块**，可用于存贮数据。

- 第0扇区的块0（即绝对地址0块），它用于存放厂商代码，已经固化，不可更改。
  - 第 0～4 个字节为卡片的序列号；
  - 第 5 个字节为序列号的校验码；
  - 第 6 个字节 为卡片的容量“SIZE”字节；
  - 第 7，8 个字节为卡片的类型号字节，即 Tagtype 字节； 
  - 其他字节由厂商另加定义。

### 控制块

每个扇区的块3为**控制块**，包括了密码A、存取控制、密码B。具体结构如下：

- 控制块数据分布及作用说明：

  | 密码A                          | 存取控制                           | 密码B                                              |
| ------------------------------ | ---------------------------------- | -------------------------------------------------- |
  | A0 A1 A2 A3 A4 A5              | FF 07 80 69                        | B0 B1 B2 B3 B4 B5                                  |
  | 密码A（前6字节）               | 存取控制（中间4字节）              | 密码B（后6字节）                                   |
  | 不可被读出，特殊情况可以修改。 | 存放本扇区的四个数据块的访问条件。 | 作为密钥时不可读取，但作为存放数据使用时可以读取。 |
  
- 每个扇区的密码和存取控制都是独立的，可以根据实际需要设定各自的密码及存取控制。

- 在空卡状态下每个扇区的尾块数据(16 进制)为：“0x 000000000000 FF078069  FFFFFFFFFFFF”。空卡时的密码 A 和密码 B 均为“0xFFFFFF”，由于 A 密码不可读， 读出的数据显示为“0x000000”。在空卡默认读写权限下可以利用密码 A 对所有块进行读写操作，以及更改各块的读写权限。但不可以利用密码 B 进行读写操作（此时 B 密码可读）。

- 存取控制为4个字节，共32位，扇区中的每个块（包括数据块和控制块）的存取条件是由密码和存取控制共同决定的，在**存取控制**中每个块都有相应的**三个控制位**，

- 三个控制位以正和反两种形式存在于存取控制字节中，决定了该块的访问权限（如进行减值操作必须验证KEY A，进行加值操必须验证KEY B，等等）。

- 控制位在存储控制区域的分布：

  | 字节  | 第7位 | 第6位  | 第5位 | 第4位 | 第3位 | 第2位 | 第1位 | 第0位 |
| ----- | ----- | ------ | ----- | ----- | ----- | ----- | ----- | ----- |
  | 字节6 | C23_b | C22_b  | C21_b | C20_b | C13_b | C12_b | C11_b | C10_b |
  | 字节7 | C13   | C12    | C11   | C10   | C33_b | C32_b | C31_b | C30_b |
  | 字节8 | C33   | C32    | C31   | C30   | C23   | C22   | C21   | C20   |
  | 字节9 | 字节9 | 均保留 |       |       |       |       |       |       |
  
  > **-b 意思是反码**。
  
-  **数据块**（块0、块1、块2）的存取控制如下：

  |      |      |      |         |         |           |                             |
  | ---- | ---- | ---- | ------- | ------- | --------- | --------------------------- |
  | C1X  | C2X  | C3X  | Read    | Write   | Increment | Decrement, transfer,Restore |
  | 0    | 0    | 0    | KeyA\|B | KeyA\|B | KeyA\|B   | KeyA\|B                     |
  | 0    | 0    | 1    | KeyA\|B | Never   | Never     | KeyA\|B                     |
  | 0    | 1    | 0    | KeyA\|B | Never   | Never     | Never                       |
  | 0    | 1    | 1    | KeyB    | KeyB    | Never     | Never                       |
  | 1    | 0    | 0    | KeyA\|B | KeyB    | Never     | Never                       |
  | 1    | 0    | 1    | KeyB    | Never   | Never     | Never                       |
  | 1    | 1    | 0    | KeyA\|B | KeyB    | KeyB      | KeyA\|B                     |
  | 1    | 1    | 1    | Never   | Never   | Never     | Never                       |

  > （KeyA|B 表示密码A或密码B，Never表示任何条件下不能实现）

- **控制块**块3的存取控制与**数据块**（块0、1、2）不同，它的存取控制如下：

  |      |      |      | 密码A | 密码A   | 存取控制 | 存取控制 | 密码B   | 密码B   |
  | ---- | ---- | ---- | ----- | ------- | -------- | -------- | ------- | ------- |
  | C13  | C23  | C33  | Read  | Write   | Read     | Write    | Read    | Write   |
  | 0    | 0    | 0    | Never | KeyA\|B | KeyA\|B  | Never    | KeyA\|B | KeyA\|B |
  | 0    | 0    | 1    | Never | KeyA\|B | KeyA\|B  | KeyA\|B  | KeyA\|B | KeyA\|B |
  | 0    | 1    | 0    | Never | Never   | KeyA\|B  | Never    | KeyA\|B | Never   |
  | 0    | 1    | 1    | Never | KeyB    | KeyA\|B  | KeyB     | Never   | KeyB    |
  | 1    | 0    | 0    | Never | KeyB    | KeyA\|B  | KeyB     | Never   | KeyB    |
  | 1    | 0    | 1    | Never | Never   | KeyA\|B  | KeyB     | Never   | Never   |
  | 1    | 1    | 0    | Never | Never   | KeyA\|B  | Never    | Never   | Never   |
  | 1    | 1    | 1    | Never | Never   | KeyA\|B  | Never    | Never   | Never   |

## 代码

### .c

#### RC522初始化

```c
/******************************************************************
 * 函 数 名 称：RC522_Init
 * 函 数 说 明：IC卡感应模块配置
 * 函 数 形 参：无
 * 函 数 返 回：无
 * 备       注：
******************************************************************/
void RC522_Init(void)
{
        //开启GPIOA GPIOC 时钟
        RCC_AHB1PeriphClockCmd(RCC_CS, ENABLE);                        
        RCC_AHB1PeriphClockCmd(RCC_SCK, ENABLE);        
        RCC_AHB1PeriphClockCmd(RCC_MOSI, ENABLE);        
        RCC_AHB1PeriphClockCmd(RCC_RST, ENABLE);        
        RCC_AHB1PeriphClockCmd(RCC_MISO, ENABLE);

        //初始化GPIO工作模式 标准的SPI模式
        GPIO_InitTypeDef  GPIO_InitStructure;
        
        // CS 片选信号线 上拉推挽输出
        GPIO_InitStructure.GPIO_Pin = GPIO_CS;
        GPIO_InitStructure.GPIO_Mode = GPIO_Mode_OUT;
        GPIO_InitStructure.GPIO_OType = GPIO_OType_PP;
        GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
        GPIO_InitStructure.GPIO_PuPd = GPIO_PuPd_UP;
        GPIO_Init(PORT_CS, &GPIO_InitStructure);
        GPIO_SetBits(PORT_CS, GPIO_CS);
        
        // SCK 时钟线 上拉推挽输出
        GPIO_InitStructure.GPIO_Pin = GPIO_SCK;
        GPIO_Init(PORT_SCK, &GPIO_InitStructure);
        GPIO_SetBits(PORT_SCK, GPIO_SCK);        
        
        // MOSI 数据线 上拉推挽输出
        GPIO_InitStructure.GPIO_Pin = GPIO_MOSI;
        GPIO_Init(PORT_MOSI, &GPIO_InitStructure);
        GPIO_SetBits(PORT_MOSI, GPIO_MOSI);        
        
        // RST 重启线 上拉推挽输出
        GPIO_InitStructure.GPIO_Pin = GPIO_RST;
        GPIO_Init(PORT_RST, &GPIO_InitStructure);
        GPIO_SetBits(PORT_RST, GPIO_RST);        

        // MISO 数据线 无上下拉输入模式
        GPIO_InitStructure.GPIO_Pin = GPIO_MISO;
        GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN;
        GPIO_InitStructure.GPIO_PuPd = GPIO_PuPd_NOPULL;
        GPIO_Init(PORT_MISO, &GPIO_InitStructure);
}
```

#### 软件SPI发送一个字节数据

```c
/* 软件模拟SPI发送一个字节数据，高位先行 */
void RC522_SPI_SendByte( uint8_t byte )
{
        uint8_t n;
        for( n=0;n<8;n++ )
        {
                if( byte&0x80 )
                        RC522_MOSI_1();
                else
                        RC522_MOSI_0();
                
                delay_us(200);
                RC522_SCK_0();
                delay_us(200);
                RC522_SCK_1();
                delay_us(200);
                
                byte<<=1;
        }
}
```

#### 软件SPI读取一个字节数据

```c
/* 软件模拟SPI读取一个字节数据，先读高位 */
uint8_t RC522_SPI_ReadByte( void )
{
        uint8_t n,data;
        for( n=0;n<8;n++ )
        {
                data<<=1;
                RC522_SCK_0();
                delay_us(200);
                
                if( RC522_MISO_GET()==1 )
                        data|=0x01;
                
                delay_us(200);
                RC522_SCK_1();
                delay_us(200);
                
                
        }
        return data;
}
```

#### 读取RC522指定寄存器的值

```c
/**
  * @brief  ：读取RC522指定寄存器的值
  * @param  ：Address:寄存器的地址
  * @retval ：寄存器的值
*/
uint8_t RC522_Read_Register( uint8_t Address )
{
        uint8_t data,Addr;
        
        Addr = ( (Address<<1)&0x7E )|0x80;
        //Address左移一位，末尾用0补齐
        //0x7E = 0b 0111 1110;
        //&0x7E也就是最高位和最低位变成0
        //0x80 = 0b 1000 0000;
        //将最高位变成1
        RC522_CS_Enable();//选中RC512
        RC522_SPI_SendByte( Addr );//发送处理后的地址数据
        data = RC522_SPI_ReadByte();//读取寄存器中的值
        RC522_CS_Disable();//取消选中RC512
        
        return data;
}
```

#### 向RC522指定寄存器中写入指定的数据

```c
/**
  * @brief  ：向RC522指定寄存器中写入指定的数据
  * @param  ：Address：寄存器地址
  * @data   ：要写入寄存器的数据
  * @retval ：无
*/
void RC522_Write_Register( uint8_t Address, uint8_t data )
{
        uint8_t Addr;
        
        Addr = ( Address<<1 )&0x7E;
        //Address左移一位，末尾用0补齐
        //0x7E = 0b 0111 1110;
        //&0x7E也就是最高位和最低位变成0
        RC522_CS_Enable();//选中RC512
        RC522_SPI_SendByte( Addr );//发送处理后的地址数据
        RC522_SPI_SendByte( data );//写入数据
        RC522_CS_Disable();//取消选中RC512
}
```

#### 置位RC522指定寄存器的指定位

```c
/**
  * @brief  ：置位RC522指定寄存器的指定位
  * @param  ：Address：寄存器地址
              mask：置位值
  * @retval ：无
*/
void RC522_SetBit_Register( uint8_t Address, uint8_t mask )
{
        uint8_t temp;
        /* 获取寄存器当前值 */
        temp = RC522_Read_Register( Address );
        /* 对指定位进行置位操作后，再将值写入寄存器 */
        RC522_Write_Register( Address, temp|mask );
}
```

#### 清位RC522指定寄存器的指定位

```c
  * @brief  ：清位RC522指定寄存器的指定位
  * @param  ：Address：寄存器地址
              mask：清位值
  * @retval ：无
*/
void RC522_ClearBit_Register( uint8_t Address, uint8_t mask )
{
        uint8_t temp;
        /* 获取寄存器当前值 */
        temp = RC522_Read_Register( Address );
        /* 对指定位进行清位操作后，再将值写入寄存器 */
        RC522_Write_Register( Address, temp&(~mask) );
}
```



#### 开启天线

```c
/**
  * @brief  ：开启天线
  * @param  ：无
  * @retval ：无
*/
void RC522_Antenna_On( void )
{
        uint8_t k;
        k = RC522_Read_Register( TxControlReg );
        /* 判断天线是否开启 */
        if( !( k&0x03 ) )//通过该寄存器值判断天线是否开启，若未开启则开启
        {
            RC522_SetBit_Register( TxControlReg, 0x03 );
        }   
}
```



#### 关闭天线

```c
/**
  * @brief  ：关闭天线
  * @param  ：无
  * @retval ：无
*/
void RC522_Antenna_Off( void )
{
        /* 直接对相应位清零 */
        RC522_ClearBit_Register( TxControlReg, 0x03 );//不判断了，直接清除寄存器特定位，关闭。
}
```

#### 复位RC522

```c
/**
  * @brief  ：复位RC522
  * @param  ：无
  * @retval ：无
*/
void RC522_Rese( void )
{
        RC522_Reset_Disable();
        delay_us ( 1 );
        RC522_Reset_Enable();
        delay_us ( 1 );
        RC522_Reset_Disable();
        delay_us ( 1 );
        RC522_Write_Register( CommandReg, 0x0F );
        while( RC522_Read_Register( CommandReg )&0x10 )
                ;

        /* 缓冲一下 */
        delay_us ( 1 );
        RC522_Write_Register( ModeReg, 0x3D );       //定义发送和接收常用模式
        RC522_Write_Register( TReloadRegL, 30 );     //16位定时器低位
        RC522_Write_Register( TReloadRegH, 0 );      //16位定时器高位
        RC522_Write_Register( TModeReg, 0x8D );      //内部定时器的设置
        RC522_Write_Register( TPrescalerReg, 0x3E ); //设置定时器分频系数
        RC522_Write_Register( TxAutoReg, 0x40 );     //调制发送信号为100%ASK
}
```

#### 设置RC522的工作方式

```c
/**
  * @brief  ：设置RC522的工作方式
  * @param  ：Type：工作方式
  * @retval ：无
  M500PcdConfigISOType
*/
void RC522_Config_Type( char Type )
{
        if( Type=='A' )
        {
                RC522_ClearBit_Register( Status2Reg, 0x08 );
                RC522_Write_Register( ModeReg, 0x3D );
                RC522_Write_Register( RxSelReg, 0x86 );
                RC522_Write_Register( RFCfgReg, 0x7F );
                RC522_Write_Register( TReloadRegL, 30 );
                RC522_Write_Register( TReloadRegH, 0 );
                RC522_Write_Register( TModeReg, 0x8D );
                RC522_Write_Register( TPrescalerReg, 0x3E );
                delay_us(2);
                /* 开天线 */
                RC522_Antenna_On();
        }
}
```

#### 通过RC522和ISO14443卡通讯

```c
/**
  * @brief  ：通过RC522和ISO14443卡通讯
  * @param  ：ucCommand：RC522命令字
  *           pInData：通过RC522发送到卡片的数据
  *           ucInLenByte：发送数据的字节长度
  *           pOutData：接收到的卡片返回数据
  *           pOutLenBit：返回数据的位长度
  * @retval ：状态值MI_OK，成功
*/
char PcdComMF522 ( uint8_t ucCommand, uint8_t * pInData, uint8_t ucInLenByte, uint8_t * pOutData, uint32_t * pOutLenBit )                
{
    char cStatus = MI_ERR;
    uint8_t ucIrqEn   = 0x00;
    uint8_t ucWaitFor = 0x00;
    uint8_t ucLastBits;
    uint8_t ucN;
    uint32_t ul;
        
        
    switch ( ucCommand )
    {
       case PCD_AUTHENT:                //Mifare认证
          ucIrqEn   = 0x12;                //允许错误中断请求ErrIEn  允许空闲中断IdleIEn
          ucWaitFor = 0x10;                //认证寻卡等待时候 查询空闲中断标志位
          break;
                         
       case PCD_TRANSCEIVE:                //接收发送 发送接收
          ucIrqEn   = 0x77;                //允许TxIEn RxIEn IdleIEn LoAlertIEn ErrIEn TimerIEn
          ucWaitFor = 0x30;                //寻卡等待时候 查询接收中断标志位与 空闲中断标志位
          break;
                         
       default:
         break;
                         
    }
   
    RC522_Write_Register ( ComIEnReg, ucIrqEn | 0x80 );                //IRqInv置位管脚IRQ与Status1Reg的IRq位的值相反 
    RC522_ClearBit_Register ( ComIrqReg, 0x80 );                        //Set1该位清零时，CommIRqReg的屏蔽位清零
    RC522_Write_Register ( CommandReg, PCD_IDLE );                //写空闲命令
    RC522_SetBit_Register ( FIFOLevelReg, 0x80 );                        //置位FlushBuffer清除内部FIFO的读和写指针以及ErrReg的BufferOvfl标志位被清除
    
    for ( ul = 0; ul < ucInLenByte; ul ++ )
                  RC522_Write_Register ( FIFODataReg, pInData [ ul ] );                    //写数据进FIFOdata
                        
    RC522_Write_Register ( CommandReg, ucCommand );                                        //写命令
   
    
    if ( ucCommand == PCD_TRANSCEIVE )
                        RC522_SetBit_Register(BitFramingReg,0x80);                                  //StartSend置位启动数据发送 该位与收发命令使用时才有效
    
    ul = 1000;//根据时钟频率调整，操作M1卡最大等待时间25ms
                
    do                                                                                                                 //认证 与寻卡等待时间        
    {
         ucN = RC522_Read_Register ( ComIrqReg );                                                        //查询事件中断
         ul --;
    } while ( ( ul != 0 ) && ( ! ( ucN & 0x01 ) ) && ( ! ( ucN & ucWaitFor ) ) );                //退出条件i=0,定时器中断，与写空闲命令
                
    RC522_ClearBit_Register ( BitFramingReg, 0x80 );                                        //清理允许StartSend位
                
    if ( ul != 0 )
    {
                        if ( ! ( RC522_Read_Register ( ErrorReg ) & 0x1B ) )                        //读错误标志寄存器BufferOfI CollErr ParityErr ProtocolErr
                        {
                                cStatus = MI_OK;
                                
                                if ( ucN & ucIrqEn & 0x01 )                                        //是否发生定时器中断
                                  cStatus = MI_NOTAGERR;   
                                        
                                if ( ucCommand == PCD_TRANSCEIVE )
                                {
                                        ucN = RC522_Read_Register ( FIFOLevelReg );                        //读FIFO中保存的字节数
                                        
                                        ucLastBits = RC522_Read_Register ( ControlReg ) & 0x07;        //最后接收到得字节的有效位数
                                        
                                        if ( ucLastBits )
                                                * pOutLenBit = ( ucN - 1 ) * 8 + ucLastBits;           //N个字节数减去1（最后一个字节）+最后一位的位数 读取到的数据总位数
                                        else
                                                * pOutLenBit = ucN * 8;                                           //最后接收到的字节整个字节有效
                                        
                                        if ( ucN == 0 )                
            ucN = 1;    
                                        
                                        if ( ucN > MAXRLEN )
                                                ucN = MAXRLEN;   
                                        
                                        for ( ul = 0; ul < ucN; ul ++ )
                                          pOutData [ ul ] = RC522_Read_Register ( FIFODataReg );                                           
                                        }                                        
      }                        
                        else
                                cStatus = MI_ERR;                           
    }
   
   RC522_SetBit_Register ( ControlReg, 0x80 );           // stop timer now
   RC522_Write_Register ( CommandReg, PCD_IDLE ); 
                 
   return cStatus;                
}
```

#### 寻卡

```c
/**
  * @brief  ：寻卡
  * @param  ucReq_code，寻卡方式
  *                      = 0x52：寻感应区内所有符合14443A标准的卡
  *                     = 0x26：寻未进入休眠状态的卡
  *         pTagType，卡片类型代码
  *                   = 0x4400：Mifare_UltraLight
  *                   = 0x0400：Mifare_One(S50)
  *                   = 0x0200：Mifare_One(S70)
  *                   = 0x0800：Mifare_Pro(X))
  *                   = 0x4403：Mifare_DESFire
  * @retval ：状态值MI_OK，成功
*/
char PcdRequest ( uint8_t ucReq_code, uint8_t * pTagType )
{
   char cStatus;  
   uint8_t ucComMF522Buf [ MAXRLEN ]; 
   uint32_t ulLen;
        
   RC522_ClearBit_Register ( Status2Reg, 0x08 );        //清理指示MIFARECyptol单元接通以及所有卡的数据通信被加密的情况
   RC522_Write_Register ( BitFramingReg, 0x07 );        //        发送的最后一个字节的 七位
   RC522_SetBit_Register ( TxControlReg, 0x03 );        //TX1,TX2管脚的输出信号传递经发送调制的13.46的能量载波信号

   ucComMF522Buf [ 0 ] = ucReq_code;                //存入寻卡方式
        /* PCD_TRANSCEIVE：发送并接收数据的命令，RC522向卡片发送寻卡命令，卡片返回卡的型号代码到ucComMF522Buf中 */
   cStatus = PcdComMF522 ( PCD_TRANSCEIVE,        ucComMF522Buf, 1, ucComMF522Buf, & ulLen );        //寻卡  
  
   if ( ( cStatus == MI_OK ) && ( ulLen == 0x10 ) )        //寻卡成功返回卡类型 
   {    
                 /* 接收卡片的型号代码 */
       * pTagType = ucComMF522Buf [ 0 ];
       * ( pTagType + 1 ) = ucComMF522Buf [ 1 ];
   }
   else
     cStatus = MI_ERR;   
     return cStatus;         
}
```

#### 防冲突

```c
/**
  * @brief  ：防冲突
  * @param  ：Snr：卡片序列，4字节，会返回选中卡片的序列
  * @retval ：状态值MI_OK，成功
*/
char PcdAnticoll ( uint8_t * pSnr )
{
    char cStatus;
    uint8_t uc, ucSnr_check = 0;
    uint8_t ucComMF522Buf [ MAXRLEN ]; 
          uint32_t ulLen;
   
    RC522_ClearBit_Register ( Status2Reg, 0x08 );                //清MFCryptol On位 只有成功执行MFAuthent命令后，该位才能置位
    RC522_Write_Register ( BitFramingReg, 0x00);                //清理寄存器 停止收发
    RC522_ClearBit_Register ( CollReg, 0x80 );                        //清ValuesAfterColl所有接收的位在冲突后被清除
   
    ucComMF522Buf [ 0 ] = 0x93;        //卡片防冲突命令
    ucComMF522Buf [ 1 ] = 0x20;
   
          /* 将卡片防冲突命令通过RC522传到卡片中，返回的是被选中卡片的序列 */
    cStatus = PcdComMF522 ( PCD_TRANSCEIVE, ucComMF522Buf, 2, ucComMF522Buf, & ulLen);//与卡片通信
        
    if ( cStatus == MI_OK)                //通信成功
    {
                        for ( uc = 0; uc < 4; uc ++ )
                        {
         * ( pSnr + uc )  = ucComMF522Buf [ uc ];                        //读出UID
         ucSnr_check ^= ucComMF522Buf [ uc ];
      }
                        
      if ( ucSnr_check != ucComMF522Buf [ uc ] )
                                cStatus = MI_ERR;             
    }    
    RC522_SetBit_Register ( CollReg, 0x80 );                
    return cStatus;                
}

```

#### 用RC522计算CRC

```c
/**
 * @brief   :用RC522计算CRC16（循环冗余校验）
        * @param  ：pIndata：计算CRC16的数组
 *            ucLen：计算CRC16的数组字节长度
 *            pOutData：存放计算结果存放的首地址
  * @retval ：状态值MI_OK，成功
*/
void CalulateCRC ( uint8_t * pIndata, u8 ucLen, uint8_t * pOutData )
{
    uint8_t uc, ucN;
        
        
    RC522_ClearBit_Register(DivIrqReg,0x04);        
    RC522_Write_Register(CommandReg,PCD_IDLE);        
    RC522_SetBit_Register(FIFOLevelReg,0x80);
        
    for ( uc = 0; uc < ucLen; uc ++)
            RC522_Write_Register ( FIFODataReg, * ( pIndata + uc ) );   

    RC522_Write_Register ( CommandReg, PCD_CALCCRC );
        
    uc = 0xFF;
        
    do 
    {
        ucN = RC522_Read_Register ( DivIrqReg );
        uc --;
    } while ( ( uc != 0 ) && ! ( ucN & 0x04 ) );
                
    pOutData [ 0 ] = RC522_Read_Register ( CRCResultRegL );
    pOutData [ 1 ] = RC522_Read_Register ( CRCResultRegM );
                
}

```

#### 选定卡片

```c
/**
  * @brief   :选定卡片
  * @param  ：pSnr：卡片序列号，4字节
  * @retval ：状态值MI_OK，成功
*/
char PcdSelect ( uint8_t * pSnr )
{
    char ucN;
    uint8_t uc;
          uint8_t ucComMF522Buf [ MAXRLEN ]; 
    uint32_t  ulLen;
    /* PICC_ANTICOLL1：防冲突命令 */
    ucComMF522Buf [ 0 ] = PICC_ANTICOLL1;
    ucComMF522Buf [ 1 ] = 0x70;
    ucComMF522Buf [ 6 ] = 0;
        
    for ( uc = 0; uc < 4; uc ++ )
    {
            ucComMF522Buf [ uc + 2 ] = * ( pSnr + uc );
            ucComMF522Buf [ 6 ] ^= * ( pSnr + uc );
    }
                
    CalulateCRC ( ucComMF522Buf, 7, & ucComMF522Buf [ 7 ] );
  
    RC522_ClearBit_Register ( Status2Reg, 0x08 );

    ucN = PcdComMF522 ( PCD_TRANSCEIVE, ucComMF522Buf, 9, ucComMF522Buf, & ulLen );
    
    if ( ( ucN == MI_OK ) && ( ulLen == 0x18 ) )
      ucN = MI_OK;  
    else
      ucN = MI_ERR;    

    return ucN;
                
}

```

#### 校验卡片密码

```c
/**
  * @brief   :校验卡片密码
  * @param  ：ucAuth_mode：密码验证模式
  *                     = 0x60，验证A密钥
  *                     = 0x61，验证B密钥
  *           ucAddr：块地址
  *           pKey：密码
  *           pSnr：卡片序列号，4字节
  * @retval ：状态值MI_OK，成功
*/
char PcdAuthState ( uint8_t ucAuth_mode, uint8_t ucAddr, uint8_t * pKey, uint8_t * pSnr )
{
    char cStatus;
          uint8_t uc, ucComMF522Buf [ MAXRLEN ];
    uint32_t ulLen; 
        
    ucComMF522Buf [ 0 ] = ucAuth_mode;
    ucComMF522Buf [ 1 ] = ucAddr;
          /* 前俩字节存储验证模式和块地址，2~8字节存储密码（6个字节），8~14字节存储序列号 */
    for ( uc = 0; uc < 6; uc ++ )
            ucComMF522Buf [ uc + 2 ] = * ( pKey + uc );   
        
    for ( uc = 0; uc < 6; uc ++ )
            ucComMF522Buf [ uc + 8 ] = * ( pSnr + uc );   
    /* 进行冗余校验，14~16俩个字节存储校验结果 */
    cStatus = PcdComMF522 ( PCD_AUTHENT, ucComMF522Buf, 12, ucComMF522Buf, & ulLen );
          /* 判断验证是否成功 */
    if ( ( cStatus != MI_OK ) || ( ! ( RC522_Read_Register ( Status2Reg ) & 0x08 ) ) )
      cStatus = MI_ERR;   
                
    return cStatus;
                
}

```

#### 在M1卡的指定块地址写入指定数据

```c
/**
  * @brief   :在M1卡的指定块地址写入指定数据
  * @param  ：ucAddr：块地址
  *           pData：写入的数据，16字节
  * @retval ：状态值MI_OK，成功
*/
char PcdWrite ( uint8_t ucAddr, uint8_t * pData )
{
    char cStatus;
          uint8_t uc, ucComMF522Buf [ MAXRLEN ];
    uint32_t ulLen;
   
    ucComMF522Buf [ 0 ] = PICC_WRITE;//写块命令
    ucComMF522Buf [ 1 ] = ucAddr;//写块地址
        
          /* 进行循环冗余校验，将结果存储在& ucComMF522Buf [ 2 ] */
    CalulateCRC ( ucComMF522Buf, 2, & ucComMF522Buf [ 2 ] );
    
        /* PCD_TRANSCEIVE:发送并接收数据命令，通过RC522向卡片发送写块命令 */
    cStatus = PcdComMF522 ( PCD_TRANSCEIVE, ucComMF522Buf, 4, ucComMF522Buf, & ulLen );

                /* 通过卡片返回的信息判断，RC522是否与卡片正常通信 */
    if ( ( cStatus != MI_OK ) || ( ulLen != 4 ) || ( ( ucComMF522Buf [ 0 ] & 0x0F ) != 0x0A ) )
      cStatus = MI_ERR;   
        
    if ( cStatus == MI_OK )
    {
                        //memcpy(ucComMF522Buf, pData, 16);
                        /* 将要写入的16字节的数据，传入ucComMF522Buf数组中 */
      for ( uc = 0; uc < 16; uc ++ )
                          ucComMF522Buf [ uc ] = * ( pData + uc );  
                        /* 冗余校验 */
      CalulateCRC ( ucComMF522Buf, 16, & ucComMF522Buf [ 16 ] );
      /* 通过RC522，将16字节数据包括2字节校验结果写入卡片中 */
      cStatus = PcdComMF522 ( PCD_TRANSCEIVE, ucComMF522Buf, 18, ucComMF522Buf, & ulLen );
                        /* 判断写地址是否成功 */
                        if ( ( cStatus != MI_OK ) || ( ulLen != 4 ) || ( ( ucComMF522Buf [ 0 ] & 0x0F ) != 0x0A ) )
        cStatus = MI_ERR;                           
    } 
    return cStatus;        
}

```

#### 读取M1卡的指定块地址的数据

```c
/**
  * @brief   :读取M1卡的指定块地址的数据
  * @param  ：ucAddr：块地址
  *           pData：读出的数据，16字节
  * @retval ：状态值MI_OK，成功
*/
char PcdRead ( uint8_t ucAddr, uint8_t * pData )
{
    char cStatus;
          uint8_t uc, ucComMF522Buf [ MAXRLEN ]; 
    uint32_t ulLen;

    ucComMF522Buf [ 0 ] = PICC_READ;
    ucComMF522Buf [ 1 ] = ucAddr;
          /* 冗余校验 */
    CalulateCRC ( ucComMF522Buf, 2, & ucComMF522Buf [ 2 ] );
    /* 通过RC522将命令传给卡片 */
    cStatus = PcdComMF522 ( PCD_TRANSCEIVE, ucComMF522Buf, 4, ucComMF522Buf, & ulLen );
        
          /* 如果传输正常，将读取到的数据传入pData中 */
    if ( ( cStatus == MI_OK ) && ( ulLen == 0x90 ) )
    {
                        for ( uc = 0; uc < 16; uc ++ )
        * ( pData + uc ) = ucComMF522Buf [ uc ];   
    }
    else
      cStatus = MI_ERR;   
        
    return cStatus;

}

```

#### 让卡片进入休眠模式

```c
/**
  * @brief   :让卡片进入休眠模式
  * @param  ：无
  * @retval ：状态值MI_OK，成功
*/
char PcdHalt( void )
{
        uint8_t ucComMF522Buf [ MAXRLEN ]; 
        uint32_t  ulLen;

  ucComMF522Buf [ 0 ] = PICC_HALT;
  ucComMF522Buf [ 1 ] = 0;
        
  CalulateCRC ( ucComMF522Buf, 2, & ucComMF522Buf [ 2 ] );
         PcdComMF522 ( PCD_TRANSCEIVE, ucComMF522Buf, 4, ucComMF522Buf, & ulLen );

  return MI_OK;
        
}
```



### .h

```c
#ifndef _BSP_RC522_H
#define _BSP_RC522_H

#include "stm32f4xx.h"

#ifndef u8
#define u8 uint8_t 
#endif

#ifndef u16
#define u16 uint16_t 
#endif

#ifndef u32
#define u32 uint32_t 
#endif

//SDA
#define RCC_CS          RCC_AHB1Periph_GPIOA
#define PORT_CS         GPIOA
#define GPIO_CS         GPIO_Pin_5

//SCK
#define RCC_SCK         RCC_AHB1Periph_GPIOA
#define PORT_SCK        GPIOA
#define GPIO_SCK        GPIO_Pin_7
//MOSI
#define RCC_MOSI         RCC_AHB1Periph_GPIOC
#define PORT_MOSI        GPIOC
#define GPIO_MOSI        GPIO_Pin_4

//RST
#define RCC_RST          RCC_AHB1Periph_GPIOA
#define PORT_RST         GPIOA
#define GPIO_RST         GPIO_Pin_6

//MISO
#define RCC_MISO         RCC_AHB1Periph_GPIOA
#define PORT_MISO        GPIOA
#define GPIO_MISO        GPIO_Pin_4



/* IO口操作函数 */
#define   RC522_CS_Enable()         GPIO_WriteBit(PORT_CS, GPIO_CS,Bit_RESET)
#define   RC522_CS_Disable()        GPIO_WriteBit(PORT_CS, GPIO_CS,Bit_SET)

#define   RC522_Reset_Enable()      GPIO_WriteBit(PORT_RST, GPIO_RST,Bit_RESET)
#define   RC522_Reset_Disable()     GPIO_WriteBit(PORT_RST, GPIO_RST,Bit_SET)

#define   RC522_SCK_0()             GPIO_WriteBit(PORT_SCK, GPIO_SCK,Bit_RESET)
#define   RC522_SCK_1()             GPIO_WriteBit(PORT_SCK, GPIO_SCK,Bit_SET)

#define   RC522_MOSI_0()            GPIO_WriteBit(PORT_MOSI, GPIO_MOSI,Bit_RESET)
#define   RC522_MOSI_1()            GPIO_WriteBit(PORT_MOSI, GPIO_MOSI,Bit_SET)

#define   RC522_MISO_GET()          GPIO_ReadInputDataBit(PORT_MISO, GPIO_MISO)   


//RC522命令字
#define PCD_IDLE              0x00               //取消当前命令
#define PCD_AUTHENT           0x0E               //验证密钥
#define PCD_RECEIVE           0x08               //接收数据
#define PCD_TRANSMIT          0x04               //发送数据
#define PCD_TRANSCEIVE        0x0C               //发送并接收数据
#define PCD_RESETPHASE        0x0F               //复位
#define PCD_CALCCRC           0x03               //CRC计算

//Mifare_One卡片命令字
#define PICC_REQIDL           0x26               //寻天线区内未进入休眠状态
#define PICC_REQALL           0x52               //寻天线区内全部卡
#define PICC_ANTICOLL1        0x93               //防冲撞
#define PICC_ANTICOLL2        0x95               //防冲撞
#define PICC_AUTHENT1A        0x60               //验证A密钥
#define PICC_AUTHENT1B        0x61               //验证B密钥
#define PICC_READ             0x30               //读块
#define PICC_WRITE            0xA0               //写块
#define PICC_DECREMENT        0xC0               //扣款
#define PICC_INCREMENT        0xC1               //充值
#define PICC_RESTORE          0xC2               //调块数据到缓冲区
#define PICC_TRANSFER         0xB0               //保存缓冲区中数据
#define PICC_HALT             0x50               //休眠

/* RC522  FIFO长度定义 */
#define DEF_FIFO_LENGTH       64                 //FIFO size=64byte
#define MAXRLEN  18


/* RC522寄存器定义 */
// PAGE 0
#define     RFU00                 0x00    //保留
#define     CommandReg            0x01    //启动和停止命令的执行
#define     ComIEnReg             0x02    //中断请求传递的使能（Enable/Disable）
#define     DivlEnReg             0x03    //中断请求传递的使能
#define     ComIrqReg             0x04    //包含中断请求标志
#define     DivIrqReg             0x05    //包含中断请求标志
#define     ErrorReg              0x06    //错误标志，指示执行的上个命令的错误状态
#define     Status1Reg            0x07    //包含通信的状态标识
#define     Status2Reg            0x08    //包含接收器和发送器的状态标志
#define     FIFODataReg           0x09    //64字节FIFO缓冲区的输入和输出
#define     FIFOLevelReg          0x0A    //指示FIFO中存储的字节数
#define     WaterLevelReg         0x0B    //定义FIFO下溢和上溢报警的FIFO深度
#define     ControlReg            0x0C    //不同的控制寄存器
#define     BitFramingReg         0x0D    //面向位的帧的调节
#define     CollReg               0x0E    //RF接口上检测到的第一个位冲突的位的位置
#define     RFU0F                 0x0F    //保留
// PAGE 1     
#define     RFU10                 0x10    //保留
#define     ModeReg               0x11    //定义发送和接收的常用模式
#define     TxModeReg             0x12    //定义发送过程的数据传输速率
#define     RxModeReg             0x13    //定义接收过程中的数据传输速率
#define     TxControlReg          0x14    //控制天线驱动器管教TX1和TX2的逻辑特性
#define     TxAutoReg             0x15    //控制天线驱动器的设置
#define     TxSelReg              0x16    //选择天线驱动器的内部源
#define     RxSelReg              0x17    //选择内部的接收器设置
#define     RxThresholdReg        0x18    //选择位译码器的阈值
#define     DemodReg              0x19    //定义解调器的设置
#define     RFU1A                 0x1A    //保留
#define     RFU1B                 0x1B    //保留
#define     MifareReg             0x1C    //控制ISO 14443/MIFARE模式中106kbit/s的通信
#define     RFU1D                 0x1D    //保留
#define     RFU1E                 0x1E    //保留
#define     SerialSpeedReg        0x1F    //选择串行UART接口的速率
// PAGE 2    
#define     RFU20                 0x20    //保留
#define     CRCResultRegM         0x21    //显示CRC计算的实际MSB值
#define     CRCResultRegL         0x22    //显示CRC计算的实际LSB值
#define     RFU23                 0x23    //保留
#define     ModWidthReg           0x24    //控制ModWidth的设置
#define     RFU25                 0x25    //保留
#define     RFCfgReg              0x26    //配置接收器增益
#define     GsNReg                0x27    //选择天线驱动器管脚（TX1和TX2）的调制电导
#define     CWGsCfgReg            0x28    //选择天线驱动器管脚的调制电导
#define     ModGsCfgReg           0x29    //选择天线驱动器管脚的调制电导
#define     TModeReg              0x2A    //定义内部定时器的设置
#define     TPrescalerReg         0x2B    //定义内部定时器的设置
#define     TReloadRegH           0x2C    //描述16位长的定时器重装值
#define     TReloadRegL           0x2D    //描述16位长的定时器重装值
#define     TCounterValueRegH     0x2E   
#define     TCounterValueRegL     0x2F    //显示16位长的实际定时器值
// PAGE 3      
#define     RFU30                 0x30    //保留
#define     TestSel1Reg           0x31    //常用测试信号配置
#define     TestSel2Reg           0x32    //常用测试信号配置和PRBS控制 
#define     TestPinEnReg          0x33    //D1-D7输出驱动器的使能管脚（仅用于串行接口）
#define     TestPinValueReg       0x34    //定义D1-D7用作I/O总线时的值
#define     TestBusReg            0x35    //显示内部测试总线的状态
#define     AutoTestReg           0x36    //控制数字自测试
#define     VersionReg            0x37    //显示版本
#define     AnalogTestReg         0x38    //控制管脚AUX1和AUX2
#define     TestDAC1Reg           0x39    //定义TestDAC1的测试值
#define     TestDAC2Reg           0x3A    //定义TestDAC2的测试值
#define     TestADCReg            0x3B    //显示ADCI和Q通道的实际值
#define     RFU3C                 0x3C    //保留
#define     RFU3D                 0x3D    //保留
#define     RFU3E                 0x3E    //保留
#define     RFU3F                                          0x3F    //保留

/* 和RC522通信时返回的错误代码 */
#define         MI_OK                 0x26
#define         MI_NOTAGERR           0xcc
#define         MI_ERR                0xbb

/**********************************************************************/

void RC522_Init(void);/* IO口初始化 */
////////////////软件模拟SPI与RC522通信///////////////////////////////////////////
void RC522_SPI_SendByte( uint8_t byte );/* 软件模拟SPI发送一个字节数据，高位先行 */
uint8_t RC522_SPI_ReadByte( void );/* 软件模拟SPI读取一个字节数据，先读高位 */
uint8_t RC522_Read_Register( uint8_t Address );//读取RC522指定寄存器的值
void RC522_Write_Register( uint8_t Address, uint8_t data );//向RC522指定寄存器中写入指定的数据
void RC522_SetBit_Register( uint8_t Address, uint8_t mask );//置位RC522指定寄存器的指定位
void RC522_ClearBit_Register( uint8_t Address, uint8_t mask );//清位RC522指定寄存器的指定位
/////////////////////STM32对RC522的基础通信///////////////////////////////////
void RC522_Antenna_On( void );//开启天线
void RC522_Antenna_Off( void );//关闭天线
void RC522_Rese( void );//复位RC522
void RC522_Config_Type( char Type );//设置RC522的工作方式
/////////////////////////STM32控制RC522与M1的通信///////////////////////////////////////
char PcdComMF522 ( uint8_t ucCommand, uint8_t * pInData, uint8_t ucInLenByte, uint8_t * pOutData, uint32_t * pOutLenBit );//通过RC522和ISO14443卡通讯
char PcdRequest ( uint8_t ucReq_code, uint8_t * pTagType );//寻卡
char PcdAnticoll ( uint8_t * pSnr );//防冲突
void CalulateCRC ( uint8_t * pIndata, u8 ucLen, uint8_t * pOutData );//用RC522计算CRC16（循环冗余校验）
char PcdSelect ( uint8_t * pSnr );//选定卡片
char PcdAuthState ( uint8_t ucAuth_mode, uint8_t ucAddr, uint8_t * pKey, uint8_t * pSnr );//校验卡片密码
char PcdWrite ( uint8_t ucAddr, uint8_t * pData );//在M1卡的指定块地址写入指定数据
char PcdRead ( uint8_t ucAddr, uint8_t * pData );//读取M1卡的指定块地址的数据
char PcdHalt( void );//让卡片进入休眠模式

#endif  
```



### .main

```c
#include "board.h"
#include "bsp_uart.h"
#include "bsp_rc522.h"
#include <stdio.h>
#include "string.h"


/* 卡的ID存储，32位,4字节 */
u8 ucArray_ID [ 4 ];
uint8_t ucStatusReturn;    //返回状态 

int main(void)
{
    int i = 0;
    
    uint8_t read_write_data[16]={0};//读写数据缓存
    uint8_t card_KEY[6] ={0xff,0xff,0xff,0xff,0xff,0xff};//默认密码
    
    board_init();
    uart1_init(115200U);

    printf ("Init....\r\n");
    RC522_Init( );//IC卡IO口初始化 
    RC522_Rese( );//复位RC522    
    printf ("Start!\r\n");
        
    while(1) 
    {

        /* 寻卡（方式：范围内全部），第一次寻卡失败后再进行一次，寻卡成功时卡片序列传入数组ucArray_ID中 */
        if ( ( ucStatusReturn = PcdRequest ( PICC_REQALL, ucArray_ID ) ) != MI_OK )
        {  
                        ucStatusReturn = PcdRequest ( PICC_REQALL, ucArray_ID );
        }

        if ( ucStatusReturn == MI_OK  )
        {
                /* 防冲突操作，被选中的卡片序列传入数组ucArray_ID中 */
                if ( PcdAnticoll ( ucArray_ID ) == MI_OK )
                {
                        //输出卡ID                
                        printf("ID: %X %X %X %X\r\n", ucArray_ID [ 0 ], ucArray_ID [ 1 ], ucArray_ID [ 2 ], ucArray_ID [ 3 ]);

                         //选卡
                        if( PcdSelect(ucArray_ID) != MI_OK )
                        {    printf("PcdSelect failure\r\n");         }

                        //校验卡片密码
                        //数据块6的密码A进行校验（所有密码默认为16个字节的0xff）
                        if( PcdAuthState(PICC_AUTHENT1B, 6, card_KEY,  ucArray_ID) != MI_OK )
                        {    printf("PcdAuthState failure\r\n");      }

                        //往数据块4写入数据read_write_data
                        read_write_data[0] = 0xaa;//将read_write_data的第一位数据改为0xaa
                        if( PcdWrite(4,read_write_data) != MI_OK )
                        {    printf("PcdWrite failure\r\n");          }

                        //将read_write_data的16位数据，填充为0（清除数据的意思）
                        memset(read_write_data,0,16);
                        delay_us(8);

                        //读取数据块4的数据
                        if( PcdRead(4,read_write_data) != MI_OK )
                        {    printf("PcdRead failure\r\n");           }

                        //输出读出的数据
                        for( i = 0; i < 16; i++ )
                        {
                                        printf("%x ",read_write_data[i]);
                        }
                        printf("\r\n");
                }
        }        
    }
}
```

