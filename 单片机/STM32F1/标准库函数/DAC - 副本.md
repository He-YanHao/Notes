```c
#include "stm32f10x.h"
#include "stm32f10x_rcc.h"
#include "stm32f10x_gpio.h"
#include "stm32f10x_dac.h"
#include "stm32f10x_dma.h"
#include "stm32f10x_tim.h"

// 正弦波参数配置
#define SINE_WAVE_POINTS 64     // 一个周期的采样点数
#define DAC_RESOLUTION   4095   // 12位DAC最大值 (2^12 - 1)
#define SINE_FREQUENCY   1000   // 目标正弦波频率 (Hz)

// 预计算正弦波表 (避免运行时浮点计算)
const uint16_t sineWaveTable[SINE_WAVE_POINTS] = {
    2048, 2248, 2447, 2642, 2831, 3012, 3184, 3346, 3496, 3633, 3756, 3864, 3956, 4032, 4091, 4133,
    4158, 4166, 4158, 4133, 4091, 4032, 3956, 3864, 3756, 3633, 3496, 3346, 3184, 3012, 2831, 2642,
    2447, 2248, 2048, 1847, 1648, 1453, 1264, 1083, 911,  749,  599,  462,  339,  231,  139,  63,
    4,    0,    4,    23,   63,   139,  231,  339,  462,  599,  749,  911,  1083, 1264, 1453, 1648
};

// 定时器配置函数 (用于触发DAC转换)
void TIM6_Configuration(void) {
    TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure;
    
    // 1. 计算定时器参数
    // 目标更新频率 = 正弦波频率 × 采样点数
    uint32_t updateFrequency = SINE_FREQUENCY * SINE_WAVE_POINTS;
    
    // 定时器时钟 = APB1时钟 × 2 = 72MHz (因为APB1预分频器=2)
    uint32_t timerClock = 72000000;
    
    // 计算预分频器和周期值
    uint16_t prescaler = 0;
    uint16_t period = (timerClock / updateFrequency) - 1;
    
    // 2. 配置定时器基础参数
    TIM_TimeBaseStructure.TIM_Period = period;           // 自动重装载值
    TIM_TimeBaseStructure.TIM_Prescaler = prescaler;     // 预分频值
    TIM_TimeBaseStructure.TIM_ClockDivision = 0;         // 时钟分割
    TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up; // 向上计数
    
    // 3. 初始化TIM6
    TIM_TimeBaseInit(TIM6, &TIM_TimeBaseStructure);
    
    // 4. 配置TIM6触发输出 (用于触发DAC)
    TIM_SelectOutputTrigger(TIM6, TIM_TRGOSource_Update);
    
    // 5. 使能TIM6
    TIM_Cmd(TIM6, ENABLE);
}

int main(void) {
    // 1. 系统初始化
    DMA_Configuration();  // 配置DMA
    DAC_Configuration();  // 配置DAC (核心初始化)
    TIM6_Configuration(); // 配置定时器触发源
    
    // 2. 启动系统
    // 注意：DAC、DMA和TIM6已在上面的配置函数中使能
    
    while (1) {
        // 主循环无需操作
        // DMA自动将正弦波数据传输到DAC
        // TIM6定时触发DAC转换
    }
}
```

