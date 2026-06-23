# ESP32发展史以及驱动LCD能力

## 核心产品型号发展史

资料来源：乐鑫官网 https://www.espressif.com.cn/zh-hans/company/about-espressif

>   2026
>   乐鑫发布 ESP32-H21：一款针对 Thread、Matter 及 Bluetooth LE 5 设备的增强型低功耗无线 SoC。
>   乐鑫发布 ESP32-E22：公司首款集成三频 Wi-Fi 6E 与双模蓝牙的无线协处理器，新一代产品矩阵由此全面开启。
>   2025
>   乐鑫发布 ESP32-C61：支持 Wi-Fi 6 和 Bluetooth 5 (LE) 的 SoC，超值畅想 Wi-Fi 6。
>   乐鑫发布 ESP32-P4：具备丰富 IO 连接、HMI 和出色安全特性的高性能 SoC。
>   乐鑫发布 ESP32-C5：行业首款 RISC-V 架构 2.4 & 5 GHz 双频双模 Wi-Fi 6 + Bluetooth LE + 802.15.4 SoC。
>   2023
>   乐鑫发布 ESP32-H2：支持 802.15.4 和 Bluetooth 5 (LE) 的 SoC，具备领先的低功耗和安全的连接能力。
>   乐鑫发布 ESP32-C6：集成 Wi-Fi 6 + Bluetooth 5 (LE) + 802.15.4 的 SoC，提升连接性能。
>   2022
>   乐鑫发布 ESP32-C2：尺寸小巧且极具性价比的 SoC，为设备添加简单又稳定的无线连接功能。
>   2021
>   乐鑫发布 ESP32-S3：集成 Wi-Fi 和 Bluetooth 5 (LE) 的强大 AI SoC，适用于 HMI 应用。
>   乐鑫发布 ESP32-C3：安全、低功耗、低成本的 RISC-V SoC，集成 Wi-Fi 和 Bluetooth 5 (LE)。
>   2020
>   乐鑫发布 ESP32-S2：安全、强大的 Wi-Fi SoC，拥有丰富的 I/O 接口。
>   2016
>   乐鑫发布官方物联网开发框架 ESP-IDF。
>   乐鑫发布旗舰产品 ESP32：适用于物联网应用的革命性 Wi-Fi + Bluetooth SoC。
>   2014
>   乐鑫发布第一款物联网 SoC ESP8266EX：面向物联网应用的超低功耗、高度集成的 Wi-Fi SoC。
>   2013
>   乐鑫发布第一款产品 ESP8089：专为平板和机顶盒应用设计的 Wi-Fi SoC。
>   2008
>   乐鑫信息科技由张瑞安先生在中国上海创立。



## 显示外设配置

以下数据来源主要为：https://docs.espressif.com/projects/esp-iot-solution/zh_CN/release-v2.0/display/lcd/lcd_development_guide.html#id22

| Soc      | SPI (QSPI) | I80  | RGB  | MIPI-DSI |
| -------- | ---------- | ---- | ---- | -------- |
| ESP32    | 支持       | 支持 |      |          |
| ESP32-C3 | 支持       |      |      |          |
| ESP32-C6 | 支持       |      |      |          |
| ESP32-S2 | 支持       | 支持 |      |          |
| ESP32-S3 | 支持       | 支持 | 支持 |          |
| ESP32-P4 | 支持       | 支持 | 支持 | 支持     |



## 驱动LCD 能力

资料来源：https://docs.espressif.com/projects/esp-techpedia/zh_CN/latest/esp-friends/advanced-development/lcd-application-note/overview.html#id5

以下为各平台常用 LCD 配置及性能汇总，可帮助快速选择和规避风险：

| 平台         | 接口类型 | 分辨率      | 色彩格式 | Benchmark (fps) | SRAM 占用 | PSRAM 占用 |
| ------------ | -------- | ----------- | -------- | --------------- | --------- | ---------- |
| **ESP32-P4** | MIPI-DSI | 1080 × 1920 | RGB565   | 25              | 108 KB    | 12.4 MB    |
|              |          | 1080 × 1920 | RGB888   | 20              | 162 KB    | 18.3 MB    |
|              | MIPI-DSI | 800 × 1280  | RGB565   | 40              | 80 KB     | 6.0 MB     |
|              |          | 800 × 1280  | RGB888   | 30              | 120 KB    | 9.0 MB     |
|              | MIPI-DSI | 1024 × 600  | RGB565   | 55              | 102 KB    | 3.7 MB     |
|              |          | 1024 × 600  | RGB888   | 50              | 153 KB    | 5.5 MB     |
|              | RGB      | 800 × 480   | RGB565   | 60              | 80 KB     | 2.3 MB     |
|              |          | 800 × 480   | RGB888   | 60              | 120 KB    | 3.4 MB     |
| **ESP32-S3** | RGB      | 800 × 480   | RGB565   | 25              | 80 KB     | 2.3 MB     |
|              | RGB      | 480 × 480   | RGB565   | 47              | 48 KB     | 1.4 MB     |
|              | QSPI     | 360 × 360   | RGB565   | 55              |           | 518 KB     |
|              | SPI      | 320 × 240   | RGB565   | 48              |           | 307 KB     |
| **ESP32-C5** | SPI      | 280 × 284   | RGB565   | 42              |           | 318 KB     |
| **ESP32-C3** | SPI      | 240 × 240   | RGB565   | 27              | 4.8 KB    |            |
| **ESP32-C2** | SPI      | 160 × 80    | RGB565   | 15              | 1.6 KB    |            |

该测试数据基于 [esp_lvgl_adapter](https://github.com/espressif/esp-iot-solution/tree/master/components/display/tools/esp_lvgl_adapter) 和 LVGL v9.4.0 Benchmark 测试得出。

其中的内存占用数值为推荐配置，可根据实际应用场景灵活调整。

>   注意：**ESP32-P4**不具备无线通信能力



