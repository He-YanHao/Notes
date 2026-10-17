# LCD

## esp_lcd_dsi_bus_handle_t

```c
typedef struct esp_lcd_dsi_bus_t {
    int bus_id;                          // 总线编号（0, 1, ...），区分多个 DSI 控制器
    mipi_dsi_hal_context_t hal;          // 内嵌的 HAL 上下文（见上）
    soc_module_clk_t phy_pllref_clk_src; // PHY PLL 参考时钟源
    soc_module_clk_t phy_cfg_clk_src;    // PHY 配置时钟源
#if CONFIG_PM_ENABLE
    esp_pm_lock_handle_t pm_lock;        // 电源管理锁
#endif
} esp_lcd_dsi_bus_t;
```



## esp_lcd_panel_io_handle_t

```c
// LCD 面板 IO 接口抽象
typedef struct esp_lcd_panel_io_t *esp_lcd_panel_io_handle_t; /*!< Type of LCD panel IO handle */

struct esp_lcd_panel_io_t {
	// 读命令对应的参数
    esp_err_t (*rx_param)(esp_lcd_panel_io_t *io, int lcd_cmd, void *param, size_t param_size);
	// 写命令 + 参数
    esp_err_t (*tx_param)(esp_lcd_panel_io_t *io, int lcd_cmd, const void *param, size_t param_size);
	// 写像素（RGB）数据
    esp_err_t (*tx_color)(esp_lcd_panel_io_t *io, int lcd_cmd, const void *color, size_t color_size);
	// 销毁对象
    esp_err_t (*del)(esp_lcd_panel_io_t *io);
	// 注册事件回调
    esp_err_t (*register_event_callbacks)(esp_lcd_panel_io_t *io, const esp_lcd_panel_io_callbacks_t *cbs, void *user_ctx);
};
```



## esp_lcd_panel_handle_t

```c
// LCD 面板级操作抽象
typedef struct esp_lcd_panel_t *esp_lcd_panel_handle_t;       /*!< Type of LCD panel handle */

struct esp_lcd_panel_t {
	// 复位
    esp_err_t (*reset)(esp_lcd_panel_t *panel);
	// 初始化
    esp_err_t (*init)(esp_lcd_panel_t *panel);
	// 删除销毁
    esp_err_t (*del)(esp_lcd_panel_t *panel);
	// 画一块位图
    esp_err_t (*draw_bitmap)(esp_lcd_panel_t *panel, int x_start, int y_start, int x_end, int y_end, const void *color_data);
	// 从源图裁剪后再画
    esp_err_t (*draw_bitmap_2d)(esp_lcd_panel_t *panel, int x_start, int y_start, int x_end, int y_end, const void *src_data, size_t src_x_size, size_t src_y_size, int src_x_start, int src_y_start, int src_x_end, int src_y_end);
	// 镜像
    esp_err_t (*mirror)(esp_lcd_panel_t *panel, bool x_axis, bool y_axis);
	// X/Y 轴交换
    esp_err_t (*swap_xy)(esp_lcd_panel_t *panel, bool swap_axes);
	// 设置面板偏移
    esp_err_t (*set_gap)(esp_lcd_panel_t *panel, int x_gap, int y_gap);
	// 颜色反转
    esp_err_t (*invert_color)(esp_lcd_panel_t *panel, bool invert_color_data);
	// 显示开关
    esp_err_t (*disp_on_off)(esp_lcd_panel_t *panel, bool on_off);
	// 睡眠模式
    esp_err_t (*disp_sleep)(esp_lcd_panel_t *panel, bool sleep);
	// 背光亮度
    esp_err_t (*set_brightness)(esp_lcd_panel_t *panel, int brightness);
	// 用户私有数据
    void *user_data;    /*!< User data, used to store externally customized data */
};
```



