# W25QXX

**引脚分布：**

| CS     | 片选输入，低电平有效       | VCC      | 正极                        |
| ------ | -------------------------- | -------- | --------------------------- |
| DO/IO1 | 数据收发                   | HOLD/IO3 | 低电平有效，无效化DI 和 CLK |
| WP/IO2 | 写保护，拉低时不能写入数据 | CLK      | 时钟                        |
| GND    | 地                         | DI/IO0   | 数据收发                    |

> IO0和IO1用在标准和双路SPI模式，IO0到IO3用在四路SPI模式下。如果IO2和IO3不使用，可以硬件拉高。
>
> 一般引脚名前加/表示低电平有效。

10.1 SPI总线操作

10.1.1 标准SPI指令

该w25q16bv是通过一个SPI兼容总线组成的四访问：串行时钟信号（CLK），芯片选择（/ CS），串行数据输入（DI）和串行数据输出（DO）。标准的SPI指令使用DI引脚输入串行写入指令，地址或数据到设备上的上升沿时钟。DO输出引脚是用来读取数据或状态的装置，在下降沿时钟。SPI总线操作模式0（0,0）和3（1,1）的支持。模式0和之间的主要差异模式3是时钟信号的正常状态时，SPI总线主备用数据没有被转移到串行闪存。对于模式0，时钟信号在的下降沿和上升沿，通常是低电平。对于模式3，时钟信号在的下降沿和上升沿，通常是高电平。

10.1.2 双倍SPI指令

  W25Q16BV使用”Fast Read Dual Output and Dual I/O(3B和BBhex)”指令支持双倍速SPI操作。这些指令允许数据以正常速度的两到三倍的在设备间传输。双倍读指令适用于 上电时快速加载代码到RAM 或者 直接从SPI总线上执行代码(XIP) 的情形。当使用双倍速SPI指令时，DI和DO引脚将充当 IO 0和IO 1.

10.1.3 四倍速SPI指令

   W25Q16BV使用”Fast Read Quad Output”、” Fast Read Quad I/O” 、”Word Read Quad I/O” 和 “Octal Word Quad I/O”指令(6B、EB、E7、E3)支持四倍速SPI操作。这些指令允许数据以正常速度的四到六倍的在设备间传输。四倍读指令显著提升连续和随机访问传输速度，这速度满足将代码快速加载到RAM或者直接在SPI总线上执行(XIP)。使用四倍速SPI指令时，DI和DO引脚将充当 IO 0和IO 1 ，WP和HOLD充当IO 2 和IO 3。四倍速SPI指令要求状态寄存器2中的QE功能位打开。

10.1.4 HOLD功能

​     指令允许W25Q16BV在选中激活状态下暂停。在与其他设备共享SPI数据和时钟信号时，这个功能很有用。例如，在已经写了一部分页Buffer后，SPI 总线上产生一个优先终端请求。在这种情形，指令可以保存指令的状态和Buffer中的数据，一旦总线再次可用时，程序可以从离开的地方恢复。功能只适用于标准SPI和双倍SPI操作，不实用四倍速SPI操作。

​     设备在选中(低电平)时，初始化状态。如果CLK信号已经处于低电平时，状态在信号的下降沿时激活。如果当时CLK不是低电平，状态将在CLK的下个下降沿后激活。如果CLK信号已经处于低电平时，状态在信号的上升沿时终止。否则，将在下一个CLK的下降沿后终止。在状态期间，DO脚是高阻态，DI和CLK信号将忽略。在整个操作过程中，信号应该保持低电平来避免重置设备内部逻辑状态。

IO0 = （D4,D0,……..）

IO1 = （D5,D1,……..）

IO2 = （D6,D2,……..）

IO3 = （D7,D3,……..）



|         | 块   | 扇区 | 页   | 字节         | 最大内存地址 |      |      |
| ------- | ---- | ---- | ---- | ------------ | ------------ | ---- | ---- |
| W25Q80  | 16   |      |      | 1048576/1M   | 0x0FFFFF     |      |      |
| W25Q16  | 32   |      |      | 2097152/2M   | 0x1FFFFF     |      |      |
| W25Q32  | 64   |      |      | 4194304/4M   | 0x3FFFFF     |      |      |
| W25Q64  | 128  |      |      | 8388608/8M   | 0x7FFFFF     |      |      |
| W25Q128 | 256  |      |      | 16777216/16M | 0xFFFFFF     |      |      |
| W25Q256 | 512  |      |      | 33554432/32M | 0x01FFFFFF   |      |      |
| W25Q512 | 1024 |      |      | 67108864/64M | 0x03FFFFFF   |      |      |

1块=16扇区；1扇区=16页；1页=256字节

**写入操作时：**

- 写入操作前，必须先进行写使能

- 每个数据位只能由1改写为0，不能由0改写为1

- 写入数据前必须先擦除，擦除后，所有数据位变为1

- 连续写入多字节时，最多写入一页的数据，超过页尾位置的数据，会回到页首覆盖写入

- 写入操作结束后，芯片进入忙状态，不响应新的读写操作

**读取操作时：**

- 直接调用读取时序，无需使能，无需额外操作，没有页的限制， 读取操作结束后不会进入忙状态，但不能在忙状态时读取。

**擦除时：**

- 擦除必须按最小擦除单元进行（4K字节），也就是16页，0XFFF字节。

