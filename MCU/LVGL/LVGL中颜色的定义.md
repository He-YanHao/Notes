````c
lv_color_t * color_p
typedef LV_CONCAT3(lv_color, LV_COLOR_DEPTH, _t) lv_color_t;
#define LV_COLOR_DEPTH 16
#define LV_CONCAT3(x, y, z) _LV_CONCAT3(x, y, z)
#define _LV_CONCAT3(x, y, z) x ## y ## z
````



这段定义中

```
lv_color_t
```

被定义为

```
LV_CONCAT3(lv_color, LV_COLOR_DEPTH, _t)
```

的另一个名称



```
LV_COLOR_DEPTH
```

是一个宏 这里是 16

所以

```
LV_CONCAT3(lv_color, 16, _t)
```



```
LV_CONCAT3(x, y, z)
```

又会调用

```
_LV_CONCAT3(x, y, z)
```



```
_LV_CONCAT3(x, y, z)
```

则会将参数拼接在一起



所以

```
LV_CONCAT3(lv_color, 16, _t)
```

会变成

```
lv_color16_t
```

