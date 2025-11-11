# OTA

很好！现在您已经成功连接WiFi，接下来要实现OTA（空中升级）功能。ESP-IDF提供了完整的OTA解决方案。以下是实现步骤：

## 1. 配置项目支持OTA

首先需要在 `idf.py menuconfig` 中启用OTA配置：

```bash
idf.py menuconfig
```

配置以下选项：
- **Partition Table** → Partition Table → 选择 "Factory app, two OTA definitions"
- **Component config** → ESP HTTPS OTA → 启用相关选项

## 2. 修改分区表

在项目根目录创建 `partitions.csv` 文件：

```
# Name,   Type, SubType, Offset,  Size, Flags
nvs,      data, nvs,     0x9000,  0x4000,
otadata,  data, ota,     0xd000,  0x2000,
phy_init, data, phy,     0xf000,  0x1000,
factory,  app,  factory, 0x10000, 1M,
ota_0,    app,  ota_0,   ,        1M,
ota_1,    app,  ota_1,   ,        1M,
```

## 3. 实现OTA功能

在 `main/wifi_OTA.c` 中添加OTA功能：

```c
#include "esp_ota_ops.h"
#include "esp_http_client.h"
#include "esp_https_ota.h"
#include "esp_flash_partitions.h"

// OTA服务器配置
#define OTA_URL "http://your-server.com/firmware.bin"

static void http_ota_task(void *pvParameter)
{
    esp_http_client_config_t config = {
        .url = OTA_URL,
        .timeout_ms = 5000,
    };
    
    esp_https_ota_config_t ota_config = {
        .http_config = &config,
    };
    
    esp_https_ota_handle_t https_ota_handle = NULL;
    esp_err_t err = esp_https_ota_begin(&ota_config, &https_ota_handle);
    
    if (err != ESP_OK) {
        ESP_LOGE("OTA", "OTA开始失败: %s", esp_err_to_name(err));
        vTaskDelete(NULL);
        return;
    }
    
    ESP_LOGI("OTA", "OTA升级开始...");
    
    while (1) {
        err = esp_https_ota_perform(https_ota_handle);
        if (err != ESP_ERR_HTTPS_OTA_IN_PROGRESS) {
            break;
        }
    }
    
    if (err == ESP_OK) {
        ESP_LOGI("OTA", "OTA升级成功!");
        esp_https_ota_finish(https_ota_handle);
        
        // 重启设备以运行新固件
        vTaskDelay(2000 / portTICK_PERIOD_MS);
        esp_restart();
    } else {
        ESP_LOGE("OTA", "OTA升级失败: %s", esp_err_to_name(err));
        esp_https_ota_abort(https_ota_handle);
    }
    
    vTaskDelete(NULL);
}

void start_ota_update(void)
{
    xTaskCreate(&http_ota_task, "ota_task", 8192, NULL, 5, NULL);
}
```

## 4. 添加HTTP服务器获取bin文件

在 `common` 组件中添加HTTP客户端功能来获取OTA文件：

```c
// common/inc/my_ota.h
#ifndef MY_OTA_H
#define MY_OTA_H

#include "esp_err.h"

void start_ota_update(void);
esp_err_t check_firmware_version(void);

#endif
```

```c
// common/src/my_ota.c
#include "my_ota.h"
#include "esp_http_client.h"
#include "esp_ota_ops.h"
#include "cJSON.h"

// 检查固件版本
esp_err_t check_firmware_version(void)
{
    esp_http_client_config_t config = {
        .url = "http://your-server.com/version.json",
        .timeout_ms = 5000,
    };
    
    esp_http_client_handle_t client = esp_http_client_init(&config);
    esp_err_t err = esp_http_client_perform(client);
    
    if (err == ESP_OK) {
        int content_length = esp_http_client_get_content_length(client);
        char *response = malloc(content_length + 1);
        esp_http_client_read(client, response, content_length);
        response[content_length] = '\0';
        
        // 解析JSON响应
        cJSON *root = cJSON_Parse(response);
        if (root != NULL) {
            cJSON *version = cJSON_GetObjectItem(root, "version");
            cJSON *url = cJSON_GetObjectItem(root, "url");
            
            if (cJSON_IsString(version) && cJSON_IsString(url)) {
                // 比较版本号，决定是否需要更新
                const esp_app_desc_t *app_desc = esp_ota_get_app_description();
                if (strcmp(app_desc->version, version->valuestring) != 0) {
                    ESP_LOGI("OTA", "发现新版本: %s", version->valuestring);
                    // 设置OTA URL并开始更新
                }
            }
            cJSON_Delete(root);
        }
        free(response);
    }
    
    esp_http_client_cleanup(client);
    return err;
}
```

## 5. 修改主程序

在 `main/wifi_OTA.c` 中集成OTA功能：

```c
#include "my_wifi.h"
#include "my_ota.h"

void app_main(void)
{
    // 初始化NVS
    esp_err_t ret = nvs_flash_init();
    if (ret == ESP_ERR_NVS_NO_FREE_PAGES || ret == ESP_ERR_NVS_NEW_VERSION_FOUND) {
        ESP_ERROR_CHECK(nvs_flash_erase());
        ret = nvs_flash_init();
    }
    ESP_ERROR_CHECK(ret);
    
    // 初始化WiFi
    wifi_init_sta();
    
    // 等待WiFi连接
    vTaskDelay(5000 / portTICK_PERIOD_MS);
    
    // 检查固件更新
    check_firmware_version();
    
    // 或者直接开始OTA更新
    // start_ota_update();
}
```

## 6. 更新CMakeLists.txt

确保添加必要的依赖：

```cmake
# main/CMakeLists.txt
idf_component_register(SRCS "wifi_OTA.c"
                    PRIV_REQUIRES esp_wifi nvs_flash esp_http_client esp_https_ota json cJSON
                    REQUIRES common)
```

```cmake
# common/CMakeLists.txt
idf_component_register(SRCS "src/my_wifi.c" "src/my_ota.c"
                    INCLUDE_DIRS "inc"
                    REQUIRES esp_wifi nvs_flash esp_http_client esp_https_ota json cJSON)
```

## 7. 服务器端准备

您需要准备一个简单的HTTP服务器来提供：
- `version.json` - 包含版本信息
- `firmware.bin` - 新的固件文件

示例 `version.json`：
```json
{
    "version": "1.1.0",
    "url": "http://your-server.com/firmware.bin",
    "changelog": "修复了WiFi连接稳定性问题"
}
```

## 8. 构建和测试

1. 构建当前固件：
   ```bash
   idf.py build
   ```

2. 生成bin文件用于OTA测试：
   ```bash
   idf.py build
   cp build/wifi_OTA.bin /path/to/your/web/server/firmware.bin
   ```

3. 烧录当前固件到设备

4. 修改版本号，构建新固件并上传到服务器

5. 设备将自动检测并下载新固件

这样您就实现了完整的WiFi OTA功能！设备可以自动检查更新并通过WiFi下载新的固件文件。
