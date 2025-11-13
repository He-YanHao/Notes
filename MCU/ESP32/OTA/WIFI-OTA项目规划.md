# WIFI-OTA规划

## 基础概念

需要在ESP32的Flash中划分出**两个应用程序分区（例如`ota_0`和`ota_1`）**。

设备运行时，其中一个分区处于**活动状态**，负责当前固件的执行；另一个则处于**非活动状态**，用于接收和存储新的固件。

当新版固件在非活动分区完成下载和验证后，Bootloader在下次启动时会转而从该分区加载固件，从而实现升级。



## ESP32-S3

| 项目          | 说明                                     |
| :------------ | :--------------------------------------- |
| **芯片型号**  | `esp32c3` - 基于RISC-V架构的ESP32-C3芯片 |
| **CPU核心**   | `1 CPU core(s)` - 单核处理器             |
| **无线功能**  | `WiFi/BLE` - 支持WiFi和蓝牙低功耗        |
| **硅版本**    | `silicon revision v0.4` - 芯片硬件版本   |
| **外部Flash** | `2MB external flash` - 外部存储容量      |



## 关键API函数



## 步骤

创建或修改分区表（CSV文件），确保包含OTA所需分区（如`otadata`, `ota_0`, `ota_1`）可通过 `idf.py menuconfig` 中的 `Partition Table` 选项进行设置。



**配置固件下载地址**：在 `menuconfig` 中指定固件下载的URL地址。

```c
#include "esp_https_ota.h"
#include "esp_http_client.h"

// 配置HTTPS OTA，如URL和服务器证书（如需验证）
esp_http_client_config_t config = {
    .url = "https://your-server.com/firmware.bin",
    .cert_pem = (char *)your_server_cert_pem, // 可选，用于服务器验证
    .event_handler = your_http_event_handler, // 处理HTTP事件，如显示进度
};

// 执行OTA升级
esp_err_t ret = esp_https_ota(&config);
if (ret == ESP_OK) {
    // 升级成功，重启设备
    esp_restart();
} else {
    // 处理升级失败
    ESP_LOGE(TAG, "Firmware upgrade failed");
}
```

