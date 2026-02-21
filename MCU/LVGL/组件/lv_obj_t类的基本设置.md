# lv_obj_t类的基本设置

## 设置颜色

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类颜色
 * @param  obj：要设置的对象指针
 * @param  value：颜色 按照RGB888格式
 * @param  selector：背景颜色
 * @retval 无 
 */
void lv_obj_set_style_bg_color(struct _lv_obj_t * obj, lv_color_t value, lv_style_selector_t selector);
```

例如：

```c
// 设置block为0x99ccff色 背景纯黑
lv_obj_set_style_bg_color(block, lv_color_hex(0x99ccff), 0);
```



## 设置对齐

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类对齐
 * @param  obj：要设置的对象指针
 * @param  align：对齐方式
 * @param  x_ofs：x坐标
 * @param  y_ofs：y坐标
 * @retval 无 
 */
void lv_obj_align(lv_obj_t * obj, lv_align_t align, lv_coord_t x_ofs, lv_coord_t y_ofs);
```

**lv_align_t对齐方式包含：**

-   **LV_ALIGN_DEFAULT (0)**: 默认对齐方式，通常由系统自动决定，或者使用其他对齐方式时的占位符。
-   **LV_ALIGN_TOP_LEFT**: 在父对象的左上角对齐。
-   **LV_ALIGN_TOP_MID**: 在父对象的顶部中间对齐。
-   **LV_ALIGN_TOP_RIGHT**: 在父对象的右上角对齐。
-   **LV_ALIGN_BOTTOM_LEFT**: 在父对象的左下角对齐。
-   **LV_ALIGN_BOTTOM_MID**: 在父对象的底部中间对齐。
-   **LV_ALIGN_BOTTOM_RIGHT**: 在父对象的右下角对齐。
-   **LV_ALIGN_LEFT_MID**: 在父对象的左边中间对齐。
-   **LV_ALIGN_RIGHT_MID**: 在父对象的右边中间对齐。
-   **LV_ALIGN_CENTER**: 在父对象的中心对齐。

**相对位置**

这些常量表示对象与其父对象的相对偏移量，通常用于设置一个对象相对于另一个对象的位置。

-   **LV_ALIGN_OUT_TOP_LEFT**: 在父对象的顶部左侧外部对齐。
-   **LV_ALIGN_OUT_TOP_MID**: 在父对象的顶部中间外部对齐。
-   **LV_ALIGN_OUT_TOP_RIGHT**: 在父对象的顶部右侧外部对齐。
-   **LV_ALIGN_OUT_BOTTOM_LEFT**: 在父对象的底部左侧外部对齐。
-   **LV_ALIGN_OUT_BOTTOM_MID**: 在父对象的底部中间外部对齐。
-   **LV_ALIGN_OUT_BOTTOM_RIGHT**: 在父对象的底部右侧外部对齐。
-   **LV_ALIGN_OUT_LEFT_TOP**: 在父对象的左侧顶部外部对齐。
-   **LV_ALIGN_OUT_LEFT_MID**: 在父对象的左侧中间外部对齐。
-   **LV_ALIGN_OUT_LEFT_BOTTOM**: 在父对象的左侧底部外部对齐。
-   **LV_ALIGN_OUT_RIGHT_TOP**: 在父对象的右侧顶部外部对齐。
-   **LV_ALIGN_OUT_RIGHT_MID**: 在父对象的右侧中间外部对齐。
-   **LV_ALIGN_OUT_RIGHT_BOTTOM**: 在父对象的右侧底部外部对齐

例如：

```c
//

```



## 设置边框

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：边框宽度的值
 * @param  ：样式选择器 来指定边框样式应用的条件或状态
 * @retval 无 
 */
void lv_obj_set_style_border_width(struct _lv_obj_t * obj, lv_coord_t value, lv_style_selector_t selector);
```

例如：

```c
//

```

## 设置圆角

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

## 设置

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

## 设置

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

## 设置

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

## 设置

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

## 设置

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

## 设置

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

## 设置

函数原型：

```c
/** 
 * @brief  设置lv_obj_t类位置
 * @param  ：
 * @param  ：
 * @param  ：
 * @retval 无 
 */
;
```

例如：

```c
//

```

