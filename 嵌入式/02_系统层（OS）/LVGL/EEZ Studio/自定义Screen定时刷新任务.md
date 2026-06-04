# 自定义Screen定时刷新任务

## 背景

EEZ Studio 生成的代码中 ui.c 包含：

```c
void tick_screen(int screen_index) {
    tick_screen_funcs[screen_index]();
}
void ui_tick() {
    tick_screen(currentScreen);
}
```

可以直接调用 `ui_tick()` 来调用当前页面的刷新函数，如：

```c
void tick_screen_main();
```

但是生成的函数默认为空，并且找不到自定义生成函数的位置，手动更新这些函数会被再次生成的函数覆盖，因此自定义Screen定时刷新任务。

## 方案

新建文件 `components/lvgl_init/ui_updates.c` 和 `ui_updates.h`

实现如下：

在 `actions_main.c` ：

```c
void screen_tick_main(void)
{
	static int n;
	if (++n >= 20)
	{
		n = 0;
		ESP_LOGI(TAG, "refresh: main");
	}
}
```



```c
#include "screen_manager.h"
#include "screens.h"
#include "ui.h"
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_log.h"

static const char *TAG = "screen_manager";

// 真正的屏幕刷新函数，在各自的actions_xxx.c中实现
extern void refresh_screen_clock(void);

// 前向声明各屏幕的刷新函数
void screen_tick_main(void);

typedef void (*screen_refresh_fn_t)(void);

typedef struct {
    int16_t screen_id;
    screen_refresh_fn_t refresh_fn;
} screen_map_entry_t;

static const screen_map_entry_t screen_map[] = {
    {SCREEN_ID_MAIN,     screen_tick_main},

};

static const int16_t screen_map_size = sizeof(screen_map) / sizeof(screen_map[0]);
static int16_t current_screen_id = -1;

/**
 * @brief 刷新当前屏幕
 * 根据当前屏幕 ID 查找映射表，如果有注册的刷新函数则调用，否则直接返回
 */
void screen_update(void)
{
    current_screen_id = get_current_screen_id();
    if (current_screen_id == -1) {
        return;
    }
    current_screen_id += 1; // 将屏幕索引转换为屏幕 ID
    for (int i = 0; i < screen_map_size; i++) {
        if (screen_map[i].screen_id == current_screen_id) {
            if (screen_map[i].refresh_fn != NULL) {
                screen_map[i].refresh_fn();
            }
            return;
        }
    }
}
```

```c
#ifndef __SCREEN_MANAGER_H__
#define __SCREEN_MANAGER_H__

#include "esp_err.h"

#ifdef __cplusplus
extern "C" {
#endif

// 刷新当前屏幕
void screen_update(void);

#ifdef __cplusplus
}
#endif

#endif /*__SCREEN_MANAGER_H__*/
```

在 `lvgl_init.c` 里：

```c
// 定时器回调（包装你的 screen_update）
static void screen_update_timer_cb(lv_timer_t *timer)
{
    screen_update();
}

esp_err_t lvgl_init(void)
{
	......
	// 创建定时器
    lv_timer_t *screen_update_timer = lv_timer_create(screen_update_timer_cb, 100, NULL);
    if (screen_update_timer == NULL) {
        ESP_LOGE(TAG, "Failed to create LVGL timer");
        return ESP_FAIL;
    }
    ESP_LOGI(TAG, "LVGL timer created successfully");
	......
}
```

