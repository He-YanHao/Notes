# USB初始化流程



## MX_USB_DEVICE_Init

```c
/*成功案例main函数前有一段代码*/

//初始化USB驱动
void MX_USB_DEVICE_Init(void)
{
    
    /*成功案例这里有一段代码*/
    
    //USB设备初始化
    if (USBD_Init(&hUsbDeviceFS, &HS_Desc, DEVICE_FS) != USBD_OK)
    {
        Error_Handler();
    }
    //注册设备类
    if (USBD_RegisterClass(&hUsbDeviceHS, &USBD_HID) != USBD_OK)
    {
        Error_Handler();
    }
    //注册HID接口
    if (USBD_HID_RegisterInterface(&hUsbDeviceHS, &USBD_HID_fops_HS) != USBD_OK)
    {
        Error_Handler();
    }
    //启动USB设备
    if (USBD_Start(&hUsbDeviceHS) != USBD_OK)
    {
        Error_Handler();
    }
}
```



## USB设备初始化 USBD_Init

初始化USB设备栈的软件层面。

```c
USBD_Init(&hUsbDeviceFS, &HS_Desc, DEVICE_FS);
//保存到hUsbDeviceFS句柄，使用HS_Desc初始化，使用DEVICE_FS模式。

USBD_DescriptorsTypeDef HS_Desc =
{
  USBD_HS_DeviceDescriptor          //
, USBD_HS_LangIDStrDescriptor       //
, USBD_HS_ManufacturerStrDescriptor //
, USBD_HS_ProductStrDescriptor      //
, USBD_HS_SerialStrDescriptor       //
, USBD_HS_ConfigStrDescriptor       //
, USBD_HS_InterfaceStrDescriptor    //
#if (USBD_LPM_ENABLED == 1)
, USBD_HS_USR_BOSDescriptor         //
#endif /* (USBD_LPM_ENABLED == 1) */
};

uint8_t * USBD_HS_DeviceDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)
{
  UNUSED(speed);    //访问变量但不使用 消除警告
  *length = sizeof(USBD_HS_DeviceDesc);//计算USB设备描述符长度
  return USBD_HS_DeviceDesc;   //返回指针
}
uint8_t * USBD_HS_LangIDStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)
{
  UNUSED(speed);
  *length = sizeof(USBD_LangIDDesc);
  return USBD_LangIDDesc;
}
uint8_t * USBD_HS_ManufacturerStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)
{
  UNUSED(speed);
  USBD_GetString((uint8_t *)USBD_MANUFACTURER_STRING, USBD_StrDesc, length);
  return USBD_StrDesc;
}

/** USB standard device descriptor. */
__ALIGN_BEGIN uint8_t USBD_HS_DeviceDesc[USB_LEN_DEV_DESC] __ALIGN_END = //USB设备描述符
{
  0x12,                       //描述符长度
  USB_DESC_TYPE_DEVICE,       //设备描述符
#if (USBD_LPM_ENABLED == 1)
  0x01,                       //标准USB2.0
#else
  0x00,                       
#endif /* (USBD_LPM_ENABLED == 1) */
  0x02,                       //标准USB2.0
  0x00,                       //设备类由接口决定
  0x00,                       //设备类由接口决定
  0x00,                       //设备类由接口决定
  USB_MAX_EP0_SIZE,           //每次控制传输最大64字节
  LOBYTE(USBD_VID),           //VID
  HIBYTE(USBD_VID),           //VID
  LOBYTE(USBD_PID_HS),        //PID
  HIBYTE(USBD_PID_HS),        //PID
  0x00,                       //固件版本 2.00（BCD编码），
  0x02,
  USBD_IDX_MFC_STR,           /*Index of manufacturer  string*/
  USBD_IDX_PRODUCT_STR,       /*Index of product string*/
  USBD_IDX_SERIAL_STR,        /*Index of serial number string*/
  USBD_MAX_NUM_CONFIGURATION  //设备支持的配置数。
};
地址	值	含义
00	0x12	bLength = 18
01	0x01	bDescriptorType = DEVICE
02	0x00	bcdUSB (low)
03	0x02	bcdUSB (high) → USB 2.00
04	0x00	bDeviceClass
05	0x00	bDeviceSubClass
06	0x00	bDeviceProtocol
07	0x40	bMaxPacketSize0 = 64
08	0x83	idVendor (low)
09	0x04	idVendor (high)
10	0x50	idProduct (low)
11	0x57	idProduct (high)
12	0x00	bcdDevice (low)
13	0x02	bcdDevice (high)
14	0x01	iManufacturer = 1
15	0x02	iProduct = 2
16	0x03	iSerialNumber = 3
17	0x01	bNumConfigurations = 1
```



## 注册设备类 USBD_RegisterClass



```c
USBD_RegisterClass(&hUsbDeviceHS, &USBD_HID)
```



## 注册HID接口 USBD_HID_RegisterInterface



```c
USBD_HID_RegisterInterface(&hUsbDeviceHS, &USBD_HID_fops_HS)
```





## 启动USB设备 USBD_Start



```c
USBD_Start(&hUsbDeviceHS)
```









## 文件

```
Core/
└── Src/
    └── main.c                            ← 应用入口层

USB_DEVICE/
├── App/
│   ├── usb_device.c                      ← USB设备初始化控制层
│   └── usbd_desc.c                       ← 设备描述符层 (告诉PC“我是谁”)
├── Target/
│   └── usbd_conf.c                       ← 底层硬件接口层 (HAL与USB库之间)

Middlewares/
└── ST/
    └── STM32_USB_Device_Library/
        ├── Core/Src/usbd_core.c          ← USB协议核心调度层
        └── Class/HID/Src/usbd_hid.c      ← HID类功能实现层
```





```C
USBD_ClassTypeDef USBD_COMPOSITE_HID =
{

    USBD_COMPOSITE_EP0_RxReady,
    USBD_COMPOSITE_DataIn,
    USBD_COMPOSITE_DataOut,
    NULL, NULL, NULL,
    USBD_COMPOSITE_GetHSCfgDesc,
    USBD_COMPOSITE_GetFSCfgDesc,
    USBD_COMPOSITE_GetOtherSpeedCfgDesc,
    USBD_COMPOSITE_GetDeviceQualifierDesc,
};
```

