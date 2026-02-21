# USB端点



USB端点是由多个因素共同决定的，主要包括以下几个方面：

## 1. 硬件限制

### 控制器硬件能力
```c
// STM32 USB OTG控制器通常支持：
- 多个IN端点（设备到主机）
- 多个OUT端点（主机到设备）  
- 端点0（控制端点）必须存在
- 有限的端点缓冲区大小和数量
```

## 2. 端点描述符定义

### 在接口描述符中配置
```c
// 端点描述符结构
typedef struct {
  uint8_t  bLength;          // 描述符长度
  uint8_t  bDescriptorType;  // 端点描述符类型(0x05)
  uint8_t  bEndpointAddress; // 端点地址和方向
  uint8_t  bmAttributes;     // 端点类型
  uint16_t wMaxPacketSize;   // 最大包大小
  uint8_t  bInterval;        // 轮询间隔
} USB_EpDescTypeDef;
```

### 端点地址决定
```c
// bEndpointAddress 字段：
Bit 7:    方向 (0=OUT, 1=IN)
Bit 6-4:  保留
Bit 3-0:  端点号 (0-15)

// 示例：
0x81 = 10000001b → IN端点1
0x01 = 00000001b → OUT端点1
0x82 = 10000010b → IN端点2
```

## 3. 端点类型决定

### bmAttributes 字段：
```c
Bits 1-0: 传输类型
  00 = Control     (控制传输)
  01 = Isochronous (等时传输) 
  10 = Bulk        (批量传输)
  11 = Interrupt   (中断传输)

Bits 3-2: 同步类型（仅等时传输）
Bits 5-4: 用法类型（仅等时传输）
```

## 4. 实际配置示例

```c
// HID键盘的端点配置示例
__ALIGN_BEGIN static uint8_t HID_EPIN_Desc[USB_ENDPOINT_DESC_SIZE] __ALIGN_END = {
  0x07,          /* bLength: Endpoint Descriptor size */
  USB_DESC_TYPE_ENDPOINT, /* bDescriptorType: Endpoint */
  0x81,          /* bEndpointAddress: IN endpoint 1 */
  0x03,          /* bmAttributes: Interrupt endpoint */
  LOBYTE(HID_EPIN_SIZE), /* wMaxPacketSize: */
  HIBYTE(HID_EPIN_SIZE),
  HID_HS_BINTERVAL, /* bInterval: Polling Interval */
};

__ALIGN_BEGIN static uint8_t HID_EPOUT_Desc[USB_ENDPOINT_DESC_SIZE] __ALIGN_END = {
  0x07,          /* bLength: Endpoint Descriptor size */
  USB_DESC_TYPE_ENDPOINT, /* bDescriptorType: Endpoint */
  0x01,          /* bEndpointAddress: OUT endpoint 1 */
  0x03,          /* bmAttributes: Interrupt endpoint */
  LOBYTE(HID_EPOUT_SIZE), /* wMaxPacketSize: */
  HIBYTE(HID_EPOUT_SIZE),
  HID_HS_BINTERVAL, /* bInterval: Polling Interval */
};
```

## 5. 决定因素总结

| 决定因素         | 说明                                        |
| ---------------- | ------------------------------------------- |
| **设备功能**     | 设备类型决定需要哪些端点（HID、MSC、CDC等） |
| **数据传输需求** | 数据量、实时性要求决定端点类型和数量        |
| **USB规范**      | 不同设备类有标准的端点要求                  |
| **硬件资源**     | 控制器支持的端点数量和缓冲区大小            |
| **性能要求**     | 带宽需求决定端点类型和最大包大小            |

## 6. 常见设备端点配置

```c
// USB串口设备(CDC)
- 端点0: 控制传输
- 端点1 IN:  中断传输（通知）
- 端点2 IN:  批量传输（数据发送）
- 端点2 OUT: 批量传输（数据接收）

// USB大容量存储设备(MSC)  
- 端点0: 控制传输
- 端点1 IN:  批量传输（数据发送）
- 端点1 OUT: 批量传输（数据接收）

// USB HID设备
- 端点0: 控制传输
- 端点1 IN: 中断传输（数据上报）
```

**总结**：USB端点由设备功能需求、USB规范要求、硬件资源限制共同决定，通过端点描述符在枚举过程中告知主机。