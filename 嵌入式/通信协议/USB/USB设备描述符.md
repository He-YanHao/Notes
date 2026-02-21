# USB设备描述符

USB设备描述符（USB Device Descriptor）是USB设备在与主机通信时提供的关键信息之一，用于描述USB设备的基本信息。

每个USB设备都必须至少提供一个设备描述符，设备描述符的内容包括设备类型、设备功能、支持的USB协议版本等。

它是USB设备在USB总线中被识别和初始化的关键。





## 首次请求的8字节设备描述符

```c
// 主机首次GET_DESCRIPTOR请求只获取前8个字节
uint8_t First8BytesOfDeviceDescriptor[8] = {
    0x12,           // [0] bLength: 描述符长度 = 18字节
    0x01,           // [1] bDescriptorType: 设备描述符 = 0x01
    0x10,           // [2] bcdUSB LSB: USB版本号低字节 = 1.10
    0x01,           // [3] bcdUSB MSB: USB版本号高字节 = 1.10 (即USB 1.1)
    0x00,           // [4] bDeviceClass: 设备类 = 0 (在接口中定义)
    0x00,           // [5] bDeviceSubClass: 设备子类 = 0
    0x00,           // [6] bDeviceProtocol: 设备协议 = 0
    0x40            // [7] bMaxPacketSize0: 端点0最大包大小 = 64字节
};
```





## 设备描述符的结构

设备描述符是一个固定格式的数据块，通常由以下字段组成：

| 位置  | 名称                   | 大小  | 作用                                                         | 典型值                                                       |
| ----- | ---------------------- | ----- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1     | **bLength**            | 1字节 | 描述符的长度                                                 | 设备描述符的固定长度是18字节，故此字段值通常为0x12。         |
| 2     | **bDescriptorType**    | 1字节 | 描述符的类型。                                               | 对于设备描述符，其值为`0x01`。                               |
| 3~4   | **bcdUSB**             | 2字节 | 支持的USB版本。                                              | USB 2.0的值为`0x0200`，USB 3.0的值为`0x0300`。               |
| 5     | **bDeviceClass**       | 1字节 | 设备的类别代码。                                             | 这个字段指示了设备属于哪个类别（如音频、通信、存储等）。如果设备使用接口来定义功能，则此字段为`0x00`，表示不定义设备类。 |
| 6     | **bDeviceSubClass**    | 1字节 | 设备子类代码，表示设备的子类。                               | 如果设备类别是`0x00`（没有设备类），此字段无效。             |
| 7     | **bDeviceProtocol**    | 1字节 | 设备协议代码，表示设备使用的协议。                           | 若设备不指定协议，则此字段通常为`0x00`。                     |
| 8     | **bMaxPacketSize0**    | 1字节 | 设备端点0的最大包大小，表示在控制传输中，设备端点0一次可以传输的最大数据量。 | 常见值是`0x40`，即64字节。                                   |
| 9~10  | **idVendor**           | 2字节 | 设备厂商的ID。                                               | 这个值由USB-IF（USB实施论坛）分配，每个厂商都有一个唯一的ID。 |
| 11~12 | **idProduct**          | 2字节 | 设备的产品ID。                                               | 厂商分配，用于唯一标识某一款产品。                           |
| 13~14 | **bcdDevice**          | 2字节 | 设备的版本号。                                               | 通常用高字节表示主版本号，低字节表示次版本号。               |
| 15    | **iManufacturer**      | 1字节 | 制造商字符串的索引。                                         | 此字段指向一个字符串描述符，该字符串描述了设备的制造商。     |
| 16    | **iProduct**           | 1字节 | 产品字符串的索引。                                           | 指向一个描述符，描述设备的名称或型号。                       |
| 17    | **iSerialNumber**      | 1字节 | 序列号字符串的索引。                                         | 如果设备有序列号，这个字段会指向一个包含设备唯一序列号的字符串描述符。 |
| 18    | **bNumConfigurations** | 1字节 | 设备的配置数量。                                             | 设备可以有多个配置，每个配置可以提供不同的设备功能。         |



### 案例：

```c
__ALIGN_BEGIN uint8_t USBD_HS_DeviceDesc[USB_LEN_DEV_DESC] __ALIGN_END =
{
  0x12,                       //长度为18           /*bLength */
  USB_DESC_TYPE_DEVICE,       //设备类描述符 1U     /*bDescriptorType*/
#if (USBD_LPM_ENABLED == 1)   // USBD_LPM_ENABLED = 0U
  0x01,                       /*bcdUSB */
#else
  0x00,                       /*bcdUSB */
#endif /* (USBD_LPM_ENABLED == 1) */
  0x02,
  0x00,                       //设备类（在接口中定义        /*设备的类别代码。bDeviceClass*/
  0x00,                       //设备子类                  /*bDeviceSubClass*/
  0x00,                       //设备协议                  /*bDeviceProtocol*/
  USB_MAX_EP0_SIZE,           //端点0最大包大小 64U        /*bMaxPacketSize*/
  LOBYTE(USBD_VID),           //厂商ID 1155 0x0483              /*idVendor*/
  HIBYTE(USBD_VID),           //厂商ID 1155 0x0483              /*idVendor*/
  LOBYTE(USBD_PID_HS),        //产品ID（高速模式）22352 0x5750    /*idProduct*/
  HIBYTE(USBD_PID_HS),        //产品ID（高速模式）22352 0x5750    /*idProduct*/
  0x00,                       //设备版本号 2.00            /*bcdDevice rel. 2.00*/
  0x02,                       //设备版本号 2.00
  USBD_IDX_MFC_STR,           //制造商字符串索引 0x01U      /*Index of manufacturer  string*/
  USBD_IDX_PRODUCT_STR,       //产品字符串索引 0x02U        /*Index of product string*/
  USBD_IDX_SERIAL_STR,        //序列号字符串索引 0x03U      /*Index of serial number string*/
  USBD_MAX_NUM_CONFIGURATION  //配置描述符数量 1U           /*bNumConfigurations*/
};
```



## 作用

USB设备描述符为主机提供了有关设备的关键信息，以便进行适当的设备识别、配置和数据交换。USB主机根据设备描述符中的信息来初始化和配置设备，从而进行后续的通讯。



