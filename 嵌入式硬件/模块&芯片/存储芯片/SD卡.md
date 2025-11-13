# SD卡

## 概述

### SD卡分类

- 标准的SD卡，这种卡比较大，在有些相机或者PC电脑上会使用；
- 第二种是miniSD，比较少见，不作详述；
- 最后一种是叫TF卡，也称mirco SD，这种卡比较小，是最常接触的那种，手机里面使用的内存卡就是这种卡。

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

-   **存储单元**是存储数据部件，存储单元通过**存储单元接口**与**卡控制单元**进行数据传输；
-   **电源检测单元**保证SD卡工作在合适的电压下，如出现掉电或上状态时，它会使控制单元和存储单元接口复位；
-   **卡及接口控制单元**控制SD卡的运行状态，它包括有8个寄存器；
-   **接口驱动器**控制SD卡引脚的输入输出。

## 命令及相关

### SD卡寄存器

SD卡总共有8个寄存器，用于设定或表示SD卡信息。

| 名称 | bit宽度 |                     描述                      | 作用                                                     | 访问命令         |      |
| :--: | :-----: | :-------------------------------------------: | -------------------------------------------------------- | ---------------- | ---- |
| CID  |   128   |     卡识别号(Card identification number)      | 用来识别的卡的个体号码(唯一的)                           | CMD2, CMD10      |      |
| RCA  |   16    |        相对地址(Relative card address)        | 卡的本地系统地址，初始化时，动态地由卡建议，主机核准。   | CMD3             |      |
| DSR  |   16    |      驱动级寄存器(Driver Stage Register)      | 主要用于配置卡的输出驱动能力，但在实际应用中很少使用。   | CMD4             |      |
| CSD  |   128   |       卡的特定数据(Card Specific Data)        | 卡的操作条件信息                                         | CMD9, CMD27      |      |
| SCR  |   64    |    SD配置寄存器(SD Configuration Register)    | 包含SD卡的特殊功能信息，如总线宽度支持、安全特性等。     | ACMD51           |      |
| OCR  |   32    | 操作条件寄存器(Operation conditions register) | 包含了卡的操作电压范围、上电状态和卡容量类型等关键信息。 | ACMD41, CMD58    |      |
| SSR  |   512   |              SD状态(SD Status):               | SD卡专有状态：性能、安全状态、擦除状态等。               | ACMD13           |      |
|  CS  |   32    |             卡状态(Card Status):              | 命令执行状态、错误标志、当前状态机状态等。               | 所有命令的R1响应 |      |

这些寄存器只能通过对应的命令访问(CMD0-CMD63)，对SD卡进行控制操作并不是像操作控制器GPIO相关寄存器那样一次读写一个寄存器的，它是通过命令来控制。

SDIO定义了64个命令，每个命令都有特殊意义，可以实现某一特定功能，SD卡接收到命令后，根据命令要求对SD卡内部寄存器进行修改，程序控制中只需要发送组合命令就可以实现SD卡的控制以及读写操作。

>   并非每个型号的SD卡都支持所以64个命令，有些命令仅存在于特定型号的SD卡。

#### 操作条件OCR寄存器

包含了卡的操作电压范围、上电状态和卡容量类型等关键信息。

##### OCR寄存器结构

OCR是一个**32位寄存器**，具体位定义如下：

```text
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9  8  7  6  5  4  3  2  1  0
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
└──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┘

位31: 卡上电状态 (Busy)
位30: 卡容量状态 (CCS)
位23-15: 电压窗口
位7-0: 保留
```



##### 详细位说明

**位31 - 卡上电状态 (Card Power Up Status)**

-   **功能**：指示卡是否完成上电初始化过程
-   **状态**：
    -   `0` = 卡正在初始化（忙状态）
    -   `1` = 卡初始化完成，可以接受命令
-   **重要性**：主机必须等待此位变为1才能继续后续操作

**位30 - 卡容量状态 (Card Capacity Status)**

-   **功能**：指示SD卡容量类型
-   **状态**：
    -   `0` = 标准容量SD卡 (SDSC, ≤2GB)，使用字节寻址
    -   `1` = 高容量SD卡 (SDHC, 2GB-32GB) 或扩展容量卡 (SDXC, 32GB-2TB)，使用块寻址
-   **影响**：决定了后续读写操作的寻址方式

**位23-15 - 电压窗口 (Voltage Window)**

每个位代表一个特定的电压范围：

| 位位置 | 电压范围 | 说明     |
| :----- | :------- | :------- |
| 位15   | 2.0-2.1V | 很少使用 |
| 位16   | 2.1-2.2V | 很少使用 |
| 位17   | 2.2-2.3V | 很少使用 |
| 位18   | 2.3-2.4V | 很少使用 |
| 位19   | 2.4-2.5V | 很少使用 |
| 位20   | 2.5-2.6V | 很少使用 |
| 位21   | 2.6-2.7V | 很少使用 |
| 位22   | 2.7-2.8V | 常用范围 |
| 位23   | 2.8-2.9V | 常用范围 |
| 位24   | 2.9-3.0V | 常用范围 |
| 位25   | 3.0-3.1V | 常用范围 |
| 位26   | 3.1-3.2V | 常用范围 |
| 位27   | 3.2-3.3V | 常用范围 |
| 位28   | 3.3-3.4V | 常用范围 |
| 位29   | 3.4-3.5V | 常用范围 |
| 位30   | 3.5-3.6V | 常用范围 |

### 命令类型与格式

SD命令由主机发出，以广播命令和寻址命令为例，广播命令是针对与SD主机总线连接的所有从设备发送的，寻址命令是指定某个地址设备进行命令传输。

SD命令有4种类型：

- 无响应广播命令(bc)，发送到所有卡，不返回任务响应；
- 带响应广播命令(bcr)，发送到所有卡，同时接收来自所有卡响应；
- 寻址命令(ac)，发送到选定卡，DAT线无数据传输；
- 寻址数据传输命令(adtc)，发送到选定卡，DAT线有数据传输。

SD卡的命令固定为48位，由6个字节组成。

```
0X XX XX XX XX XX XX;
0B 0000 0000  0000 0000  0000 0000  0000 0000  0000 0000  0000 0000;
```

- 字节1：
  - 字节1的最高位固定为0。
  - 第二高位为1时表示命令，方向为主机传输到SD卡，该位为0时表示响应，方向为SD卡传输到主机。
  - 低6位为命令号，共有64个命令(代号：CMD0~CMD63)，。
- 字节2~5为命令参数，有些命令是没有参数的。
- 字节6：
  - 字节6的高七位为CRC校验值，用于验证命令传输内容正确性，如果发生外部干扰导致传输数据个别位状态改变将导致校准失败，也意味着命令传输失败，SD卡不执行命令。
  - 最低位恒定为1。

> - 例如CMD16: 0B 0101 0000  0000 0000  0000 0000  0000 0000  0000 0000  0000 0000;



### 参数



### 响应

SD卡共有七个响应类型（R1~R7）。

在主机发送有响应的命令后，SD卡都会给出相对应的应答，以告知主机该命令的执行情况，或者返回主机需要获取的数据。

与命令一样，SD卡的响应也是通过CMD线连续传输的。

SD的响应大体分为短响应48bit和长响应136bit，每个响应也有规定好的格式。R1、R1b、R3、R6和R7属于短响应，而R2属于长响应。

| 响应 | 长度   | 描述                                                         |
| ---- | ------ | ------------------------------------------------------------ |
| R1   | 48bit  | 正常响应，告知卡的状态。                                     |
| R1b  | 48bit  | 格式与R1相同，卡可能通过拉低DAT0线表示忙状态，直到操作完成。 |
| R2   | 136bit | 用于响应CMD2（请求CID）或CMD10（请求CSD），返回CID或CSD寄存器的值。 |
| R3   | 48bit  | 用于ACMD41命令（SD卡初始化命令），返回OCR（Operating Conditions Register）寄存器的值。 |
| R6   | 48bit  | 用于响应CMD3（发布相对卡地址RCA），返回新分配的RCA和卡状态。 |
| R7   | 48bit  | 用于响应CMD8命令（发送接口条件），返回电压信息和检查模式。   |

SD卡有多种命令和响应，发送命令时主机只能通过CMD引脚发送给SD卡，串行逐位发送时先发送最高位(MSB)，然后是次高位这样类推。

### 具体命令

| 命令         | 参数             | 响应         | 描述                                |
| ------------ | ---------------- | ------------ | ----------------------------------- |
| CMD0(0X00)   | NONE             | R1           | 复位SD卡                            |
| CMD1(0X)     | NONE             | R1           | MMC卡的初始化命令                   |
| CMD2(0X02)   | NONE             | R2           | 读取SD卡的CID寄存器值               |
| CMD3(0X03)   | NONE             | R6           | 要求SD卡发布新的相对地址            |
| CMD7(0X07)   | RCA              | R1b          | 选中SD卡                            |
| CMD8(0X08)   | VHS+Checkpattern | 检查接口条件 | 发送接口状态命令                    |
| CMD9(0X09)   | RCA              | R2           | 获得选定卡的CSD寄存器的内容         |
| CMD13(0x0D)  | RCA              | R1           | 被选中的卡返回其状态                |
| CMD16(0X10)  | 块大小           | R1           | 设置块大小（字节数）                |
| CMD17(0X11)  | 地址             | R1           | 读取一个块的数据                    |
| CMD24(0X18)  | 地址             | R1           | 写入一个块的数据                    |
| CMD55(0X37)  | NONE             | R1           | 告诉SD卡，下一个是特定应用命令      |
| ACMD41(0X29) | HCS+VDD电压      | R3           | 主机发送容量支持信息和OCR寄存器内容 |

上表中，大部分的命令是初始化的时候用的。



## 初始化流程

```
CMD0 → CMD8 → CMD55 → ACMD41循环 → CMD2 → CMD3 → CMD7 → ACMD6 → 高速模式 → CMD16
CMD0 → CMD8 → CMD55 → ACMD41循环 → CMD2 → CMD3 → CMD7 → CMD9
// 精简后的正确流程：
CMD0  → 卡复位（进入空闲状态）
CMD8  → 验证电压范围，检测卡版本
循环：
  CMD55 → 应用命令前缀
  ACMD41 → 发送操作条件，等待卡就绪
结束循环后：
CMD2  → 获取CID
CMD3  → 获取相对地址(RCA)
// ... 后续其他命令
```

-   CMD0：初始化
-   CMD8：电压验证
-   ACMD41循环：
-   CMD2：
-   CMD3：
-   CMD7：
-   ACMD6：
-   高速模式：
-   CMD16：



1.  初始化GPIO口，配置好引脚复用。
2.  配置低速时钟，确保初始时钟频率在400kHz左右（SD卡规范要求）。
3.  设置SDIO电源状态为开启
4.  发送CMD0（GO_IDLE_STATE）：使SD卡进入空闲状态，命令参数为0，无响应。
5.  电压验证（可选）：发送CMD8（SEND_IF_COND）检查SD卡版本和电压兼容性，V2.0以上SD卡会响应
6.  **初始化过程** - 循环发送ACMD41（SD_APP_OP_COND）进行初始化：
    - 先发送CMD55（APP_CMD）表明下一个是应用特定命令
    - 再发送ACMD41带上HCS（高容量支持）位
    - 重复直到OCR寄存器中的忙位变为0，表示初始化完成
7.  **获取CID** - 发送CMD2（ALL_SEND_CID）获取卡的唯一标识符
8.  **获取相对地址** - 发送CMD3（SEND_RELATIVE_ADDR）获取SD卡分配的相对地址（RCA）
9.  **选择卡片** - 发送CMD7（SELECT_CARD）使用获取到的RCA选择该SD卡
10.  **设置总线宽度** - 通过ACMD6（SET_BUS_WIDTH）将数据总线从默认的1位切换到4位模式：
     - 先发送CMD55（APP_CMD）
     - 再发送ACMD6设置4位总线宽度
11.  **切换到高速模式** - 调用`SDIO_SwitchToHighSpeed()`函数：
     - 禁用SDIO时钟
     - 重新配置时钟分频器以获得更高频率
     - 重新使能时钟
     - 可能发送CMD6切换到高速传输模式（取决于卡类型）
12.  **设置块长度** - 发送CMD16（SET_BLOCKLEN）设置读写块大小为512字节
13.  **完成初始化** - 此时SD卡已准备好进行数据读写操作



```c
uint8_t SD_SendCommand(uint32_t cmd, uint32_t arg, uint8_t response_type)
{
    uint32_t timeout = 0xFFFF;//超时等待
    // 清除所有标志
    SDIO_ClearFlag((SDIO_FLAG_CCRCFAIL|SDIO_FLAG_CTIMEOUT|SDIO_FLAG_CMDREND|SDIO_FLAG_CMDSENT));
    SDIO_CmdInitTypeDef SDIO_CmdInitStructure;
    // 配置命令
    SDIO_CmdInitStructure.SDIO_Argument = arg;
    SDIO_CmdInitStructure.SDIO_CmdIndex = cmd;
    SDIO_CmdInitStructure.SDIO_Response = response_type;
    SDIO_CmdInitStructure.SDIO_Wait = SDIO_Wait_No;
    SDIO_CmdInitStructure.SDIO_CPSM = SDIO_CPSM_Enable;
    SDIO_SendCommand(&SDIO_CmdInitStructure);

    // 等待响应或超时
    while (timeout > 0)
    {
        // 检查命令超时
        if (SDIO_GetFlagStatus(SDIO_FLAG_CTIMEOUT) != RESET)
        {
            SDIO_ClearFlag(SDIO_FLAG_CTIMEOUT);
            return SDIO_ERROR_CMD_TIMEOUT; // 明确表示命令超时
        }
        // 检查CRC失败
        if (SDIO_GetFlagStatus(SDIO_FLAG_CCRCFAIL) != RESET)
        {
            SDIO_ClearFlag(SDIO_FLAG_CCRCFAIL);
            return SDIO_ERROR_CMD_CRC; // 明确表示命令CRC失败
        }
        // 根据响应类型检查不同的完成标志
        if (response_type == SDIO_Response_No)
        {
            // 无响应命令：检查CMDSENT标志
            if (SDIO_GetFlagStatus(SDIO_FLAG_CMDSENT) != RESET)
            {
                SDIO_ClearFlag(SDIO_FLAG_CMDSENT);
                return SDIO_ERROR_NONE;
            }
        } else
        {
            // 有响应命令：检查CMDREND标志
            if (SDIO_GetFlagStatus(SDIO_FLAG_CMDREND) != RESET)
            {
                SDIO_ClearFlag(SDIO_FLAG_CMDREND);
                // 验证响应命令索引是否匹配
                if (SDIO_GetCommandResponse() != cmd)
                {
                    return SDIO_ERR_CMD_RSP_MISMATCH;
                }
                return SDIO_ERROR_NONE;
            }
        }
        timeout--;
    }
    if(timeout == 0)
    {
        return SDIO_ERROR_DATA_TIMEOUT;
    }
    return SDIO_ERROR_NONE;
}
```

