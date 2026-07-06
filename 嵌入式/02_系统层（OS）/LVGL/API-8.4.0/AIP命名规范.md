# AIP命名规范

## 词汇

### 缩写词

以下缩写词可以使用且被允许：

- `dsc`：描述符
- `param`：参数
- `indev`：输入设备
- `anim`：动画
- `buf`：缓冲区
- `str`：字符串
- `min/max`：最小值/最大值
- `alloc`：分配
- `ctrl`：控制
- `pos`：位置

### 关键词

- `create`：创建
- `source`：源
- `screens`：屏幕

## 组件AIP

### gif

```C
lv_obj_t * gif = lv_gif_create(lv_scr_act());
lv_gif_set_src(gif, "A:image/stm32.gif");
lv_obj_align(gif, LV_ALIGN_CENTER, 0, 0);
```

