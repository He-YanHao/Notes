# 外部SRAM

使用外部SRAM可以极大地扩展STM32的可用内存空间，特别适用于需要大容量数据缓冲区、图形显示缓存或复杂算法的应用。STM32通过**FSMC**或**FMC**控制器来连接外部SRAM。

## 核心流程概述

整个过程可以分为四个主要步骤：

1.  **硬件连接**：将外部SRAM芯片正确连接到STM32的对应引脚。
2.  **外设配置**：使用STM32CubeMX或直接编写代码配置FSMC/FMC控制器。
3.  **软件验证**：编写测试代码，验证SRAM的读写是否正常。
4.  **集成使用**：将外部SRAM地址空间用于动态内存分配或直接存取。



## 硬件连接

首先，确保STM32型号支持FSMC或FMC。通常，带有FMC控制器的是更高性能的系列（如F4, F7, H7）。

需要连接以下几组信号：

*   **地址总线**：`A0~A[x]` -> 连接到SRAM的地址引脚。这决定了可以寻址的空间大小（例如A0~A18对应512KB）。
*   **数据总线**：`D0~D15` -> 连接到SRAM的数据引脚。可以是8位、16位。
*   **控制信号**：
    *   `NE1/NE2/...`：片选信号。选择要访问的存储块。
    *   `NOE`：输出使能（读使能）。
    *   `NWE`：写使能。
    *   `NBL0, NBL1`：字节使能（在16位模式下有用）。
*   **其它**：
    *   将SRAM的`/BYTE`引脚通常接高电平（选择16位模式）。
    *   将SRAM的`/RESET`引脚接高电平（如果存在）。

**重要提示**：

*   仔细查阅STM32的数据手册和SRAM芯片的数据手册，确保引脚一一对应。
*   **地址线A0的连接**：对于16位宽度的SRAM，STM32的地址线`A[0]`通常不连接，而是从`A[1]`开始连接到SRAM的`A[0]`。这是因为STM32是按字节寻址的，而16位设备是按半字寻址的。这在配置地址映射时至关重要。



## 外设配置（以STM32CubeMX和HAL库为例）

这是最关键的一步。我们以16位宽度的SRAM连接到STM32F407的**Bank 1**为例。

1.  **开启FMC控制器**：
    *   在STM32CubeMX中，找到`FMC`功能。
    *   选择`FMC` -> `SRAM`。

2.  **配置FMC参数**：
    *   **Bank**: 选择你硬件连接对应的片选，例如`Bank 1`（对应`NE1`）。
    *   **Memory type**: `SRAM`。
    *   **Data**: `16 bits`。
    *   **Address setup time**: 根据SRAM时序手册设置（例如1个HCLK周期）。如果SRAM速度快，可以设小；如果稳定性有问题，就设大。
    *   **Data setup time**: 同上（例如1个周期）。
    *   **Bus turn around time**: 通常设为0，除非有特殊时序要求。

3.  **确定基地址**：
    *   FMC会将外部存储器映射到STM32的固定内存地址。例如，Bank 1的起始地址是`0x6000 0000`。
    *   你的SRAM的起始地址就是这个基地址。例如，如果你使用Bank 1，起始地址就是`0x6000 0000`。

4.  **生成代码**：
    *   配置好时钟树等基本参数后，生成代码。CubeMX会自动生成FMC的初始化代码`MX_FMC_Init()`。



## 软件验证

生成代码后，可以编写一个简单的测试函数来验证SRAM是否工作正常。

```c
// 定义SRAM的基地址，根据你使用的Bank确定
#define EXT_SRAM_ADDR    ((volatile uint16_t*)0x60000000)
#define EXT_SRAM_SIZE    (1024 * 64) // 例如，64K x 16bit = 128KB

// SRAM测试函数
uint8_t SRAM_Test(void) {
    volatile uint16_t *sram = EXT_SRAM_ADDR;
    uint32_t i;

    // 写入测试模式： marching 1's 或简单的递增模式
    for (i = 0; i < EXT_SRAM_SIZE; i++) {
        sram[i] = (uint16_t)(i & 0xFFFF); // 写入数据
    }

    // 回读验证
    for (i = 0; i < EXT_SRAM_SIZE; i++) {
        if (sram[i] != (uint16_t)(i & 0xFFFF)) {
            // 验证失败
            return 0; // 失败
        }
    }
    return 1; // 成功
}

// 在main函数中调用
int main(void) {
    HAL_Init();
    SystemClock_Config();
    MX_FMC_Init();

    if (SRAM_Test()) {
        printf("External SRAM Test PASSED!\n");
    } else {
        printf("External SRAM Test FAILED!\n");
        while(1);
    }

    // ... 其他应用代码
}
```



## 集成使用

验证通过后，就可以在项目中使用这片外部SRAM了。主要有两种方式：

### 方式一：直接指针访问

这是最简单直接的方式，就像操作普通数组一样。

```c
// 向外部SRAM的指定偏移位置写入一个16位数据
*(__IO uint16_t*)(0x60000000 + offset) = my_data;

// 从外部SRAM读取数据
my_data = *(__IO uint16_t*)(0x60000000 + offset);
```



### 方式二：作为堆内存（推荐用于动态分配）

这是更高级和自动化的用法。通过修改链接脚本，将`malloc`、`free`等函数管理的堆空间分配到外部SRAM。

**以Keil MDK为例**：

1.  修改启动文件（`startup_stm32f407xx.s`）中的堆栈设置（不常用，因为内部SRAM的堆栈性能更好）。
2.  **更常用的方法**：创建一个特殊的内存管理区。
    *   使用`malloc`/`free`的替代方案，如`mem1`和`mem2`。
    *   或者，使用HAL库提供的`malloc`/`free`函数，并重写`_sbrk`函数，但这比较复杂。

**更实用的方法：使用自定义的内存分配器**

你可以创建一个专门管理外部SRAM的分配器。

```c
// ext_sram_mem.c
#define EXT_SRAM_BASE 0x60000000
#define EXT_SRAM_SIZE (128 * 1024) // 128KB

static uint8_t sram_pool[EXT_SRAM_SIZE] __attribute__((at(EXT_SRAM_BASE))); // Keil语法
// 对于GCC，通常在链接脚本中定义

// 然后你可以使用这个 sram_pool 来实现你自己的 malloc/free，
// 或者直接把它作为一个大的全局数组使用。
```

**对于GCC编译器（如STM32CubeIDE）**：
你需要修改链接脚本（`.ld`文件），定义一个额外的内存区域（`EXTRAM`）和一个专门的段（`.sram`），然后将特定的变量或堆分配到那个段。

```ld
/* 在MEMORY部分添加 */
MEMORY
{
  RAM (xrw)   : ORIGIN = 0x20000000, LENGTH = 128K   /* 内部RAM */
  EXTRAM (xrw): ORIGIN = 0x60000000, LENGTH = 128K  /* 外部SRAM */
}

/* 在SECTIONS部分添加 */
._user_sram :
{
    . = ALIGN(4);
    _sram_start = .;
    *(.sram)
    *(.sram*)
    . = ALIGN(4);
    _sram_end = .;
} >EXTRAM
```

然后在代码中，你可以将变量指定到外部SRAM：

```c
uint16_t large_buffer[50000] __attribute__((section(".sram")));
```



## 常见问题与调试技巧

1.  **无法读写，全是0xFF或0x00**：
    *   **检查硬件连接**：尤其是片选`NE1`、读写使能`NOE`/`NWE`是否接对。
    *   **检查时序配置**：尝试增加`Address setup time`和`Data setup time`。
    *   **检查电源和地**：确保SRAM芯片供电稳定。
    *   **检查地址线A0**：确认STM32的A1是否连接到了SRAM的A0。

2.  **部分数据错误**：
    *   **检查数据线**：可能有虚焊或短路，特别是D0-D15。
    *   **检查时序**：SRAM访问速度可能跟不上STM32的系统时钟，适当降低FMC时钟或增加等待周期。
    *   **检查电磁干扰**：在高速情况下，总线需要加串阻。

3.  **使用逻辑分析仪或示波器**：这是最强大的调试工具。直接探测`NOE`、`NWE`、`NE1`和数据线、地址线，看波形是否符合SRAM数据手册的时序要求。



## 总结

| 步骤            | 关键内容                                 | 工具/方法        |
| :-------------- | :--------------------------------------- | :--------------- |
| **1. 硬件连接** | 地址/数据/控制总线，片选，A0处理         | 原理图，数据手册 |
| **2. 外设配置** | FMC模式、数据宽度、时序参数              | STM32CubeMX      |
| **3. 软件验证** | 读写测试函数，验证数据完整性             | 自定义测试代码   |
| **4. 集成使用** | 直接指针访问，或修改链接脚本用于动态分配 | 编译器链接脚本   |

