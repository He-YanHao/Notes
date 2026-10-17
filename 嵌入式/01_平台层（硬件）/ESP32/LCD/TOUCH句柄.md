# TOUCH句柄

## esp_lcd_panel_io_t

```c
// LCD/TOUCH 共用的 IO 接口抽象
typedef struct esp_lcd_panel_io_t *esp_lcd_panel_io_handle_t; /*!< Type of LCD panel IO handle */

struct esp_lcd_panel_io_t {
    // 读命令对应的参数（触摸 IC 读坐标/ID 走这里）
    esp_err_t (*rx_param)(esp_lcd_panel_io_t *io, int lcd_cmd, void *param, size_t param_size);
    // 写命令 + 参数（触摸 IC 配置寄存器走这里）
    esp_err_t (*tx_param)(esp_lcd_panel_io_t *io, int lcd_cmd, const void *param, size_t param_size);
    // 写像素（RGB）数据（触摸场景一般用不到）
    esp_err_t (*tx_color)(esp_lcd_panel_io_t *io, int lcd_cmd, const void *color, size_t color_size);
    // 销毁对象
    esp_err_t (*del)(esp_lcd_panel_io_t *io);
    // 注册事件回调
    esp_err_t (*register_event_callbacks)(esp_lcd_panel_io_t *io, const esp_lcd_panel_io_callbacks_t *cbs, void *user_ctx);
};
```

```c
/**
 * @brief Touch controller type
 *
 */
typedef struct esp_lcd_touch_s esp_lcd_touch_t;
typedef esp_lcd_touch_t *esp_lcd_touch_handle_t;

struct esp_lcd_touch_s {
    // 进入睡眠模式（降低触摸 IC 功耗，通常用于熄屏或系统休眠）
    esp_err_t (*enter_sleep)(esp_lcd_touch_handle_t tp);
    // 退出睡眠模式（唤醒触摸 IC，使其恢复扫描）
    esp_err_t (*exit_sleep)(esp_lcd_touch_handle_t tp);
    // 从触摸 IC 读取一次原始数据（坐标/点数/压力等，结果存入 tp->data）
    // 注意：它只负责"读硬件"，不做坐标校准/方向变换，上层需再调 get_xy 取结果
    esp_err_t (*read_data)(esp_lcd_touch_handle_t tp);
    // 从最近一次 read_data 的结果中取出触摸点（坐标 + 压力 + 点数）
    // 返回 true 表示有有效触摸；false 表示无触摸
    bool (*get_xy)(esp_lcd_touch_handle_t tp, uint16_t *x, uint16_t *y, uint16_t *strength, uint8_t *point_num, uint8_t max_point_num);
    // 获取每个触摸点的追踪 ID（多点触控时用于区分手指，需 IC 支持）
    esp_err_t (*get_track_id)(esp_lcd_touch_handle_t tp, uint8_t *track_id, uint8_t point_num);
#if (CONFIG_ESP_LCD_TOUCH_MAX_BUTTONS > 0)
    // 获取触摸 IC 上虚拟按键的状态（第 n 个按键，*state 返回按下/松开）
    esp_err_t (*get_button_state)(esp_lcd_touch_handle_t tp, uint8_t n, uint8_t *state);
#endif
    // 设置 X/Y 轴交换（适配屏幕旋转方向）
    esp_err_t (*set_swap_xy)(esp_lcd_touch_handle_t tp, bool swap);
    // 获取当前 X/Y 轴交换状态
    esp_err_t (*get_swap_xy)(esp_lcd_touch_handle_t tp, bool *swap);
    // 设置 X 轴镜像（适配屏幕翻转方向）
    esp_err_t (*set_mirror_x)(esp_lcd_touch_handle_t tp, bool mirror);
    // 获取当前 X 轴镜像状态
    esp_err_t (*get_mirror_x)(esp_lcd_touch_handle_t tp, bool *mirror);
    // 设置 Y 轴镜像
    esp_err_t (*set_mirror_y)(esp_lcd_touch_handle_t tp, bool mirror);
    // 获取当前 Y 轴镜像状态
    esp_err_t (*get_mirror_y)(esp_lcd_touch_handle_t tp, bool *mirror);
    // 销毁触摸对象（释放资源、复位引脚、关闭 IO）
    esp_err_t (*del)(esp_lcd_touch_handle_t tp);
    // 触摸配置（x_max/y_max、镜像/交换标志、中断回调等），创建时拷贝进来
    esp_lcd_touch_config_t config;
    // 触摸 IC 的通信 IO 句柄（SPI/I2C，复用 esp_lcd_panel_io_t）
    esp_lcd_panel_io_handle_t io;
    // 最近一次 read_data 读到的原始触摸数据（坐标/点数/压力/按键等缓存）
    esp_lcd_touch_data_t data;
};
```

