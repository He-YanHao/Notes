# STM32存储器架构概述

STM32采用**哈佛架构**，代码存储（Flash）和数据存储（SRAM）分开，同时通过**存储器映射**将外设寄存器也映射到统一的地址空间中。

## 主要地址区域划分

| 地址范围                    | 区域类型         | 用途说明                  |
| --------------------------- | ---------------- | ------------------------- |
| `0x0000 0000 - 0x1FFF FFFF` | 代码区           | Flash存储器、系统存储器等 |
| `0x2000 0000 - 0x3FFF FFFF` | SRAM区           | 数据存储器                |
| `0x4000 0000 - 0x5FFF FFFF` | 外设区           | 外设寄存器                |
| `0x6000 0000 - 0x9FFF FFFF` | 外部存储器       | FSMC/FMC控制的设备        |
| `0xA000 0000 - 0xDFFF FFFF` | 保留             | -                         |
| `0xE000 0000 - 0xFFFF FFFF` | Cortex-M内核外设 | NVIC、SysTick等           |

## 代码区

### 核心概念

STM32的代码区，通常指的是用于存储和执行程序代码的**主闪存存储器**。一般是芯片内部Flash，属于**非易失性存储器**，意味着断电后存储的内容不会丢失。

从ARM Cortex-M内核的视角看，这个区域被映射到一个固定的地址空间。

### 物理介质与内存映射地址

代码区的物理载体是STM32芯片内部的Flash内存。编译生成的二进制程序文件（通常是`.bin`或`.hex`文件）就是通过烧录工具下载到这个区域。

不同型号的STM32，其内部Flash容量各不相同，从几KB到几MB不等（例如STM32F103C8T6有64KB，而STM32H7系列可达2MB）。这在芯片的数据手册中有明确说明。

在STM32的存储器映射中，代码区通常被映射到地址 **`0x0800 0000`**。这是绝大部分STM32型号的默认启动地址。

代码区占据的地址范围是 `0x0800 0000` 到 `(0x0800 0000 + Flash Size - 1)`。例如，一个512KB Flash的芯片，其代码区地址范围是 `0x0800 0000` ~ `0x0807 FFFF`。

代码区理论极限大小是512MB，但硬件一般只存在不足1MB。

### 执行总线

*   **执行总线**：Cortex-M内核通过**I-Code总线**和**D-Code总线**来访问代码区，以实现高效的**哈佛架构**。
    *   **I-Code总线**：专门用于**取指**（Fetching Instructions）。内核通过这条总线从代码区读取要执行的指令。
    *   **D-Code总线**：专门用于**访问存储在代码区的常量数据**（例如 `const` 变量、查找表等）。
*   **性能优势**：这种分离的总线结构允许内核同时进行取指和数据访问，减少了总线冲突，提高了执行效率。

### 存储的内容

代码区不仅仅存储程序指令，它还包含以下几个关键部分：

*   **中断向量表**：这是代码区最开始的部分（位于 `0x0800 0000`）。它包含了一个指针数组，第一个元素是**主堆栈指针**的初始值，第二个元素是**复位向量**（程序开始执行的地址），后面依次是所有中断服务函数的入口地址。芯片上电或复位后，首先会从这里加载MSP和PC。
*   **程序代码**：编译后的机器指令，即你的C/C++或汇编代码。
*   **常量数据**：所有被 `const` 关键字修饰的全局变量和静态变量都存储在这里。例如 `const uint8_t my_lookup_table[] = {1,2,3};`。
*   **文本常量**：程序中的字符串字面量（如 `"Hello, World!"`）也存储在这里。



### 总结与要点

**代码区就是STM32内部的那块Flash，你的整个程序都存放在这里，芯片上电后就从这里的`0x0800 0000`地址开始执行。它不仅存放代码，还存放了决定程序如何启动的中断向量表和所有只读的常量。**



## SRAM区详解

### SRAM区的基本信息

- **起始地址**：`0x2000 0000`
- **大小**：根据不同型号从几KB到几百KB不等
- **特性**：易失性存储器，掉电后数据丢失

### SRAM的组织结构

STM32的SRAM通常分为多个块：

**典型的分块结构（以STM32F4为例）：**

```c
0x2000 0000 - 0x2001 FFFF     // 主SRAM (128KB)
0x2002 0000 - 0x2002 3FFF     // CCM RAM (64KB) - 仅CPU可访问，无DMA
```

**更复杂的分块（如STM32H7）：**

```c
0x2000 0000 - 0x2001 FFFF     // DTCM RAM (128KB) - 零等待周期
0x2400 0000 - 0x2407 FFFF     // AXI SRAM (512KB)
0x3000 0000 - 0x3001 FFFF     // AHB SRAM (128KB)
0x3800 0000 - 0x3800 FFFF     // Backup SRAM (64KB)
```

### SRAM的用途

#### 程序运行时数据
```c
// 1. 全局变量和静态变量
uint32_t global_var;           // 已初始化数据段
static uint32_t static_var;    // 静态变量

// 2. 栈空间（Stack）
void function() {
    int local_var;             // 局部变量，在栈上分配
    int array[100];            // 大数组可能造成栈溢出
}

// 3. 堆空间（Heap）
int *ptr = malloc(1024);       // 动态内存分配
```

#### 链接脚本中的SRAM分配
```c
/* 链接脚本片段 */
MEMORY
{
  RAM (xrw)      : ORIGIN = 0x20000000, LENGTH = 128K
  CCMRAM (xrw)   : ORIGIN = 0x10000000, LENGTH = 64K
}

SECTIONS
{
  .data : { *(.data) } > RAM
  .bss : { *(.bss) } > RAM
  .heap : { . = ALIGN(8); __heap_start = .; } > RAM
  .stack : { . = ALIGN(8); __stack_top = .; } > RAM
}
```

### SRAM访问特性

#### 访问速度：
```c
// 不同SRAM块的访问速度可能不同
volatile uint32_t *dtcm_ram = (uint32_t*)0x20000000;    // 零等待周期
volatile uint32_t *axi_ram = (uint32_t*)0x24000000;     // 可能有缓存

// 性能关键代码应放在快速SRAM中
__attribute__((section(".fast_ram"))) void critical_function() {
    // 时间敏感的操作
}
```

#### DMA访问限制：
```c
// CCM RAM只能被CPU访问，不能用于DMA
uint32_t dma_buffer[1024] __attribute__((section(".ccmram")));  // 错误！DMA无法访问

// 正确的DMA缓冲区应放在主SRAM中
uint32_t dma_buffer[1024] __attribute__((section(".dma_buffer")));  // 主SRAM区域
```



## 外设区详解

### 外设区的基本信息

- **起始地址**：`0x4000 0000`
- **大小**：512MB的地址空间
- **特性**：内存映射的外设寄存器

### 外设地址计算

```c
// 外设基地址 + 总线偏移 + 外设偏移 + 寄存器偏移

// 以GPIOA为例：
#define PERIPH_BASE        0x40000000U                     //外设基地址
#define APB2PERIPH_BASE    (PERIPH_BASE + 0x10000U)        //总线偏移
#define GPIOA_BASE         (APB2PERIPH_BASE + 0x0800U)     //外设偏移

// GPIO寄存器偏移
#define GPIO_CRL_OFFSET    0x00U    // 端口配置低寄存器
#define GPIO_CRH_OFFSET    0x04U    // 端口配置高寄存器  
#define GPIO_IDR_OFFSET    0x08U    // 输入数据寄存器
#define GPIO_ODR_OFFSET    0x0CU    // 输出数据寄存器

// 完整的寄存器地址
#define GPIOA_CRL         (*(volatile uint32_t*)(GPIOA_BASE + GPIO_CRL_OFFSET))//寄存器偏移
```



### 外设寄存器访问

#### 直接地址访问：
```c
// 配置GPIOA Pin5为输出
uint32_t *gpioa_crl = (uint32_t*)0x40010800;
*gpioa_crl &= ~(0xF << 20);     // 清除配置位
*gpioa_crl |= (0x3 << 20);      // 推挽输出，50MHz
```

#### 结构体方式访问（推荐）：
```c
typedef struct {
    volatile uint32_t CRL;     // 0x00
    volatile uint32_t CRH;     // 0x04  
    volatile uint32_t IDR;     // 0x08
    volatile uint32_t ODR;     // 0x0C
    volatile uint32_t BSRR;    // 0x10
    volatile uint32_t BRR;     // 0x14
    volatile uint32_t LCKR;    // 0x18
} GPIO_TypeDef;

#define GPIOA ((GPIO_TypeDef*)GPIOA_BASE)

// 使用结构体访问
GPIOA->CRL |= (0x3 << 20);     // 配置PA5
GPIOA->BSRR = (1 << 5);        // 设置PA5高电平
```

## 外部存储器

FSMC/FMC控制的设备

可以扩充SRAM空间

## Cortex-M内核外设



## 实际应用示例

### 1. 内存管理示例
```c
#include "stm32f4xx.h"

// 定义不同内存区域的变量
__attribute__((section(".dtcm"))) uint32_t fast_data[256];     // DTCM RAM，快速访问
__attribute__((section(".sram"))) uint8_t dma_buffer[4096];    // 主SRAM，DMA可用
__attribute__((section(".ccm"))) uint32_t critical_data[128];  // CCM RAM，仅CPU访问

// 外设配置函数
void GPIO_Config(void) {
    // 1. 使能GPIOA时钟（访问外设区）
    RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;
    
    // 2. 配置PA5为输出（访问GPIO寄存器）
    GPIOA->MODER &= ~GPIO_MODER_MODER5;
    GPIOA->MODER |= GPIO_MODER_MODER5_0;  // 输出模式
    
    // 3. 设置PA5输出高电平
    GPIOA->BSRR = GPIO_BSRR_BS5;
}

// 内存操作函数
void Memory_Operations(void) {
    // 在SRAM区进行数据操作
    for(int i = 0; i < 256; i++) {
        fast_data[i] = i;          // 快速SRAM访问
        dma_buffer[i] = i & 0xFF;  // 主SRAM访问
    }
    
    // 使用DMA从外设传输数据到SRAM
    // DMA1->CPAR = (uint32_t)&(SPI1->DR);   // 外设地址
    // DMA1->CMAR = (uint32_t)dma_buffer;    // 内存地址
}
```

### 2. 性能优化示例
```c
// 将性能关键代码和数据放在快速内存中
__attribute__((section(".fast_code"))) 
__attribute__((optimize("O3"))) 
void DSP_Processing(void) {
    // 使用DTCM RAM进行快速数据处理
    for(int i = 0; i < 256; i++) {
        critical_data[i] = complex_algorithm(fast_data[i]);
    }
}

// 链接脚本配置对应的内存区域
/* 在链接脚本中定义 */
.fast_code : {
    *(.fast_code)
} > ITCM_RAM AT> FLASH

.dtcm : {
    *(.dtcm)
} > DTCM_RAM
```

---

## 六、调试技巧和注意事项

### 1. 内存访问检查
```c
// 检查地址是否在有效范围内
bool is_valid_sram_address(uint32_t addr) {
    return (addr >= 0x20000000 && addr < 0x20000000 + SRAM_SIZE);
}

bool is_valid_periph_address(uint32_t addr) {
    return (addr >= 0x40000000 && addr < 0x50000000);
}
```

### 2. 总线错误调试
```c
// 检查HardFault中的总线错误
void HardFault_Handler(void) {
    uint32_t *cfsr = (uint32_t*)0xE000ED28;
    if (*cfsr & (1 << 7)) {  // 检查BFARVALID
        uint32_t *bfar = (uint32_t*)0xE000ED38;
        printf("Bus fault at address: 0x%08lX\n", *bfar);
    }
    while(1);
}
```

### 3. 内存保护单元配置（高级应用）
```c
// 配置MPU保护不同的内存区域
void MPU_Config(void) {
    // 保护外设区为只读（防止意外写入）
    MPU->RBAR = 0x40000000;  // 外设基地址
    MPU->RASR = MPU_RASR_ENABLE_Msk | 
                (0x18 << MPU_RASR_AP_Pos) |  // PRIV:RW, USER:RO
                (0xB << MPU_RASR_TEX_Pos);   // 设备内存属性
}
```







会指向0x0800xxxx这样的地址，因为那就是代码真正所在的地方。