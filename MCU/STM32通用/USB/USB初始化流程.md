# USB初始化流程



## MX_USB_DEVICE_Init

```c
void MX_USB_DEVICE_Init(void)
{
  if (USBD_Init(&hUsbDeviceFS, &HS_Desc, DEVICE_FS) != USBD_OK)
  {
    Error_Handler();
  }
  if (USBD_RegisterClass(&hUsbDeviceHS, &USBD_HID) != USBD_OK)
  {
    Error_Handler();
  }
  if (USBD_HID_RegisterInterface(&hUsbDeviceHS, &USBD_HID_fops_HS) != USBD_OK)
  {
    Error_Handler();
  }
  if (USBD_Start(&hUsbDeviceHS) != USBD_OK)
  {
    Error_Handler();
  }
}
```



## USB设备初始化 USBD_Init

初始化USB设备栈的软件层面。

```c
USBD_StatusTypeDef USBD_Init(USBD_HandleTypeDef *pdev,
                             USBD_DescriptorsTypeDef *pdesc, uint8_t id)
{
  USBD_StatusTypeDef ret;

  /* Check whether the USB Host handle is valid */
  if (pdev == NULL)
  {
#if (USBD_DEBUG_LEVEL > 1U)
    USBD_ErrLog("Invalid Device handle");
#endif /* (USBD_DEBUG_LEVEL > 1U) */
    return USBD_FAIL;
  }

#ifdef USE_USBD_COMPOSITE
  /* Parse the table of classes in use */
  for (uint32_t i = 0; i < USBD_MAX_SUPPORTED_CLASS; i++)
  {
    /* Unlink previous class*/
    pdev->pClass[i] = NULL;
    pdev->pUserData[i] = NULL;

    /* Set class as inactive */
    pdev->tclasslist[i].Active = 0;
    pdev->NumClasses = 0;
    pdev->classId = 0;
  }
#else
  /* Unlink previous class*/
  pdev->pClass[0] = NULL;
  pdev->pUserData[0] = NULL;
#endif /* USE_USBD_COMPOSITE */

  pdev->pConfDesc = NULL;

  /* Assign USBD Descriptors */
  if (pdesc != NULL)
  {
    pdev->pDesc = pdesc;
  }

  /* Set Device initial State */
  pdev->dev_state = USBD_STATE_DEFAULT;
  pdev->id = id;

  /* Initialize low level driver */
  ret = USBD_LL_Init(pdev);

  return ret;
}
```







## 注册设备类 USBD_RegisterClass



```c

```



## 注册HID接口 USBD_HID_RegisterInterface



```c

```





## 启动USB设备 USBD_Start



```c

```









