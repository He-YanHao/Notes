# STM32F103C8T6基于HAL库初始化为标准HID设备

## STM32MX选项

外部高速时钟源，选择FS模式，USB时钟要精确为48MHz。



## 初始化函数

```c
void MX_USB_DEVICE_Init(void)
{
  //
  if (USBD_Init(&hUsbDeviceFS, &FS_Desc, DEVICE_FS) != USBD_OK)
  {
    Error_Handler();
  }
  //
  if (USBD_RegisterClass(&hUsbDeviceFS, &USBD_HID) != USBD_OK)
  {
    Error_Handler();
  }
  //
  if (USBD_Start(&hUsbDeviceFS) != USBD_OK)
  {
    Error_Handler();
  }
}
```

