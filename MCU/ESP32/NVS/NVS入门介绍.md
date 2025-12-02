# NVS入门介绍

## NVS基础描述

原始 Flash 以扇区（通常4KB）为单位进行操作，即使写入 1bit 数据也要擦除一个完整的扇区，自行处理擦写并不安全。

引入 NVS (非易失性存储) 后可以以键值对通过键名直接访问数据，并且提供命名空间、原子操作和CRC校验。



## API

### 初始化

使用

```c
nvs_flash_init()
```

初始化 nvs ，可能返回：

| 返回值                          | 含义                   |
| ------------------------------- | ---------------------- |
| `ESP_OK`                        | 初始化成功             |
| `ESP_ERR_NVS_NO_FREE_PAGES`     | NVS 空间不足或结构损坏 |
| `ESP_ERR_NVS_NEW_VERSION_FOUND` | NVS 版本升级导致不兼容 |
| 其他 err                        | 其他异常               |

```c
// 初始化nvs 并记录返回值
esp_err_t err = nvs_flash_init();
// 假设空间不足 或 版本不正确
if (err == ESP_ERR_NVS_NO_FREE_PAGES || err == ESP_ERR_NVS_NEW_VERSION_FOUND)
{
    // 全部擦除
	ESP_ERROR_CHECK(nvs_flash_erase());
    // 再次初始化
	err = nvs_flash_init();
}
// 假设再次初始化错误则报错退出程序
ESP_ERROR_CHECK(err);
```

### 获取句柄

```c
// NVS 的“句柄”，类似于文件句柄，用于后续读 key/value。
nvs_handle_t my_handle;
/**
 *nvs：打开 nvs 分区。
 *storage：
 *NVS_READONLY：限制为只读，读写是 NVS_READWRITE。
 *my_handle：成功时会填入一个有效句柄的地址。
 */
err = nvs_open_from_partition("nvs", "storage", NVS_READONLY, &my_handle);
if (err != ESP_OK) {
	ESP_LOGE(TAG, "Error (%s) opening NVS handle!\n", esp_err_to_name(err));
	return;
}
```

### 使用

#### u8

```c
uint8_t u8_val = 0;
// 获取命名为 "u8_key" 的键值，保存到 u8_val 里。
err = nvs_get_u8(my_handle, "u8_key", &u8_val);
ESP_ERROR_CHECK(err);
printf("%"PRIu8"\n", u8_val);
// 断言 如果不成立 则失败卡死
assert(u8_val == 255);
```

#### i8

    int8_t i8_val = 0;
    err = nvs_get_i8(my_handle, "i8_key", &i8_val);
    ESP_ERROR_CHECK(err);
    printf("%"PRIi8"\n", i8_val);
    assert(i8_val == -128);
#### u16

```c
uint16_t u16_val = 0;
err = nvs_get_u16(my_handle, "u16_key", &u16_val);
ESP_ERROR_CHECK(err);
printf("%"PRIu16"\n", u16_val);
assert(u16_val == 65535);
```

#### u32

```c
uint32_t u32_val = 0;
err = nvs_get_u32(my_handle, "u32_key", &u32_val);
ESP_ERROR_CHECK(err);
printf("%"PRIu32"\n", u32_val);
assert(u32_val == 4294967295);
```
#### i32

    int32_t i32_val = 0;
    err = nvs_get_i32(my_handle, "i32_key", &i32_val);
    ESP_ERROR_CHECK(err);
    printf("%"PRIi32"\n", i32_val);
    assert(i32_val == -2147483648);
#### i16

    size_t str_len = 0;
    err = nvs_get_str(my_handle, "str_key", NULL, &str_len);
    ESP_ERROR_CHECK(err);
    assert(str_len == 222);
    
    char* str_val = (char*) malloc(str_len);
    err = nvs_get_str(my_handle, "str_key", str_val, &str_len);
    ESP_ERROR_CHECK(err);
    printf("%s\n", str_val);
    assert(str_val[0] == 'L');
    free(str_val);
### 关闭

用完一定要记得关闭

```c
nvs_close(my_handle);
```

## nvs分区表

```
# Sample csv file
key,type,encoding,value
storage,namespace,,
u8_key,data,u8,255
i8_key,data,i8,-128
u16_key,data,u16,65535
u32_key,data,u32,4294967295
i32_key,data,i32,-2147483648
str_key,data,string,"Lorem ipsum dolor sit amet, consectetur adipiscing elit.
Fusce quis risus justo.
Suspendisse egestas in nisi sit amet auctor.
Pellentesque rhoncus dictum sodales.
In justo erat, viverra at interdum eget, interdum vel dui."
```



## C

### 头文件

```c
#include <stdio.h>
#include <inttypes.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"
#include "esp_log.h"
#include "nvs_flash.h"
#include "nvs.h"
```

```c
typedef struct {
    nvs_type_t type;
    const char *str;
} type_str_pair_t;
```



### 只读变量数据表

```c
static const type_str_pair_t type_str_pair[] = {
    { NVS_TYPE_I8, "i8" },
    { NVS_TYPE_U8, "u8" },
    { NVS_TYPE_U16, "u16" },
    { NVS_TYPE_I16, "i16" },
    { NVS_TYPE_U32, "u32" },
    { NVS_TYPE_I32, "i32" },
    { NVS_TYPE_U64, "u64" },
    { NVS_TYPE_I64, "i64" },
    { NVS_TYPE_STR, "str" },
    { NVS_TYPE_BLOB, "blob" },
    { NVS_TYPE_ANY, "any" },
};
```

### 类型查找函数

```c
static const char *type_to_str(nvs_type_t type)
{
    for (int i = 0; i < TYPE_STR_PAIR_SIZE; i++) {
        const type_str_pair_t *p = &type_str_pair[i];
        if (p->type == type) {
            return  p->str;
        }
    }
    return "Unknown";
}
```



```c

    // Initialize NVS
    esp_err_t err = nvs_flash_init();
    if (err == ESP_ERR_NVS_NO_FREE_PAGES || err == ESP_ERR_NVS_NEW_VERSION_FOUND) {
        // NVS partition was truncated and needs to be erased
        // Retry nvs_flash_init
        ESP_ERROR_CHECK(nvs_flash_erase());
        err = nvs_flash_init();
    }
    ESP_ERROR_CHECK(err);

    // Open NVS handle
    ESP_LOGI(TAG, "\nOpening Non-Volatile Storage (NVS) handle...");
    nvs_handle_t my_handle;
    err = nvs_open("storage", NVS_READWRITE, &my_handle);
    if (err != ESP_OK) {
        ESP_LOGE(TAG, "Error (%s) opening NVS handle!", esp_err_to_name(err));
        return;
    }

    // Store and read an integer value
    int32_t counter = 42;
    ESP_LOGI(TAG, "\nWriting counter to NVS...");
    err = nvs_set_i32(my_handle, "counter", counter);
    if (err != ESP_OK) {
        ESP_LOGE(TAG, "Failed to write counter!");
    }

    // Read back the value
    int32_t read_counter = 0;
    ESP_LOGI(TAG, "\nReading counter from NVS...");
    err = nvs_get_i32(my_handle, "counter", &read_counter);
    switch (err) {
        case ESP_OK:
            ESP_LOGI(TAG, "Read counter = %" PRIu32, read_counter);
            break;
        case ESP_ERR_NVS_NOT_FOUND:
            ESP_LOGW(TAG, "The value is not initialized yet!");
            break;
        default:
            ESP_LOGE(TAG, "Error (%s) reading!", esp_err_to_name(err));
    }

    // Store and read a string
    ESP_LOGI(TAG, "\nWriting string to NVS...");
    err = nvs_set_str(my_handle, "message", "Hello from NVS!");
    if (err != ESP_OK) {
        ESP_LOGE(TAG, "Failed to write string!");
    }

    // Read back the string
    size_t required_size = 0;
    ESP_LOGI(TAG, "\nReading string from NVS...");
    err = nvs_get_str(my_handle, "message", NULL, &required_size);
    if (err == ESP_OK) {
        char* message = malloc(required_size);
        err = nvs_get_str(my_handle, "message", message, &required_size);
        if (err == ESP_OK) {
            ESP_LOGI(TAG, "Read string: %s", message);
        }
        free(message);
    }

    // Find keys in NVS
    ESP_LOGI(TAG, "\nFinding keys in NVS...");
    nvs_iterator_t it = NULL;
    esp_err_t res = nvs_entry_find("nvs", "storage", NVS_TYPE_ANY, &it);
    while(res == ESP_OK) {
        nvs_entry_info_t info;
        nvs_entry_info(it, &info);
        const char *type_str =  type_to_str(info.type);
        ESP_LOGI(TAG, "Key: '%s', Type: %s", info.key, type_str);
        res = nvs_entry_next(&it);
    }
    nvs_release_iterator(it);

    // Delete a key from NVS
    ESP_LOGI(TAG, "\nDeleting key from NVS...");
    err = nvs_erase_key(my_handle, "counter");
    if (err != ESP_OK) {
        ESP_LOGE(TAG, "Failed to erase key!");
    }

    // Commit changes
    // After setting any values, nvs_commit() must be called to ensure changes are written
    // to flash storage. Implementations may write to storage at other times,
    // but this is not guaranteed.
    ESP_LOGI(TAG, "\nCommitting updates in NVS...");
    err = nvs_commit(my_handle);
    if (err != ESP_OK) {
        ESP_LOGE(TAG, "Failed to commit NVS changes!");
    }

    // Close
    nvs_close(my_handle);
    ESP_LOGI(TAG, "NVS handle closed.");

    ESP_LOGI(TAG, "Returned to app_main");
```



```c
static const char *TAG = "nvs_example";

static const size_t TYPE_STR_PAIR_SIZE = sizeof(type_str_pair) / sizeof(type_str_pair[0]);




```

