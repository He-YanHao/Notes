# ESP32升级



## OTA升级

OTA：Over-The-Air，空中升级，不连接线缆的无接触式升级均属于OTA。

因此，WIFI，蓝牙升级均属于OTA。



## 升级数据来源

### 非OTA类

- **串口（UART）**
- **SD/TF卡**
- **SPI/I2C 等接口从外部设备获取**

### OTA类

- **HTTP/HTTPS服务器**
- **蓝牙BLE**
- **Web服务器**



## Flash分区结构

| 逻辑分区        | 起始地址 | 名称                      | **主要功能**                    |
| --------------- | -------- | ------------------------- | ------------------------------- |
| Bootloader      | 0x0000   | 二级Bootloader            | 执行芯片初始化                  |
| Partition Table | 0x8000   | 分区表                    | 保存Flash分区信息               |
| NVS             | 0x9000   | 非易失性存储              | 存储需要持久化的配置数据        |
| OTA-Data        | 自定义   | OTA数据                   | 记录OTA升级状态                 |
| phy_init        | 自定义   | 存储Wi-Fi射频的初始化参数 | 初始化硬件                      |
| factory         | 自定义   | 出场固件/安全固件         | 做为最后回退的手段              |
| OTA 0 和 OTA 1  | 自定义   |                           | 存放运行的bin包或作为另一个分区 |



## CSV分区

相较于串口烧录烧录的bin文件来自PC端，由PC端控制进行覆盖旧程序的操作不同，OTA升级需要原程序接收新的bin文件之后自己进行切换，在此期间新的bin文件保存在ESP32内部，因此管理其内部空间变得非常重要，这样才能使得找准新程序的首地址，用来启动。

乐鑫官方使用 `partitions.csv` 文件定义 CSV分区 ，通过在 `menuconfig` 中选择了 "Custom partition table CSV"，并输入该分区表的 CSV 文件在项目中的路径指定项目CSV分区的使用的 `partitions.csv` 。

CSV 文件可以根据需要，描述任意数量的分区信息。

例如下面是一个自定义的 OTA 分区表的 CSV 文件：

```
# Name,   Type, SubType, Offset,   Size, Flags
# 分区名称  类型  子类      偏移      大小   标记
nvs,      data, nvs,     0x9000,   0x4000,
otadata,  data, ota,     0xd000,   0x2000,
phy_init, data, phy,     0xf000,   0x1000,
factory,  app,  factory, 0x10000,  512K,
ota_0,    app,  ota_0,   ,         512K,
ota_1,    app,  ota_1,   ,         512K,
# 共占用 16K + 8K + 4K + 512K + 512K + 512K = 1564k ≈ 1.527MB
# 足够保存到 ESP32-S3 的 2MB 大的 Flash 里
```

- 字段之间的空格会被忽略，任何以 `#` 开头的行（注释）也会被忽略。
- CSV 文件中的每个非注释行均为一个分区定义。
- 每个分区的 `Offset` 字段可以为空，`gen_esp32part.py` 工具会从分区表位置的后面开始自动计算并填充该分区的偏移地址，同时确保每个分区的偏移地址正确对齐。



## 启动

1.  程序首先从固化在`ROM` 里的  `ROM Bootloader` 启动，执行基础的初始化。
2.  随后开始运行保存在 `Flash` 里的 二级Bootloader ，执行 `esp-idf` 的初始化。
3.  开始从 `0x9000` 读取分区表，获取各个分区信息。
4.  读取 `OTA-Data` 分区，获得 OTA状态 ，决定运行 OTA 0 还是 OTA 1。

