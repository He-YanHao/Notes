# LVGL概述

## 移植要求

| 项目         | 要求                                            |
| ------------ | ----------------------------------------------- |
| 微控制器     | 16、32 、64 位的微控制器或处理器                |
| 主控频率(Hz) | 至少16 MHz 时钟速度                             |
| Flash/ROM    | 至少64 kB ，如果使用非常多的部件，推荐 > 180 kB |
| 内存(RAM)    | 8kB（建议配置24kB）                             |



## 使用步骤

以下是如何将 LVGL 集成到您的项目中的概述。 完成 详情请参阅概述。

-   驱动程序初始化:

    用户有责任设置时钟、定时器、外围设备等。

-   调用 lv_init（）:

    初始化 LVGL 本身。

-   创建显示和输入设备:

    创建显示器 （）和输入设备 （）并设置其回调。`lv_display_t``lv_indev_t`

-   创建UI:

    调用 LVGL 函数来创建屏幕、小部件、样式、动画、事件等。

-   在循环中调用 lv_timer_handler（）:

    这将处理所有与 LVGL 相关的任务：刷新显示，读取输入设备，根据用户输入（和其他内容）触发事件，运行任何动画，以及运行用户创建的计时器。

## 案例

```c
void main(void)
{
    your_driver_init();

    lv_init();

    lv_tick_set_cb(my_get_millis);

    lv_display_t * display = lv_display_create(320, 240);

    /* LVGL will render to this 1/10 screen sized buffer for 2 bytes/pixel */
    static uint8_t buf[320 * 240 / 10 * 2];
    lv_display_set_buffers(display, buf, NULL, LV_DISPLAY_RENDER_MODE_PARTIAL);

    /* This callback will display the rendered image */
    lv_display_set_flush_cb(display, my_flush_cb);

    /* Create widgets */
    lv_obj_t * label = lv_label_create(lv_screen_active());
    lv_label_set_text(label, "Hello LVGL!");

    /* Make LVGL periodically execute its tasks */
    while(1) {
        /* Provide updates to currently-displayed Widgets here. */
        lv_timer_handler();
        my_sleep(5);  /*Wait 5 milliseconds before processing LVGL timer again*/
    }
}

/* Return the elapsed milliseconds since startup.
 * It needs to be implemented by the user */
uint32_t my_get_millis(void)
{
    return my_tick_ms;
}

/* Copy rendered image to screen.
 * This needs to be implemented by the user. */
void my_flush_cb(lv_display_t * disp, const lv_area_t * area, uint8_t * px_buf)
{
    /* Show the rendered image on the display */
    my_display_update(area, px_buf);

    /* Indicate that the buffer is available.
     * If DMA were used, call in the DMA complete interrupt. */
    lv_display_flush_ready();
}
```







## 小组件

小组件是UI的基本构建块。例如：按钮（lv_button）滑块（lv_slider）下拉列表（lv_dropdown）图表（lv_chart）等。

可以通过调用其各自的创建函数来动态创建控件。

这 create 函数返回一个指针，该指针可用于稍后配置小部件。`lv_obj_t *`

每个创建函数都有一个参数，用于定义将新函数添加到哪个控件。`parent`

例如：

```c
lv_obj_t * my_button1 = lv_button_create(lv_screen_active());
lv_obj_t * my_label1 = lv_label_create(my_button1);
```

如果不再需要小部件或屏幕，可以通过调用 `lv_obj_delete`（my_button1） 将其删除

要更改小部件的属性，可以使用两组函数：

-   `lv_obj_...()`通用属性的函数，例如 `lv_obj_set_width`、`lv_obj_add_style` 等。这些内容在 `常见控件功能中` 进行了介绍。
-   `lv_<widget_type>_...()`特定于类型的属性的函数，例如 `lv_label_set_text` 、`lv_slider_set_value` 等。

下面是一个示例，其中还显示了一些大小的非像素单位：

```c
lv_obj_t * my_button1 = lv_button_create(lv_screen_active());
/* 创建按钮对象，将其父对象设置为当前活动屏幕 */

lv_obj_set_size(my_button1, lv_pct(100), LV_SIZE_CONTENT);
/* 设置宽度为父对象宽度100%，高度为内容自适应 */

lv_obj_align(my_button1, LV_ALIGN_RIGHT_MID, -20, 0);
/* 右中对齐，水平方向偏移-20像素 */

lv_obj_t * my_label1 = lv_label_create(my_button1);
/* 在按钮内创建标签对象 */

lv_label_set_text_fmt(my_label1, "Click me!");
/* 设置标签文本内容为"Click me!" */

lv_obj_set_style_text_color(my_label1, lv_color_hex(0xff0000), 0);
/* 设置文本颜色为红色 */
```



## 事件

事件用于通知应用程序小部件发生了某些事情。 你可以将一个或多个回调分配给一个小组件，当小组件 被点击、释放、拖动、被删除等。

回调的分配方式如下：

```
lv_obj_add_event_cb(btn, my_btn_event_cb, LV_EVENT_CLICKED, NULL);

void my_btn_event_cb(lv_event_t * e)
{
    printf("Clicked\n");
}
```

事件回调接收参数 lv_event_t * e 包含 当前事件代码和其他事件相关信息。当前事件代码可以 通过以下方式检索：

```
lv_event_code_t code = lv_event_get_code(e);
```

触发事件的小部件可以通过以下方式检索：

```
lv_obj_t * widget = lv_event_get_target_obj(e);
```



## 部件和状态

### 部件

小组件由一个或多个部件构建。

通过使用零件，可以对零件应用不同的样式小部件的。

阅读小组件的文档，了解它使用哪些部分。





### 向小部件添加样式

之后，它可以添加到小部件中：

```
lv_obj_add_style(my_button1, &style1, 0); /*0 means add to the main part and default state*/
lv_obj_add_style(my_checkbox1, &style1, LV_STATE_DISABLED); /*Add to checkbox's disabled state*/
lv_obj_add_style(my_slider1, &style1, LV_PART_KNOB | LV_STATE_PRESSED); /*Add to the slider's knob pressed state*/
```



## 继承

某些属性（尤其是与文本相关的属性）可以继承。这 表示如果未在 Widget 中设置属性，则将在 它的父母。例如，您可以在屏幕的 style 和该屏幕上的所有文本都将默认继承它，除非 font 在小部件或其父级之一上指定。

### 本地风格

局部样式属性也可以添加到控件中。这会创建一个 style 的样式，它驻留在 Widget 中，并且仅由该 Widget 使用：

```
lv_obj_set_style_bg_color(slider1, lv_color_hex(0x2080bb), LV_PART_INDICATOR | LV_STATE_PRESSED);
```



