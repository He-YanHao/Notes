# DAC

---

## 重置为默认值 DAC_DeInit
```c
void DAC_DeInit(void);
```
- **功能**：将 DAC 外设寄存器重置为默认值
- **参数**：无
- **说明**：
  - 复位 DAC 的所有寄存器
  - 禁用所有 DAC 通道
  - 清除所有配置设置
- **使用场景**：系统初始化或需要完全重置 DAC 时

---

## 初始化 DAC_Init
```c
void DAC_Init(uint32_t DAC_Channel, DAC_InitTypeDef* DAC_InitStruct);
```
- **功能**：初始化指定 DAC 通道
- **参数**：
  - `DAC_Channel`：选择通道 (DAC_Channel_1 或 DAC_Channel_2)
  - `DAC_InitStruct`：指向配置结构的指针
- **配置结构**：
  ```c
  typedef struct {
    uint32_t DAC_Trigger;        // 触发源选择
    uint32_t DAC_WaveGeneration; // 波形生成模式
    uint32_t DAC_LFSRUnmask_TriangleAmplitude; // LFSR掩码/三角波幅度
    uint32_t DAC_OutputBuffer;   // 输出缓冲使能
  } DAC_InitTypeDef;
  ```
- **示例**：
  ```c
  DAC_InitTypeDef dacConfig;
  dacConfig.DAC_Trigger = DAC_Trigger_T6_TRGO;
  dacConfig.DAC_WaveGeneration = DAC_WaveGeneration_None;
  dacConfig.DAC_OutputBuffer = DAC_OutputBuffer_Enable;
  DAC_Init(DAC_Channel_1, &dacConfig);
  ```

---

## 默认值初始化 DAC_StructInit
```c
void DAC_StructInit(DAC_InitTypeDef* DAC_InitStruct);
```
- **功能**：使用默认值初始化 DAC 配置结构
- **参数**：指向配置结构的指针
- **默认值**：
  - Trigger: DAC_Trigger_None
  - WaveGeneration: DAC_WaveGeneration_None
  - OutputBuffer: DAC_OutputBuffer_Enable
- **使用场景**：初始化配置结构前的准备工作

---

## 使能或禁用指定 DAC 通道 DAC_Cmd
```c
void DAC_Cmd(uint32_t DAC_Channel, FunctionalState NewState);
```
- **功能**：使能或禁用指定 DAC 通道
- **参数**：
  - `DAC_Channel`：选择通道
  - `NewState`：ENABLE 或 DISABLE
- **说明**：必须在初始化后调用才能启动 DAC

---

## 使能/禁用 DAC 的 DMA 请求 DAC_DMACmd
```c
void DAC_DMACmd(uint32_t DAC_Channel, FunctionalState NewState);
```
- **功能**：使能/禁用 DAC 的 DMA 请求
- **参数**：
  - `DAC_Channel`：选择通道
  - `NewState`：ENABLE 或 DISABLE
- **使用场景**：与 DMA 配合实现自动数据更新
- **注意**：需先配置 DMA 控制器

---

## 使能/禁用 DAC 软件触发 DAC_SoftwareTriggerCmd
```c
void DAC_SoftwareTriggerCmd(uint32_t DAC_Channel, FunctionalState NewState);
```
- **功能**：使能/禁用 DAC 软件触发
- **参数**：
  - `DAC_Channel`：选择通道
  - `NewState`：ENABLE 或 DISABLE
- **触发方式**：
  ```c
  DAC_SoftwareTriggerCmd(DAC_Channel_1, ENABLE);
  DAC_SetChannel1Data(DAC_Align_12b_R, value); // 写入数据触发转换
  ```

---

## 使能/禁用双 DAC 通道的同步软件触发 DAC_DualSoftwareTriggerCmd
```c
void DAC_DualSoftwareTriggerCmd(FunctionalState NewState);
```
- **功能**：使能/禁用双 DAC 通道的同步软件触发
- **参数**：ENABLE 或 DISABLE
- **使用场景**：需要同时更新两个 DAC 通道时
- **触发方式**：
  ```c
  DAC_DualSoftwareTriggerCmd(ENABLE);
  DAC_SetDualChannelData(align, data2, data1); // 同时触发双通道
  ```

---

## 使能/禁用内置波形生成器 DAC_WaveGenerationCmd
```c
void DAC_WaveGenerationCmd(uint32_t DAC_Channel, uint32_t DAC_Wave, FunctionalState NewState);
```
- **功能**：使能/禁用内置波形生成器
- **参数**：
  - `DAC_Channel`：选择通道
  - `DAC_Wave`：波形类型 (DAC_Wave_Noise 或 DAC_Wave_Triangle)
  - `NewState`：ENABLE 或 DISABLE
- **示例**：生成三角波
  ```c
  DAC_SetChannel1TriangleAmplitude(DAC_TriangleAmplitude_1023);
  DAC_WaveGenerationCmd(DAC_Channel_1, DAC_Wave_Triangle, ENABLE);
  ```

---

## 设置 DAC 通道 1 的转换数据 DAC_SetChannel1Data
```c
void DAC_SetChannel1Data(uint32_t DAC_Align, uint16_t Data);
```
- **功能**：设置 DAC 通道 1 的转换数据
- **参数**：
  - `DAC_Align`：数据对齐方式：
    - DAC_Align_8b_R：8位右对齐
    - DAC_Align_12b_L：12位左对齐
    - DAC_Align_12b_R：12位右对齐（最常用）
  - `Data`：转换值 (0-4095 对应 0-3.3V)
- **对齐方式示例**：
  ```c
  // 12位右对齐（数据位 D11-D0）
  DAC_SetChannel1Data(DAC_Align_12b_R, 2048); // 1.65V输出
  ```

---

## 设置 DAC 通道 2 的转换数据 DAC_SetChannel2Data
```c
void DAC_SetChannel2Data(uint32_t DAC_Align, uint16_t Data);
```
- **功能**：设置 DAC 通道 2 的转换数据
- **参数**：同通道 1
- **注意**：通道 2 对应 PA5 引脚

---

## 同时设置双通道转换数据 DAC_SetDualChannelData
```c
void DAC_SetDualChannelData(uint32_t DAC_Align, uint16_t Data2, uint16_t Data1);
```
- **功能**：同时设置双通道转换数据
- **参数**：
  - `DAC_Align`：对齐方式（需相同）
  - `Data2`：通道 2 数据
  - `Data1`：通道 1 数据
- **使用场景**：需要同步更新双通道输出时
- **数据格式**：
  - 12位右对齐：Data1[11:0] + Data2[11:0]
  - 8位右对齐：Data1[7:0] + Data2[7:0]

---

## 读取 DAC 当前输出值 DAC_GetDataOutputValue
```c
uint16_t DAC_GetDataOutputValue(uint32_t DAC_Channel);
```
- **功能**：读取 DAC 当前输出值（数字形式）
- **参数**：DAC_Channel_1 或 DAC_Channel_2
- **返回值**：当前 DAC 输出值 (0-4095)
- **注意**：读取的是数据寄存器值，不是实际模拟电压
- **使用场景**：
  ```c
  uint16_t currentValue = DAC_GetDataOutputValue(DAC_Channel_1);
  ```

---

### 关键使用流程
1. **初始化配置**
   
   ```c
   DAC_DeInit();
   DAC_Init(DAC_Channel_1, &initStruct);
   DAC_Cmd(DAC_Channel_1, ENABLE);
   ```

2. **数据写入**
   ```c
   // 单次写入
   DAC_SetChannel1Data(DAC_Align_12b_R, 3000);
   
   // 自动更新 (DMA)
   DAC_DMACmd(DAC_Channel_1, ENABLE);
   ```

3. **触发转换**
   ```c
   // 硬件触发 (TIM)
   TIM_Cmd(TIM6, ENABLE);
   
   // 软件触发
   DAC_SoftwareTriggerCmd(DAC_Channel_1, ENABLE);
   ```

---

### 重要注意事项
1. **GPIO 配置**：必须将 PA4 (Ch1)/PA5 (Ch2) 设置为模拟模式
   ```c
   GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN;
   ```

2. **电压参考**：输出范围取决于 VREF+ 引脚电压（通常 3.3V）

3. **建立时间**：输出缓冲禁用时响应更快，但驱动能力下降

4. **双通道同步**：使用 `DAC_DualSoftwareTriggerCmd` 确保同步更新

5. **DMA 配置**：DAC 通道 1 对应 DMA1 通道 3，通道 2 对应 DMA1 通道 4

这些函数提供了完整的 DAC 控制能力，可实现从简单的电压输出到复杂的波形生成等各种应用场景。