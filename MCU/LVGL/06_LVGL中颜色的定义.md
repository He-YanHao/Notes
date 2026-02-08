# LVGL中颜色的定义

## 颜色位数的定义 lv_color_t

首先有一个宏定义定义了颜色的位数：

```c
#define LV_COLOR_DEPTH 16
```

接下来

```c
1. typedef LV_CONCAT3(lv_color, LV_COLOR_DEPTH, _t) lv_color_t;
2. #define LV_CONCAT3(x, y, z) _LV_CONCAT3(x, y, z)
3. #define _LV_CONCAT3(x, y, z) x ## y ## z
```

第一行会将颜色位数带入，变为：

```c
LV_CONCAT3(lv_color, 16, _t)
```

第二行又会将其替换为：

```c
_LV_CONCAT3(lv_color, 16, _t)
```

第三行又会将其连接为：

```c
lv_color16_t
```

回到第一行，就是

```c
typedef lv_color16_t lv_color_t;
```

因此

```
lv_color_t * color_p
```

里面的 `lv_color_t` 是 `lv_color16_t` 类型。





## lv_color_hex

lv_color_hex(); 函数会根据 `LV_COLOR_DEPTH` 的位数自动将RGB888的配置转化为对应的配置，因此无限手动计算这些颜色的对应关系。

```c
static inline lv_color_t lv_color_hex(uint32_t c)
{
#if LV_COLOR_DEPTH == 16
    lv_color_t r;
#if LV_COLOR_16_SWAP == 0
    /* Convert a 4 bytes per pixel in format ARGB32 to R5G6B5 format
        naive way (by calling lv_color_make with components):
                    r = ((c & 0xFF0000) >> 19)
                    g = ((c & 0xFF00) >> 10)
                    b = ((c & 0xFF) >> 3)
                    rgb565 = (r << 11) | (g << 5) | b
        That's 3 mask, 5 bitshift and 2 or operations

        A bit better:
                    r = ((c & 0xF80000) >> 8)
                    g = ((c & 0xFC00) >> 5)
                    b = ((c & 0xFF) >> 3)
                    rgb565 = r | g | b
        That's 3 mask, 3 bitshifts and 2 or operations */
    r.full = (uint16_t)(((c & 0xF80000) >> 8) | ((c & 0xFC00) >> 5) | ((c & 0xFF) >> 3));
#else
    /* We want: rrrr rrrr GGGg gggg bbbb bbbb => gggb bbbb rrrr rGGG */
    r.full = (uint16_t)(((c & 0xF80000) >> 16) | ((c & 0xFC00) >> 13) | ((c & 0x1C00) << 3) | ((c & 0xF8) << 5));
#endif
    return r;
#elif LV_COLOR_DEPTH == 32
    lv_color_t r;
    r.full = c | 0xFF000000;
    return r;
#else /*LV_COLOR_DEPTH == 8*/
    return lv_color_make((uint8_t)((c >> 16) & 0xFF), (uint8_t)((c >> 8) & 0xFF), (uint8_t)(c & 0xFF));
#endif
}
```

