# CAN

## 基础

CAN通讯包含CAN_H和CAN_L两根通讯线，属于 **异步** **半双工** 通讯。

一次可以发送 1 到 8 字节的有效数据。

可实现广播式和请求式两种传输方式。

## CAN协议

CAN分为高速CAN和低速CAN。

- 高速CAN使用闭环网络，CAN_H和CAN_L两端添加120Ω的终端电阻。125k~1Mbps, <40m。
- 低速CAN使用开环网络，CAN_H和CAN_L其中一端添加2.2kΩ的终端电阻。10k~125kbps, <1km。

CAN协议分为主要有CAN1.2和CAN2.0。

在CAN2.0中分为2.0A和2.0B。2.0A就是CAN2.0中兼容CAN1.2的部分。

## CAN数据帧

CAN总线采用差分信号，即两线电压差（CAN_H - CAN_L）传输数据位。

**高速CAN规定：**

- 电压差为0V时表示逻辑1（隐性电平）。此时CAN_H和CAN_L都为2.5V。
- 电压差为2V时表示逻辑0（显性电平）。此时CAN_H为3.5V，CAN_L为1.5V。

**低速CAN规定：**

- 电压差为-1.5V时表示逻辑1（隐性电平）。此时CAN_H为1.75V，CAN_L为3.25V。
- 电压差为3V时表示逻辑0（显性电平）。此时CAN_H为4V，CAN_L为1V。

在CAN中，发送逻辑0显性电平需要设备主动改变电位差，发送逻辑1隐性电平只需要放开就行。

总线上执行逻辑上的线“与”时， 显性电平为“0”，隐性电平为“1”。

显性电平和隐性电平二者必居其一。发送方通过使总线电平发生变化，将消息发送给接收方。

**位填充规则：**

发送方每发送5个相同电平后，自动追加一个相反电平的填充位， 接收方检测到填充位时，会自动移除填充位，恢复原始数据。

## 数据格式

| 帧类型 | 用途                                   |
| ------ | -------------------------------------- |
| 数据帧 | 发送设备主动发送数据（广播式）         |
| 遥控帧 | 接收设备主动请求数据（请求式）         |
| 错误帧 | 某个设备检测出错误时向其他设备通知错误 |
| 过载帧 | 接收设备通知其尚未做好接收准备         |
| 帧间隔 | 用于将数据帧及遥控帧与前面的帧分离开   |

### 数据帧

**数据帧各部分用途简介：**

- 帧起始：
  
  - SOF（Start of Frame）：帧起始，表示后面一段波形为传输的数据位，1个位的显性位。
  
- 仲裁段：分为标准格式和扩展格式。

  - 标准格式：

    - ID（Identify）：标识符，区分功能，同时决定优先级。

      > 有 11 个位。从 ID28 到 ID18 被依次发送。禁止高 7 位都为隐性。（禁止设定：ID=1111111XXXX）。

    - RTR（Remote Transmission Request ）：远程请求位，区分数据帧和遥控帧。数据帧RTR为显性电平 0。

  - 扩展格式：

    - 基本 ID（Identify）：扩展格式ID的 ID28 到 ID18。为标识符，区分功能，同时决定优先级。

      > 有 29 个位。基本 ID 从 ID28 到 ID18，扩展 ID 由 ID17 到 ID0 表示。基本 ID 和 标准格式的 ID 相同。禁止高 7 位都为隐性。（禁止设定：基本 ID=1111111XXXX）。

    - SRR（Substitute Remote Request）：替代标准格式的RTR，协议升级时留下的无意义位，永远置隐性电平 1。

    - IDE（Identifier Extension）：扩展标志位，区分标准格式和扩展格式。IDE位为显性电平 0，表示数据帧为标准格式；IDE位为隐性电平 1，表示数据帧为扩展帧格式。

    - 扩展 ID：扩展格式ID的 ID17 到 ID0。为标识符，区分功能，同时决定优先级。

    - RTR（Remote Transmission Request ）：远程请求位，区分数据帧和遥控帧。数据帧RTR为显性电平 0。

- 控制段：也分为标准格式和扩展格式。

  - 标准格式：
    - IDE（Identifier Extension）：扩展标志位，区分标准格式和扩展格式。IDE位为显性电平 0，表示数据帧为标准格式；
    - r0：保留位，为后续协议升级留下空间。发送显性电平 0，但接收方可以接收显性、隐性及其任意组合的电平。
    - DLC（Data Length Code）：指示接下来的的数据段的长度，本身有四位，用二进制的方式指示数据段有几个字节。
  - 扩展格式：
    - r1：保留位，为后续协议升级留下空间。发送显性电平 0，但接收方可以接收显性、隐性及其任意组合的电平。
    - r0：保留位，为后续协议升级留下空间。发送显性电平 0，但接收方可以接收显性、隐性及其任意组合的电平。
    - DLC（Data Length Code）：指示接下来的的数据段的长度，本身有四位，用二进制的方式指示数据段有几个字节。

- 数据段：

  - Data：数据段的1~8个字节有效数据。从 MSB（最高位）开始输出。

- CRC段：循环冗余校验，校验数据是否正确，包含16位。CRC 的计算范围包括帧起始、仲裁段、控制段、数据段。

  - CRC数据段：15 位，进行CRC校验。
  - CRC界定符：为应答位前后发送方和接收方释放总线留下时间。显性电平 0。

- ACK段：应答位，判断数据有没有被接收方接收。

  - ACK 槽(ACK Slot)：若接收方正确接收则回复隐形电平 1；否则为显性电平 0。
  - ACK 界定符：为应答位前后发送方和接收方释放总线留下时间。

- 帧结束：

  - EOF（End of Frame ）：帧结束，表示数据位已经传输完毕。由 7 个位的隐性位构成。

### 遥控帧

遥控帧无数据段，RTR为隐性电平1，其他部分与数据帧相同。

### 错误帧

总线上所有设备都会监督总线的数据，一旦发现 “ 位错误 ” 或 “ 填充错误 ” 或 “ CRC 错误 ” 或 “ 格式错误 ” 或 “ 应答错误 ” ，这些设备便会发出错误帧来破坏数据，同时终止当前的发送设备。

错误帧分为主动错误帧和被动错误帧。

主动错误帧包含 6 个显性0，

被动错误帧包含 6 个隐性1，

### 过载帧

当接收方收到大量数据而无法处理时，其可以发出过载帧，延缓发送方的数据发送，以平衡总线负载，避免数据丢失。

过载帧包含 6 个显性0，

### 帧间隔

将数据帧和遥控帧与前面的帧分离开。

## CAN时钟

CAN总线没有时钟线，总线上的所有设备通过约定波特率的方式确定每一个数据位的时长。

为了灵活调整每个采样点的位置，使采样点对齐数据位中心附近，CAN总线对每一个数据位的时长进行了更细的划分，分为：

- 同步段（SS）
- 传播时间段 （PTS）
- 相位缓冲段1（PBS1）
- ***在相位缓冲段1 & 相位缓冲段2 之间采集数据。***
- 相位缓冲段2（PBS2）

每个段又由若干个最小时间单位（Tq）构成。

**硬同步：**

- 每个设备都有一个位时序计时周期，当某个设备（发送方）率先发送报文， 其他所有设备（接收方）收到SOF的下降沿时，接收方会将自己的位时序计时周期拨到SS段的位置，与发送方的位时序计时周期保持同步。

- 硬同步只在帧的第一个下降沿（SOF下降沿）有效。

- 经过硬同步后，若发送方和接收方的时钟没有误差，则后续所有数据位的采样点必然都会对齐数据位中心附近。

**再同步：**

- 若发送方或接收方的时钟有误差，随着误差积累，数据位边沿逐渐偏离SS段， 则此时接收方根据再同步补偿宽度值（SJW）通过加长PBS1段，或缩短PBS2 段，以调整同步。

- 再同步可以发生在第一个下降沿之后的每个数据位跳变边沿。

波特率 = 1 / 一个数据位的时长 = 1 / (**T**SS + **T**PTS + **T**PBS1 + **T**PBS2)。

例如： SS = 1Tq，PTS = 3Tq，PBS1 = 3Tq，PBS2 = 3Tq Tq = 0.5us。

波特率 = 1 / (0.5us + 1.5us + 1.5us + 1.5us) = 200kbps。

## CAN发送资源分配

CAN只有一对数据线，所以同时最多只能有一个设备发数据，根据这一特点，在遇到多设备同时试图发送数据有两个原则：

原则1 **先占先得**：（已经发送中要加入第二设备发送）

- 若当前已经有设备正在操作总线发送数据帧/遥控帧，则其他任何设备不能再同时发送数据帧/遥控帧（可以发送错误帧/过载帧破坏当前数据）。

- 任何设备检测到连续11个隐性电平，即认为总线空闲，只有在总线空闲时， 设备才能发送数据帧/遥控帧。
- 一旦有设备正在发送数据帧/遥控帧，总线就会变为活跃状态，必然不会出现连续11个隐性电平，其他设备自然也不会破坏当前发送。
- 若总线活跃状态其他设备有发送需求，则需要等待总线变为空闲，才能执行发送需求。

原则2 **非破坏性仲裁**：（两个设备同时发送）

- 若多个设备的发送需求同时到来或因等待而同时到来，则CAN总线协议会根据ID号（仲裁段）进行非破坏性仲裁，ID号小的（优先级高）取到总线控制权，ID号大的（优先级低）仲裁失利后将转入接收状态，等待下一次总线空 闲时再尝试发送。
- 实现非破坏性仲裁需要两个要求：
  - 线与特性：总线上任何一个设备发送显性电平0时，总线就会呈现显性电平0状态，只有当所有设备都发送隐性电平1时，总线才呈现隐性电平1状态，即：0 & X & X = 0，1 & 1 & 1 = 1。
  - 回读机制：每个设备发出一个数据位后，都会读回总线当前的电平状态， 以确认自己发出的电平是否被真实地发送出去了，根据线与特性，发出0 读回必然是0，发出1读回不一定是1。、

- 数据位从前到后依次比较，出现差异且数据位为1的设备仲裁失利。

- 数据帧和遥控帧ID号一样时，数据帧的优先级高于遥控帧。

## CAN错误

错误状态的种类单元始终处于 3 种状态之一。

- **主动错误状态**：主动错误状态是可以正常参加总线通信的状态。 处于主动错误状态的单元检测出错误时，输出包含 6 个显性 0 的主动错误标志破坏通信。

- **被动错误状态**：处于被动错误状态的单元虽能参加总线通信，但为不妨碍其它单元通信，接收时不能积极地发送错误通知。 处于被动错误状态的单元检测出错误时，输出包含 6 个隐性 1 的被动错误标志，不可破坏总线通信。

  处于被动错误状态的单元即使检测出错误，而其它处于主动错误状态的单元如果没发现错误，整个总线也被认为是没有错误的。  

  另外，处于被动错误状态的单元在发送结束后不能马上再次开始发送。在开始下次发送前，在间隔帧期间内必须插入“延迟传送”(8 个位的隐性位)。

- **总线关闭态**：总线关闭态是不能参加总线上通信的状态。 信息的接收和发送均被禁止。 这些状态依靠发送错误计数和接收错误计数来管理，根据计数值决定进入何种状态。

每个设备内部管理一个发送错误计数器TEC和发送错误计数器REC，通过二者的数量判断状态。

设备默认处于主动错误状态，权力最大，可以发送主动错误帧破坏通信。出现过多错误（TEC或REC 大于 127 时）进入被动错误状态，在被动错误状态减小了TEC或REC的值可以恢复主动错误状态。当 TEC > 255 时，进入总线关闭状态，在总线上检测到128次连续的11个隐性位则恢复主动错误状态。



## CAN特点

CAN 协议具有以下特点：

- **多主控制：**在总线空闲时，所有的单元都可开始发送消息（多主控制）。 最先访问总线的单元可获得发送权（CSMA/CA 方式）。 多个单元同时开始发送时，发送高优先级 ID 消息的单元可获得发送权。
- **消息的发送：** 在 CAN 协议中，所有的消息都以固定的格式发送。总线空闲时，所有与总线相连的单元都可以开始发送新消息。两个以上的单元同时开始发送消息时，根据标识符（Identifier 以下称为 ID）决定优先级。ID 并不是表示发送的目的地址，而是表示访问总线的消息的优先级。两个以上的单元同时开始发送消息时，对各消息 ID 的每个位进行逐个仲裁比较。仲裁获胜（被判定为优先级最高）的单元可继续发送消息，仲裁失利的单元则立刻停止发送而进行接收工作。
- **系统的柔软性：**与总线相连的单元没有类似于“地址”的信息。因此在总线上增加单元时，连接在总线上的其它单元的软硬件及应用层都不需要改变。
- **通信速度：**根据整个网络的规模，可设定适合的通信速度。 在同一网络中，所有单元必须设定成统一的通信速度。即使有一个单元的通信速度与其它的不一样，此单元也会输出错误信号，妨碍整个网络的通信。不同网络间则可以有不同的通信速度。
- **远程数据请求：**可通过发送“遥控帧” 请求其他单元发送数据。
- **错误检测功能&错误通知功能&错误恢复功能：**所有的单元都可以检测错误（错误检测功能）。检测出错误的单元会立即同时通知其他所有单元（错误通知功能）。正在发送消息的单元一旦检测出错误，会强制结束当前的发送。强制结束发送的单元会不断反复地重新发送此消息直到成功发送为止（错误恢复功能）。
- **故障封闭：**CAN 可以判断出错误的类型是总线上暂时的数据错误（如外部噪声等）还是持续的数据错误（如单元内部故障、驱动器故障、断线等）。由此功能，当总线上发生持续数据错误时，可将引起此故障的单元从总线上隔离出去。
- **连接：**CAN 总线是可同时连接多个单元的总线。可连接的单元总数理论上是没有限制的。但实际上可连接的单元数受总线上的时间延迟及电气负载的限制。降低通信速度，可连接的单元数增加；提高通信速度，则可连接 的单元数减少。

## bxCAN收发流程

**收流程：**

将数据写入发送邮箱（有 3 个，包含发送邮箱 0，邮箱 1，邮箱 2）后使用程序触发后由发送和接收控制器发送（！自动发送？），

**发流程：**

发送和接收控制器接收到数据后会自动用接收过滤器进行过滤，将其存入接收FIFO（接收FIFO有两个，包含接收FIFO 0 和 接收FIFO  1） 的邮箱（每个FIFO有三个邮箱，包含邮箱 0，邮箱 1，邮箱 2）里，然后由CPU读取。

## STM32F1XX函数介绍

### CAN_DeInit CAN外设置缺省值

将外设 CAN 的全部寄存器重设为缺省值。

```c
void CAN_DeInit(CAN_TypeDef* CANx);
```

### CAN_Init CAN外设初始化

按照CAN_InitTypeDef成员初始化CAN。

```c
uint8_t CAN_Init(CAN_TypeDef* CANx, CAN_InitTypeDef* CAN_InitStruct);
```

CAN_InitTypeDef成员及作用：

```C
typedef struct 
{ 
FunctionnalState CAN_TTCM; 
FunctionnalState CAN_ABOM; 
FunctionnalState CAN_AWUM; 
FunctionnalState CAN_NART; 
FunctionnalState CAN_RFLM; 
FunctionnalState CAN_TXFP; 
u8 CAN_Mode; 
u8 CAN_SJW; 
u8 CAN_BS1; 
u8 CAN_BS2; 
u16 CAN_Prescaler; 
} CAN_InitTypeDef;
```

- CAN_TTCM：用来使能或者失能时间触发通讯模式，可以设置这个参数的值为 ENABLE 或者 DISABLE。
  - ENABLE：
  - DISABLE：
- CAN_ABOM：用来使能或者失能自动离线管理，可以设置这个参数的值为 ENABLE 或者 DISABLE。
  - ENABLE：
  - DISABLE：
- CAN_AWUM：用来使能或者失能自动唤醒模式，可以设置这个参数的值为 ENABLE 或者 DISABLE。
  - ENABLE：
  - DISABLE：
- CAN_NARM：用来使能或者失能非自动重传输模式，可以设置这个参数的值为 ENABLE 或者 DISABLE。
  - ENABLE：
  - DISABLE：
- CAN_RFLM：用来使能或者失能接收 FIFO 锁定模式，可以设置这个参数的值为 ENABLE 或者 DISABLE。
  - ENABLE：接收FIFO锁定，FIFO溢出时，新收到的报文会被丢弃；
  - DISABLE：禁用FIFO锁定，FIFO溢出时，FIFO中最后收到的报文被新报文覆盖；
- CAN_TXFP：用来使能或者失能发送 FIFO 优先级，可以设置这个参数的值为 ENABLE 或者 DISABLE。
  - ENABLE：优先级由发送请求的顺序来决定，先请求的先发送；
  - DISABLE：优先级由报文标识符来决定，标识符值小的先发送（标识符值相等时，邮箱号小 的报文先发送）；
- CAN_Mode：设置了 CAN 的工作模式。
  - CAN_Mode_Normal：CAN 硬件工作在正常模式。
  - CAN_Mode_Silent：CAN 硬件工作在静默模式。
  - CAN_Mode_LoopBack：CAN 硬件工作在环回模式。
  - CAN_Mode_Silent_LoopBack：CAN 硬件工作在静默环回模式。
- CAN_SJW：定义了重新同步跳跃宽度(SJW)，即在每位中可以延长或缩短多少个时间单位的上限。
  - CAN_SJW_1tq：重新同步跳跃宽度 1 个时间单位。
  - CAN_SJW_2tq：重新同步跳跃宽度 2 个时间单位。
  - CAN_SJW_3tq：重新同步跳跃宽度 3 个时间单位。
  - CAN_SJW_4tq：重新同步跳跃宽度 4 个时间单位。
- CAN_BS1：设定了时间段 1 的时间单位数目。
  - CAN_BS1_1tq：时间段 1 为 1 个时间单位。
  - CAN_BS1_16tq：时间段 1 为 16 个时间单位。
- CAN_BS2：设定了时间段 1 的时间单位数目。
  - CAN_BS2_1tq：时间段 2 为 1 个时间单位。
  - CAN_BS2_8tq：时间段 2 为 8 个时间单位。
- CAN_Prescaler 设定了一个时间单位的长度，它的范围是 1 到 1024。

### CAN_FilterInit CAN过滤器初始化

按CAN_FilterInitTypeDef成员初始化CAN过滤器。

函数原型为：

```c
void CAN_FilterInit(CAN_FilterInitTypeDef* CAN_FilterInitStruct);
```

CAN_FilterInitTypeDef成员及作用：

```c
typedef struct 
{ 
u8 CAN_FilterNumber; 
u8 CAN_FilterMode; 
u8 CAN_FilterScale; 
u16 CAN_FilterIdHigh; 
u16 CAN_FilterIdLow; 
u16 CAN_FilterMaskIdHigh; 
u16 CAN_FilterMaskIdLow; 
u16 CAN_FilterFIFOAssignment; 
FunctionalState CAN_FilterActivation; 
} CAN_FilterInitTypeDef; 
```

- CAN_FilterNumber：CAN_FilterNumber 指定了待初始化的过滤器，它的范围是 0 到 13。

  STM32F1XX系列单片机有 14 个过滤器组（互联型28个）。

- CAN_FilterMode： 指定了过滤器将被初始化到的模式。
  - CAN_FilterMode_IdMask：标识符屏蔽位模式。
  
    可以过滤一部分。
  
  - CAN_FilterMode_IdList：标识符列表模式。
  
    仅接收与过滤器报文ID相同的CAN报文。
  
- CAN_FilterScale：给出了过滤器位宽。
  - CAN_FilterScale_Two16bit 2 个 16 位过滤器。
  - CAN_FilterScale_One32bit 1 个 32 位过滤器。
  
- CAN_FilterIdHigh：用来设定过滤器标识符（32 位位宽时为其高段位，16 位位宽时为第一个）。它的范围是 0x0000 到 0xFFFF。

- CAN_FilterIdLow：用来设定过滤器标识符（32 位位宽时为其低段位，16 位位宽时为第二个）。它的范围是 0x0000 到 0xFFFF。

- CAN_FilterMaskIdHigh：用来设定过滤器屏蔽标识符或者过滤器标识符（32 位位宽时为其高段位，16 位位宽时为第一个）。它的范围是 0x0000 到 0xFFFF。

- CAN_FilterMaskIdLow：用来设定过滤器屏蔽标识符或者过滤器标识符（32 位位宽时为其低段位，16 位位 宽时为第二个）。它的范围是 0x0000 到 0xFFFF。

- CAN_FilterFIFOAssignment：设定了指向过滤器的 FIFO（0 或 1）。
  - CAN_FilterFIFO0 过滤器 FIFO0 指向过滤器 x。
  - CAN_FilterFIFO1 过滤器 FIFO1 指向过滤器 x。
  
- CAN_FilterActivation：使能或者失能过滤器。该参数可取的值为 ENABLE 或者 DISABLE。

### CAN_StructInit 开始消息的传输

```c
uint8_t CAN_Transmit(CAN_TypeDef* CANx, CanTxMsg* TxMessage);
```

参数：

```c
typedef struct
{
u32 StdId;
u32 ExtId;
u8 IDE;
u8 RTR;
u8 DLC;
u8 Data[8];
} CanTxMsg;
```

- StdId：用来设定标准标识符。它的取值范围为 0 到 0x7FF。
- ExtId：用来设定扩展标识符。它的取值范围为 0 到 0x3FFFF。
- IDE：用来设定消息标识符的类型。
  - CAN_ID_STD 使用标准标识符。
  - CAN_ID_EXT 使用标准标识符 + 扩展标识符。
- RTR：用来设定待传输消息的帧类型。它可以设置为数据帧或者远程帧。
  - CAN_RTR_DATA 数据帧。
  - CAN_RTR_REMOTE 远程帧。
- DLC：用来设定待传输消息的帧长度。它的取值范围是 0 到 0x8。
- Data[8]：包含了待传输数据，它的取值范围为 0 到 0xFF。

返回值：所使用邮箱的号码，如果没有空邮箱返回 CAN_NO_MB。

### CAN_TransmitStatus 检查传输状态

```c
uint8_t CAN_TransmitStatus(CAN_TypeDef* CANx, uint8_t TransmitMailbox)
```

参数：邮箱号码。

返回值：该邮箱的状态。

- CAN_TxStatus_Ok：传输完毕。
- CAN_TxStatus_PENDING：消息是否挂号。
- CAN_TxStatus_Ok：其他。

### CAN_MessagePending 返回挂号的信息数量

```c

```

### CAN_Receive 接收一个消息

```c
void CAN_Receive(CAN_TypeDef* CANx, uint8_t FIFONumber, CanRxMsg* RxMessage);
```



```c
typedef struct
{
u32 StdId;
u32 ExtId;
u8 IDE;
u8 RTR;
u8 DLC;
u8 Data[8];
u8 FMI;
} CanRxMsg;
```

- StdId：用来设定标准标识符。它的取值范围为 0 到 0x7FF。
- ExtId：用来设定扩展标识符。它的取值范围为 0 到 0x3FFFF。
- IDE：用来设定消息标识符的类型。
  - CAN_ID_STD 使用标准标识符。
  - CAN_ID_EXT 使用标准标识符 + 扩展标识符。
- RTR：用来设定待传输消息的帧类型。它可以设置为数据帧或者远程帧。
  - CAN_RTR_DATA 数据帧。
  - CAN_RTR_REMOTE 远程帧。
- DLC：用来设定待传输消息的帧长度。它的取值范围是 0 到 0x8。
- Data[8]：包含了待传输数据，它的取值范围为 0 到 0xFF。
- FMI 设定为消息将要通过的过滤器索引，这些消息存储于邮箱中。该参数取值范围 0 到 0xFF。


## STM32F1使用CAN流程

### STM32F1的CAN初始化流程

1. 开启GPIO和CAN时钟。

   ```c
   RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
   RCC_APB1PeriphClockCmd(RCC_APB1Periph_CAN1, ENABLE);
   ```

2. CAN收发GPIO初始化。

   ```c
   GPIO_InitTypeDef GPIO_InitStructure;
   GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
   GPIO_InitStructure.GPIO_Pin = GPIO_Pin_12;
   GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
   GPIO_Init(GPIOA, &GPIO_InitStructure);
   GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
   GPIO_InitStructure.GPIO_Pin = GPIO_Pin_11;
   GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
   GPIO_Init(GPIOA, &GPIO_InitStructure);
   ```
   
3. CAN_InitTypeDef初始化。

   ```c
   CAN_InitTypeDef CAN_InitStructure;                //申请CAN_InitTypeDef结构体
   CAN_InitStructure.CAN_TTCM = DISABLE;             //时间触发通讯模式失能
   CAN_InitStructure.CAN_ABOM = DISABLE;             //自动离线管理失能
   CAN_InitStructure.CAN_AWUM = DISABLE;             //自动唤醒模式失能
   CAN_InitStructure.CAN_NART = DISABLE;             //非自动重传输模式失能
   CAN_InitStructure.CAN_RFLM = DISABLE;             //接收 FIFO 锁定模式失能
   CAN_InitStructure.CAN_TXFP = DISABLE;             //发送 FIFO 优先级失能
   CAN_InitStructure.CAN_Mode = CAN_Mode_LoopBack;   //CAN 硬件工作在静默环回模式
   CAN_InitStructure.CAN_SJW = CAN_SJW_2tq;          //重新同步跳跃宽度 2 个时间单位
   CAN_InitStructure.CAN_BS1 = CAN_BS1_2tq;          //时间段 1 为 2 个时间单位
   CAN_InitStructure.CAN_BS2 = CAN_BS2_3tq;          //时间段 2 为 3 个时间单位
   CAN_InitStructure.CAN_Prescaler = 48;             //设定了一个时间单位的长度，也就是波特率。
                                                     //波特率 = 36M / 48 / (1 + 2 + 3) = 125K
   CAN_Init(CAN1, &CAN_InitStructure);               //CAN_InitTypeDef初始化
   ```

4. CAN_FilterInitTypeDef初始化。

   ```c
   CAN_FilterInitTypeDef CAN_FilterInitStructure;                        //申请CAN_FilterInitTypeDef结构体
   CAN_FilterInitStructure.CAN_FilterNumber = 0;                         //指定待初始化的过滤器，范围是 0 到 13。
   CAN_FilterInitStructure.CAN_FilterMode = CAN_FilterMode_IdMask;       //指定过滤器初始化为标识符屏蔽位模式。
   CAN_FilterInitStructure.CAN_FilterScale = CAN_FilterScale_32bit;      //过滤器位宽为1 个 32 位过滤器
   CAN_FilterInitStructure.CAN_FilterIdHigh = 0x0000;                    //设定过过滤器标识符高位段
   CAN_FilterInitStructure.CAN_FilterIdLow = 0x0000;                     //设定过过滤器标识符低位段
   CAN_FilterInitStructure.CAN_FilterMaskIdHigh = 0x0000;                //过滤器屏蔽标识符或者过滤器标识符
   CAN_FilterInitStructure.CAN_FilterMaskIdLow = 0x0000;                 //设定过滤器屏蔽标识符或者过滤器标识符
   CAN_FilterInitStructure.CAN_FilterFIFOAssignment = CAN_Filter_FIFO0;  //了指向过滤器的 FIFO
   CAN_FilterInitStructure.CAN_FilterActivation = ENABLE;                //使能过滤器。
   CAN_FilterInit(&CAN_FilterInitStructure);                             //CAN_FilterInitTypeDef初始化
   ```


### STM32F1的CAN发送流程

```c
void MyCAN_Transmit(uint32_t ID, uint8_t Length, uint8_t *Data)//参数为ID，数据长度，数据数组的首位地址。
{
	CanTxMsg TxMessage;//申请CanTxMsg结构体，并初始化。
	TxMessage.StdId = ID;//ID
	TxMessage.ExtId = ID;//ID
	TxMessage.IDE = CAN_Id_Standard;//CAN_ID_STD 使用标准标识符。
	TxMessage.RTR = CAN_RTR_Data;//数组。
	TxMessage.DLC = Length;//数据长度。
	for (uint8_t i = 0; i < Length; i ++)
	{
		TxMessage.Data[i] = Data[i];
	}
	
	uint8_t TransmitMailbox = CAN_Transmit(CAN1, &TxMessage);
	
	uint32_t Timeout = 0;
	while (CAN_TransmitStatus(CAN1, TransmitMailbox) != CAN_TxStatus_Ok)
	{
		Timeout ++;
		if (Timeout > 100000)
		{
			break;
		}
	}
}

uint8_t MyCAN_ReceiveFlag(void)
{
	if (CAN_MessagePending(CAN1, CAN_FIFO0) > 0)
	{
		return 1;
	}
	return 0;
}

void MyCAN_Receive(uint32_t *ID, uint8_t *Length, uint8_t *Data)
{
	CanRxMsg RxMessage;
	CAN_Receive(CAN1, CAN_FIFO0, &RxMessage);
	
	if (RxMessage.IDE == CAN_Id_Standard)
	{
		*ID = RxMessage.StdId;
	}
	else
	{
		*ID = RxMessage.ExtId;
	}
	
	if (RxMessage.RTR == CAN_RTR_Data)
	{
		*Length = RxMessage.DLC;
		for (uint8_t i = 0; i < *Length; i ++)
		{
			Data[i] = RxMessage.Data[i];
		}
	}
	else
	{
		//...
	}
}

```





```c
void MyCAN_Init(void)
{
    //打开GPIO和CAN的时钟。
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_CAN1, ENABLE);
	//GPIO初始化
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_12;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_11;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);
	//CAN初始化
	CAN_InitTypeDef CAN_InitStructure;
	CAN_InitStructure.CAN_Prescaler = 48;//配置CAN外设的时钟分频，范围1~1024。//波特率 =36M/48/(1+2+3)=125K
	CAN_InitStructure.CAN_Mode = CAN_Mode_LoopBack;//配置CAN的工作模式，回环或正常模式
	CAN_InitStructure.CAN_SJW = CAN_SJW_2tq;//配置SJW极限值
	CAN_InitStructure.CAN_BS1 = CAN_BS1_2tq;//配置BS1段长度
	CAN_InitStructure.CAN_BS2 = CAN_BS2_3tq;//配置BS2段长度
	CAN_InitStructure.CAN_TTCM = DISABLE;//是否使能TTCM时间触发功能
	CAN_InitStructure.CAN_ABOM = DISABLE;//是否使能ABOM自动离线管理功能
	CAN_InitStructure.CAN_AWUM = DISABLE;//是否使能AWUM自动唤醒功能
	CAN_InitStructure.CAN_NART = DISABLE;//是否使用NART自动重传功能
	CAN_InitStructure.CAN_RFLM = DISABLE;//是否使能RELM锁定FIFO功能
	CAN_InitStructure.CAN_TXFP = DISABLE;//配置TXFP报文优先级的判定方法
	CAN_Init(CAN1, &CAN_InitStructure);
	//CAN通讯接收初始化
	CAN_FilterInitTypeDef CAN_FilterInitStructure;
	CAN_FilterInitStructure.CAN_FilterIdHigh = 0x0000;
	CAN_FilterInitStructure.CAN_FilterIdLow = 0x0000;
	CAN_FilterInitStructure.CAN_FilterMaskIdHigh = 0x0000;
	CAN_FilterInitStructure.CAN_FilterMaskIdLow = 0x0000;
	CAN_FilterInitStructure.CAN_FilterFIFOAssignment = CAN_Filter_FIFO0;
	CAN_FilterInitStructure.CAN_FilterNumber = 0;
	CAN_FilterInitStructure.CAN_FilterMode = CAN_FilterMode_IdMask;
	CAN_FilterInitStructure.CAN_FilterScale = CAN_FilterScale_32bit;
	CAN_FilterInitStructure.CAN_FilterActivation = ENABLE;
	CAN_FilterInit(&CAN_FilterInitStructure);
}

void MyCAN_Transmit(uint32_t ID, uint8_t Length, uint8_t *Data)
{
	CanTxMsg TxMessage;
	TxMessage.StdId = ID;
	TxMessage.ExtId = ID;
	TxMessage.IDE = CAN_Id_Standard;		//CAN_ID_STD
	TxMessage.RTR = CAN_RTR_Data;
	TxMessage.DLC = Length;
	for (uint8_t i = 0; i < Length; i ++)
	{
		TxMessage.Data[i] = Data[i];
	}
	
	uint8_t TransmitMailbox = CAN_Transmit(CAN1, &TxMessage);
	
	uint32_t Timeout = 0;
	while (CAN_TransmitStatus(CAN1, TransmitMailbox) != CAN_TxStatus_Ok)
	{
		Timeout ++;
		if (Timeout > 100000)
		{
			break;
		}
	}
}

uint8_t MyCAN_ReceiveFlag(void)
{
	if (CAN_MessagePending(CAN1, CAN_FIFO0) > 0)
	{
		return 1;
	}
	return 0;
}

void MyCAN_Receive(uint32_t *ID, uint8_t *Length, uint8_t *Data)
{
	CanRxMsg RxMessage;
	CAN_Receive(CAN1, CAN_FIFO0, &RxMessage);
	
	if (RxMessage.IDE == CAN_Id_Standard)
	{
		*ID = RxMessage.StdId;
	}
	else
	{
		*ID = RxMessage.ExtId;
	}
	
	if (RxMessage.RTR == CAN_RTR_Data)
	{
		*Length = RxMessage.DLC;
		for (uint8_t i = 0; i < *Length; i ++)
		{
			Data[i] = RxMessage.Data[i];
		}
	}
	else
	{
		//...
	}
}

```

```c
#ifndef __MYCAN_H
#define __MYCAN_H

void MyCAN_Init(void);
void MyCAN_Transmit(uint32_t ID, uint8_t Length, uint8_t *Data);
uint8_t MyCAN_ReceiveFlag(void);
void MyCAN_Receive(uint32_t *ID, uint8_t *Length, uint8_t *Data);

#endif

```

```c
#include "stm32f10x.h"                  // Device header
#include "Delay.h"
#include "OLED.h"
#include "Key.h"
#include "MyCAN.h"

uint8_t KeyNum;
uint32_t TxID = 0x555;
uint8_t TxLength = 4;
uint8_t TxData[8] = {0x11, 0x22, 0x33, 0x44};

uint32_t RxID;
uint8_t RxLength;
uint8_t RxData[8];

int main(void)
{
	OLED_Init();
	Key_Init();
	MyCAN_Init();
	
	OLED_ShowString(1, 1, "TxID:");
	OLED_ShowHexNum(1, 6, TxID, 3);
	OLED_ShowString(2, 1, "RxID:");
	OLED_ShowString(3, 1, "Leng:");
	OLED_ShowString(4, 1, "Data:");
	
	while (1)
	{
		KeyNum = Key_GetNum();
		
		if (KeyNum == 1)
		{
			TxData[0] ++;
			TxData[1] ++;
			TxData[2] ++;
			TxData[3] ++;
			
			MyCAN_Transmit(TxID, TxLength, TxData);
		}
		
		if (MyCAN_ReceiveFlag())
		{
			MyCAN_Receive(&RxID, &RxLength, RxData);
			
			OLED_ShowHexNum(2, 6, RxID, 3);
			OLED_ShowHexNum(3, 6, RxLength, 1);
			OLED_ShowHexNum(4, 6, RxData[0], 2);
			OLED_ShowHexNum(4, 9, RxData[1], 2);
			OLED_ShowHexNum(4, 12, RxData[2], 2);
			OLED_ShowHexNum(4, 15, RxData[3], 2);
		}
	}
}

```

