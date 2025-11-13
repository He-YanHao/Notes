# SD卡

## 基础介绍

通常可通过**SDIO接口**或**SPI接口**实现。

SDIO方式**速度快**，适合大容量高速数据传输；

SPI方式**引脚占用少**，实现相对简单。



## 硬件连接

### 嘉立创天空星引脚连接

| SDIO        | GPIO |        |
| ----------- | ---- | ------ |
| SDIO_CLK    | PC12 | 时钟线 |
| SDIO_CMD    | PD2  | 命令线 |
| SDIO_D0     | PC8  | 数据线 |
| SDIO_D1     | PC9  | 数据线 |
| SDIO_D2     | PC10 | 数据线 |
| SDIO_D3     | PC11 | 数据线 |
| SD_CARD_DET | PD3  |        |





## 软件驱动实现

### 工程文件准备

根据你选择的通信方式，在你的工程中添加对应的驱动文件：

- **SDIO方式**：
    - 添加STM32F4标准库中的 `stm32f4xx_sdio.c` 和 `stm32f4xx_dma.c`（如果使用DMA）。
    - 需要自己实现或移植 `sdio_sdcard.c` 和 `sdio_sdcard.h`，这些文件包含了SD卡初始化和读写等核心函数。
- **SPI方式**：
    - 添加 `stm32f4xx_spi.c` 。
    - 需要自己实现或移植SPI方式的SD卡驱动文件，例如 `mmc_sd.c` 和 `mmc_sd.h` 。

### 初始化配置

**SDIO方式** (`SD_Init()` 函数概览)：
1.  **配置SDIO时钟和GPIO**：初始化SDIO所用到的GPIO引脚为复用功能，并使能SDIO时钟。
2.  **SD卡上电识别** (`SD_PowerOn`)：发送CMD0使SD卡进入空闲状态，发送CMD8进行电压检查，通过CMD55+ACMD41获取SD卡版本和容量信息（区分V1、V2、SDHC等）。
3.  **初始化SD卡** (`SD_InitializeCards`)：发送CMD2获取CID，CMD3获取RCA（相对地址）。
4.  **配置SDIO**：设置SDIO时钟分频（初始化阶段通常不超过400kHz，初始化完成后可提高至SD卡支持的最高频率），设置4位总线宽度（通过ACMD6）。
5.  **选择SD卡** (CMD7+RCA)：通过RCA地址选中要操作的SD卡。

**SPI方式** (`SD_Initialize()` 函数概览)：
1.  **初始化SPI和GPIO**：配置SPI为主机模式，较低初始速率（如400kHz），以及相关的MOSI、MISO、SCK和CS引脚。
2.  **发送至少74个时钟周期**：在初始化前，需提供至少74个时钟脉冲供SD卡同步。
3.  **切换到SPI模式**：发送CMD0（参数为0x00）使SD卡进入IDLE状态，此命令会使SD卡切换到SPI模式。
4.  检查SD卡版本（可选）：发送CMD8检查是否支持V2.0协议。
5.  **初始化SD卡**：循环发送CMD55+ACMD41，直到返回值非0x01，表明SD卡初始化完成，退出IDLE状态。
6.  **禁用CRC检查**（可选）：发送CMD59，参数0x00以禁用CRC检查。
7.  **设置扇区大小**：发送CMD16，设置块大小为512字节。
8.  **切换到高速模式**：初始化完成后，提高SPI时钟速率（例如到3MHz或更高，取决于SD卡支持）。

### 实现磁盘读写函数

无论SDIO还是SPI方式，最终都需要实现基础的磁盘读写函数，这些函数是后续移植文件系统的基础：

- **SDIO方式**：通常需要实现 `SD_ReadDisk()` 和 `SD_WriteDisk()` 函数，内部会调用 `SD_ReadBlock()` / `SD_ReadMultiBlocks()` 和 `SD_WriteBlock()` / `SD_WriteMultiBlocks()` 等函数，并处理单块与多块读写。
- **SPI方式**：同样需要实现 `SD_ReadDisk()` 和 `SD_WriteDisk()` 函数，内部通过SPI时序读取和写入数据块。

**特别注意**：在SDIO方式下，传递给读写函数的**数据缓冲区地址最好4字节对齐**，否则可能导致DMA传输错误或需要软件进行数据拷贝。



## 代码

```c

// 函数声明
uint8_t SDIO_Init(void);
void SDIO_GPIO_Init(void);
void SDIO_ClockConfig(uint8_t clock_div);
void SDIO_SwitchToHighSpeed(void);



#endif /* __SDIO_DRIVER_H */
​```

### SD卡驱动实现 (sdio_driver.c)
​```c
#include "sdio_driver.h"



​```

### 更新Main函数
​```c
#include "stm32f4xx.h"
#include "sdio_driver.h"

int main(void)
{
    uint8_t ret;
    
    // 系统时钟初始化
    SystemInit();
    
    // SDIO硬件初始化
    ret = SDIO_Init();
    if(ret != SDIO_ERROR_NONE)
    {
        // 初始化失败处理
        while(1)
        {
            // 错误指示灯或其他处理
        }
    }
    
    // SD卡初始化
    ret = SD_Init();
    if(ret != SDIO_ERROR_NONE)
    {
        // SD卡初始化失败
        while(1)
        {
            // 错误处理
        }
    }
    
    // SD卡读写测试
    SD_ReadWriteTest();
    
    while(1)
    {
        // 主循环 - 可以定期进行读写测试或其他操作
        for(uint32_t i = 0; i < 1000000; i++); // 简单延时
        SD_ReadWriteTest(); // 定期测试
    }
}

```











## FatFs文件系统移植

如果需要以文件方式读写SD卡，可以移植FatFs模块。

1.  **获取FatFs源码**：从FatFs官网下载源码。
2.  **添加到工程**：将 `ff.c`、`ff.h`、`diskio.c`、`diskio.h` 以及字符编码支持文件（如 `cc936.c`）添加到你的工程。
3.  **实现 `diskio.c` 中的底层接口**：这是移植的关键，主要实现以下函数：
    - `disk_initialize()`：调用你的 `SD_Init()`。
    - `disk_status()`：返回磁盘状态。
    - `disk_read()`：调用你的 `SD_ReadDisk()`。
    - `disk_write()`：调用你的 `SD_WriteDisk()`。
    - `disk_ioctl()`：处理一些控制命令，如获取扇区数量 (`GET_SECTOR_COUNT`)、获取扇区大小 (`GET_BLOCK_SIZE`) 等。

4.  **配置 `ffconf.h`**：根据需要进行配置，例如将 `_CODE_PAGE` 设置为936以支持简体中文，将 `_USE_LFN` 设置为1以使用长文件名等。





## 调试与测试

1.  **初始化检测**：在主循环前调用 `SD_Init()`，根据返回值判断初始化是否成功。
2.  **打印SD卡信息**：初始化成功后，可以打印SD卡类型、容量、制造商ID等信息，便于验证和调试。
3.  **读写测试**：先进行简单的扇区读写测试，确保底层驱动稳定。
4.  **文件操作测试**：移植好FatFs后，使用 `f_mount`、`f_open`、`f_write`、`f_read`、`f_close` 等函数进行文件创建、写入和读取测试。



## 关键点与常见问题

- **电源稳定**：确保给SD卡提供稳定且充足的电源。
- **引脚配置**：**SDIO方式**下，数据线**必须**连接到STM32指定的SDIO专用引脚。
- **时钟配置**：SDIO的时钟 (`SDIO_CK`) 在初始化阶段不能超过400kHz，初始化完成后可提高。
- **数据处理**：**SDIO方式**使用DMA传输时，务必保证数据缓存区**4字节对齐**。
- **错误处理**：在驱动中妥善处理SD卡的响应和错误状态，例如通过 `SD_GetCardStatus()` 函数获取状态。

希望这些信息能帮助你完成STM32F407VGT6的SD卡驱动。如果你在移植过程中遇到具体问题，比如某个函数实现有疑问，欢迎继续提问。