# LVGL 样式 Style 详解

样式是LVGL中用于定义控件外观的机制，类似于网页开发中的CSS。

## 一、样式的本质

样式是一个 lv_style_t 结构体，存储了各种外观属性，如背景色、边框宽度、字体等。样式本身不依附于任何控件，可以被多个控件共享使用。

一个控件可以同时应用多个样式，样式之间按照优先级进行合并。

## 二、样式的核心概念

| 概念     | 说明                                 |
| -------- | ------------------------------------ |
| 样式属性 | 具体的外观设置，如背景色、边框大小   |
| 样式状态 | 同一控件在不同交互状态下使用不同样式 |
| 样式部件 | 控件的不同组成部分可独立设置样式     |
| 样式继承 | 子控件可继承父控件的部分样式属性     |
| 样式共享 | 多个控件共用同一个样式对象，节省内存 |

## 三、样式属性分类

| 分类   | 属性示例                                               | 说明                   |
| ------ | ------------------------------------------------------ | ---------------------- |
| 背景   | bg_color, bg_opa, bg_grad_color                        | 背景色、透明度、渐变   |
| 边框   | border_color, border_width, border_side                | 边框颜色、宽度、方向   |
| 轮廓   | outline_color, outline_width, outline_pad              | 轮廓颜色、宽度、外扩   |
| 阴影   | shadow_color, shadow_width, shadow_offset              | 阴影颜色、宽度、偏移   |
| 文字   | text_color, text_font, text_letter_space               | 文字颜色、字体、字间距 |
| 图像   | image_recolor, image_recolor_opa                       | 图像重新着色           |
| 线条   | line_color, line_width, line_dash_width                | 线条颜色、宽度、虚线   |
| 圆弧   | arc_color, arc_width, arc_rounded                      | 圆弧颜色、宽度、圆头   |
| 滚动   | scrollbar_width, scrollbar_bg_color                    | 滚动条样式             |
| 内边距 | pad_top, pad_bottom, pad_left, pad_right               | 内容与边缘的间距       |
| 外边距 | margin_top, margin_bottom, margin_left, margin_right   | 控件与外部间距         |
| 布局   | layout, flex_flow, grid_column_dsc                     | 布局方式               |
| 变换   | transform_width, transform_height, translate_x         | 尺寸变换、位置偏移     |
| 过渡   | transition_property, transition_time, transition_delay | 样式变化过渡效果       |

## 四、样式状态

同一控件在不同交互状态下可显示不同样式。

| 状态              | 说明                         |
| ----------------- | ---------------------------- |
| LV_STATE_DEFAULT  | 默认状态，正常外观           |
| LV_STATE_CHECKED  | 选中状态，如开关打开         |
| LV_STATE_FOCUSED  | 焦点状态，通过触摸或键盘获得 |
| LV_STATE_PRESSED  | 按下状态，正在被点击         |
| LV_STATE_DISABLED | 禁用状态，灰化不可交互       |
| LV_STATE_SCROLLED | 滚动状态，内容正在滚动       |

状态可通过位或运算组合使用，例如 PRESSED 和 CHECKED 可同时存在。

## 五、样式部件

将控件分解为多个可独立设置样式的部分。

| 部件              | 说明                   | 适用控件             |
| ----------------- | ---------------------- | -------------------- |
| LV_PART_MAIN      | 主体部分，所有控件都有 | 所有控件             |
| LV_PART_SCROLLBAR | 滚动条                 | 有滚动功能的容器     |
| LV_PART_INDICATOR | 指示器                 | 滑动条、进度条       |
| LV_PART_KNOB      | 旋钮                   | 滑动条、旋钮控件     |
| LV_PART_SELECTED  | 选中项                 | 下拉列表、表格、滚轮 |
| LV_PART_ITEMS     | 列表项                 | 列表、表格           |
| LV_PART_TICKS     | 刻度                   | 滑动条、仪表         |
| LV_PART_CURSOR    | 光标                   | 文本区域             |

## 六、API函数

### 样式初始化与销毁

| 函数           | 作用                         |
| -------------- | ---------------------------- |
| lv_style_init  | 初始化样式结构体             |
| lv_style_reset | 重置样式，释放动态分配的资源 |

### 样式属性设置

| 函数                      | 作用           |
| ------------------------- | -------------- |
| lv_style_set_bg_color     | 设置背景色     |
| lv_style_set_bg_opa       | 设置背景透明度 |
| lv_style_set_border_width | 设置边框宽度   |
| lv_style_set_border_color | 设置边框颜色   |
| lv_style_set_text_font    | 设置文字字体   |
| lv_style_set_text_color   | 设置文字颜色   |
| lv_style_set_pad_all      | 设置所有内边距 |

类似的setter函数覆盖所有属性类型，命名格式统一为 lv_style_set_属性名。

### 对象样式操作

| 函数                    | 作用               |
| ----------------------- | ------------------ |
| lv_obj_add_style        | 为对象添加样式     |
| lv_obj_remove_style     | 移除对象样式       |
| lv_obj_remove_style_all | 移除对象所有样式   |
| lv_obj_has_style        | 检查是否有指定样式 |

### 样式属性读取

| 函数                      | 作用             |
| ------------------------- | ---------------- |
| lv_obj_get_style_bg_color | 获取对象的背景色 |
| lv_obj_get_style_bg_opa   | 获取背景透明度   |

## 七、样式优先级

当多个样式作用于同一控件时，按以下优先级从高到低合并：

1. 内联样式（直接通过 lv_obj_set_style_xxx 设置）
2. 本地样式（通过 lv_obj_add_style 添加，先添加的优先级更高）
3. 主题样式（LVGL默认主题）
4. 父控件继承的样式

## 八、样式继承

部分样式属性可以从父控件继承到子控件，主要是文字相关属性：

- text_color
- text_font
- text_letter_space
- text_line_space

其他属性如背景、边框等不会继承，保持独立。

## 九、实际应用示例

### 创建样式并应用到按钮

```c
static lv_style_t style_btn;
static lv_style_t style_btn_pressed;

void init_button_styles(void)
{
    lv_style_init(&style_btn);
    lv_style_set_bg_color(&style_btn, lv_color_hex(0x2196F3));
    lv_style_set_border_width(&style_btn, 0);
    lv_style_set_radius(&style_btn, 8);
    
    lv_style_init(&style_btn_pressed);
    lv_style_set_bg_color(&style_btn_pressed, lv_color_hex(0x0b5e8c));
}

void create_styled_button(lv_obj_t * parent)
{
    lv_obj_t * btn = lv_btn_create(parent);
    lv_obj_remove_style_all(btn);
    lv_obj_add_style(btn, &style_btn, 0);
    lv_obj_add_style(btn, &style_btn_pressed, LV_STATE_PRESSED);
    
    lv_obj_t * label = lv_label_create(btn);
    lv_label_set_text(label, "Button");
    lv_obj_center(label);
}
```

### 滚动条样式示例

```c
static lv_style_t style_scrollbar;

void init_scrollbar_style(void)
{
    lv_style_init(&style_scrollbar);
    lv_style_set_width(&style_scrollbar, 4);
    lv_style_set_bg_opa(&style_scrollbar, LV_OPA_COVER);
    lv_style_set_bg_color(&style_scrollbar, lv_color_hex3(0xeee));
    lv_style_set_radius(&style_scrollbar, LV_RADIUS_CIRCLE);
}

void apply_scrollbar_style(lv_obj_t * obj)
{
    lv_obj_add_style(obj, &style_scrollbar, LV_PART_SCROLLBAR);
}
```

## 十、设计要点

| 要点         | 说明                                       |
| ------------ | ------------------------------------------ |
| 样式复用     | 相同样式的控件共用同一个样式对象，节省内存 |
| 状态独立     | 为不同状态设置不同样式，提升交互反馈       |
| 部件分离     | 将复合控件的不同部件独立设置样式           |
| 避免过度嵌套 | 样式继承层级过深会影响性能                 |
| 使用主题     | 整套UI设计建议从主题开始，统一风格         |