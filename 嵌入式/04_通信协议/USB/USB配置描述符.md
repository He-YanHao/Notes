# USB配置描述符



案例值：

```c
/* USB CUSTOM_HID device FS Configuration Descriptor */
__ALIGN_BEGIN static uint8_t USBD_HID_CfgDesc[USB_HID_CONFIG_DESC_SIZ] __ALIGN_END =
{
    /* Configuration Descriptor */
    0x09,                       //长度9字节    /* bLength: Configuration Descriptor size */
    USB_DESC_TYPE_CONFIGURATION,/* bDescriptorType: Configuration */
    LOBYTE(USB_HID_CONFIG_DESC_SIZ), /* wTotalLength: Total length of data returned */
    HIBYTE(USB_HID_CONFIG_DESC_SIZ),
    0x01,                       /* bNumInterfaces: 1 interface */
    0x01,                       /* bConfigurationValue: Configuration value */
    0x00,                       /* iConfiguration: Index of string descriptor describing the configuration */
#if (USBD_SELF_POWERED == 1U)
    0xC0,                       /* bmAttributes: Self powered */
#else
    0x80,                       /* bmAttributes: Bus powered */
#endif
    USBD_MAX_POWER,             /* MaxPower (mA) */

    /* Interface Descriptor */
    0x09,                       /* bLength: Interface Descriptor size */
    USB_DESC_TYPE_INTERFACE,    /* bDescriptorType: Interface */
    0x00,                       /* bInterfaceNumber */
    0x00,                       /* bAlternateSetting */
    0x02,                       /* bNumEndpoints: 2 endpoints (IN + OUT) */
    USB_CLASS_CODE_HID,         /* bInterfaceClass: HID */
    0x00,                       /* bInterfaceSubClass: 0 = no boot */
    0x00,                       /* bInterfaceProtocol: 0 = none */
    0x00,                       /* iInterface: Index of string descriptor */

    /* HID Descriptor */
    0x09,                       /* bLength: HID Descriptor size */
    HID_CLASS_DESC_HID,          /* bDescriptorType: HID */
    LOBYTE(CUSHID_BCD_NUM),      /* bcdHID (LSB) */
    HIBYTE(CUSHID_BCD_NUM),      /* bcdHID (MSB) */
    0x00,                        /* bCountryCode: Not localized */
    0x01,                        /* bNumDescriptors: 1 report descriptor follows */
    HID_CLASS_DESC_REPORT,       /* bDescriptorType: Report descriptor type */
    LOBYTE(USBD_CUSTOM_HID_REPORT_DESC_SIZE), /* wDescriptorLength (LSB) */
    HIBYTE(USBD_CUSTOM_HID_REPORT_DESC_SIZE), /* wDescriptorLength (MSB) */

    /* Endpoint Descriptor (IN) */
    0x07,                       /* bLength: Endpoint Descriptor size */
    USB_DESC_TYPE_ENDPOINT,     /* bDescriptorType: Endpoint */
    HID_EPIN_ADDR,              /* bEndpointAddress: IN endpoint */
    USB_EPT_DESC_INTERRUPT,     /* bmAttributes: Interrupt */
    LOBYTE(HID_EPIN_SIZE),      /* wMaxPacketSize (LSB) */
    HIBYTE(HID_EPIN_SIZE),      /* wMaxPacketSize (MSB) */
    HID_HS_BINTERVAL,           /* bInterval: Polling interval */

    /* Endpoint Descriptor (OUT) */
    0x07,                       /* bLength: Endpoint Descriptor size */
    USB_DESC_TYPE_ENDPOINT,     /* bDescriptorType: Endpoint */
    HID_EPOUT_ADDR,             /* bEndpointAddress: OUT endpoint */
    USB_EPT_DESC_INTERRUPT,     /* bmAttributes: Interrupt */
    LOBYTE(HID_EPOUT_SIZE),     /* wMaxPacketSize (LSB) */
    HIBYTE(HID_EPOUT_SIZE),     /* wMaxPacketSize (MSB) */
    HID_HS_BINTERVAL            /* bInterval: Polling interval */
};
```

