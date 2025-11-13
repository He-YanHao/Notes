# STM32的SDIO模式

## 硬件配置

### SDIO引脚定义（STM32F407）
```c
// SDIO接口使用以下引脚：
// PC8  - SDIO_D0
// PC9  - SDIO_D1  
// PC10 - SDIO_D2
// PC11 - SDIO_D3
// PC12 - SDIO_CK
// PD2  - SDIO_CMD
```

## SDIO初始化代码

### SDIO和GPIO配置
```c
#include "stm32f4xx.h"
#include "stm32f4xx_sdio.h"
#include "stm32f4xx_rcc.h"
#include "stm32f4xx_gpio.h"
#include "ff.h"
#include "diskio.h"

void SDIO_GPIO_Init(void)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    
    // 使能GPIOC和GPIOD时钟
    RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_GPIOC | RCC_AHB1Periph_GPIOD, ENABLE);
    
    // 配置PC8~PC11为SDIO数据线
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_8 | GPIO_Pin_9 | GPIO_Pin_10 | GPIO_Pin_11;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_100MHz;
    GPIO_InitStructure.GPIO_OType = GPIO_OType_PP;
    GPIO_InitStructure.GPIO_PuPd = GPIO_PuPd_UP;
    GPIO_Init(GPIOC, &GPIO_InitStructure);
    
    // 配置PC12为SDIO时钟
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_12;
    GPIO_Init(GPIOC, &GPIO_InitStructure);
    
    // 配置PD2为SDIO命令线
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2;
    GPIO_Init(GPIOD, &GPIO_InitStructure);
    
    // 连接到SDIO复用功能
    GPIO_PinAFConfig(GPIOC, GPIO_PinSource8, GPIO_AF_SDIO);
    GPIO_PinAFConfig(GPIOC, GPIO_PinSource9, GPIO_AF_SDIO);
    GPIO_PinAFConfig(GPIOC, GPIO_PinSource10, GPIO_AF_SDIO);
    GPIO_PinAFConfig(GPIOC, GPIO_PinSource11, GPIO_AF_SDIO);
    GPIO_PinAFConfig(GPIOC, GPIO_PinSource12, GPIO_AF_SDIO);
    GPIO_PinAFConfig(GPIOD, GPIO_PinSource2, GPIO_AF_SDIO);
}

void SDIO_Init(void)
{
    SDIO_InitTypeDef SDIO_InitStructure;
    
    // 使能SDIO时钟
    RCC_AHB2PeriphClockCmd(RCC_AHB2Periph_SDIO, ENABLE);
    
    SDIO_InitStructure.SDIO_ClockDiv = SDIO_TRANSFER_CLK_DIV; // 通常设置为2-4
    SDIO_InitStructure.SDIO_ClockEdge = SDIO_ClockEdge_Rising;
    SDIO_InitStructure.SDIO_ClockBypass = SDIO_ClockBypass_Disable;
    SDIO_InitStructure.SDIO_ClockPowerSave = SDIO_ClockPowerSave_Disable;
    SDIO_InitStructure.SDIO_BusWide = SDIO_BusWide_4b;
    SDIO_InitStructure.SDIO_HardwareFlowControl = SDIO_HardwareFlowControl_Disable;
    
    SDIO_Init(&SDIO_InitStructure);
    SDIO_SetPowerState(SDIO_PowerState_ON);
    SDIO_ClockCmd(ENABLE);
}
```



## 创建纯白BMP图片

```c
// 创建RGB565格式的纯白BMP
void CreateWhiteBMP_RGB565(uint8_t *buffer, uint32_t width, uint32_t height)
{
    BMP_FileHeader fileHeader;
    BMP_V4Header infoHeader;
    
    uint32_t rowSize = ((width * 16 + 31) / 32) * 4; // 每行字节数（4字节对齐）
    uint32_t imageSize = rowSize * height;
    uint32_t fileSize = sizeof(fileHeader) + sizeof(infoHeader) + imageSize;

    // 填充文件头
    fileHeader.bfType = 0x4D42; // "BM"
    fileHeader.bfSize = fileSize;
    fileHeader.bfReserved1 = 0;
    fileHeader.bfReserved2 = 0;
    fileHeader.bfOffBits = sizeof(fileHeader) + sizeof(infoHeader);
    
    // 填充信息头（BITMAPV4HEADER）
    memset(&infoHeader, 0, sizeof(infoHeader));
    infoHeader.bV4Size = sizeof(infoHeader);
    infoHeader.bV4Width = width;
    infoHeader.bV4Height = -height; // 负数表示正向（从上到下）
    infoHeader.bV4Planes = 1;
    infoHeader.bV4BitCount = 16;    // RGB565
    infoHeader.bV4Compression = 3;  // BI_BITFIELDS
    infoHeader.bV4SizeImage = imageSize;
    infoHeader.bV4XPelsPerMeter = 0;
    infoHeader.bV4YPelsPerMeter = 0;
    infoHeader.bV4ClrUsed = 0;
    infoHeader.bV4ClrImportant = 0;
    infoHeader.bV4RedMask = 0xF800;   // RGB565红色掩码
    infoHeader.bV4GreenMask = 0x07E0; // RGB565绿色掩码
    infoHeader.bV4BlueMask = 0x001F;  // RGB565蓝色掩码
    infoHeader.bV4AlphaMask = 0x0000; // 无Alpha
    infoHeader.bV4CSType = 0x206E6957; // 'Win ' LCS_WINDOWS_COLOR_SPACE

    // 拷贝头信息
    memcpy(buffer, &fileHeader, sizeof(fileHeader));
    memcpy(buffer + sizeof(fileHeader), &infoHeader, sizeof(infoHeader));

    // 填充纯白像素数据
    uint8_t *pixelData = buffer + fileHeader.bfOffBits;
    uint16_t whitePixel = 0xFFFF; // RGB565白色
    
    for(uint32_t y = 0; y < height; y++) {
        uint16_t *row = (uint16_t*)(pixelData + y * rowSize);
        for(uint32_t x = 0; x < width; x++) {
            row[x] = whitePixel;
        }
        // 行末尾填充（如果需要对齐）
        for(uint32_t x = width; x < rowSize / 2; x++) {
            row[x] = 0;
        }
    }
}
```

