# USB初始化流程



## MX_USB_DEVICE_Init

```c
//初始化USB驱动
void MX_USB_DEVICE_Init(void)
{
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

