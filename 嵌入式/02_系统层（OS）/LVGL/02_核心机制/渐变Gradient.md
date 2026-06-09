# 渐变 Gradient

渐变是LVGL中背景颜色的平滑过渡效果，支持水平、线性、径向和锥形四种类型。

## 一、渐变类型总览

| 类型     | 效果             | 适用场景           |
| -------- | ---------------- | ------------------ |
| 水平渐变 | 从左到右颜色过渡 | 进度条、标题栏     |
| 线性渐变 | 任意方向线性过渡 | 背景装饰、按钮     |
| 径向渐变 | 圆形放射状过渡   | 圆形控件、光晕效果 |
| 锥形渐变 | 绕中心旋转过渡   | 色轮、仪表盘       |

## 二、渐变数据结构

```c
typedef struct {
    lv_grad_stop_t stops[LV_GRAD_MAX_STOPS];  // 断点数组（最多8个）
    uint8_t stop_count;                       // 实际断点数量
    lv_grad_dir_t dir;                        // 渐变方向
    union {
        lv_grad_linear_t linear;              // 线性渐变参数
        lv_grad_radial_t radial;              // 径向渐变参数
        lv_grad_conical_t conical;            // 锥形渐变参数
    } params;
} lv_grad_dsc_t;
```

## 三、渐变断点

每个断点定义颜色、透明度和位置。

```c
typedef struct {
    lv_color_t color;  // 颜色
    lv_opa_t opa;      // 透明度（0-255）
    uint8_t frac;      // 位置（0-255，对应0%-100%）
} lv_grad_stop_t;
```

### 断点位置示例

| frac值 | 百分比位置   |
| ------ | ------------ |
| 0      | 0%（起点）   |
| 64     | 25%          |
| 128    | 50%          |
| 192    | 75%          |
| 255    | 100%（终点） |

## 四、API函数

### 断点初始化

```c
void lv_grad_init_stops(lv_grad_dsc_t * grad, 
    const lv_color_t * colors,  // 颜色数组
    const lv_opa_t * opas,      // 透明度数组（可为NULL）
    const uint8_t * fracs,      // 位置数组（可为NULL，则均匀分布）
    uint8_t stop_count);        // 断点数量
```

### 类型初始化

```c
// 水平渐变
void lv_grad_horizontal_init(lv_grad_dsc_t * grad);

// 线性渐变
void lv_grad_linear_init(lv_grad_dsc_t * grad, 
    int16_t x1, int16_t y1,  // 起点坐标
    int16_t x2, int16_t y2,  // 终点坐标
    lv_grad_extend_t extend); // 扩展模式

// 径向渐变
void lv_grad_radial_init(lv_grad_dsc_t * grad, 
    int16_t cx, int16_t cy,  // 圆心
    int16_t cw, int16_t ch,  // 圆上一点
    lv_grad_extend_t extend);

// 设置焦点
void lv_grad_radial_set_focal(lv_grad_dsc_t * grad, 
    int16_t fx, int16_t fy,  // 焦点坐标
    int16_t fw);              // 焦点宽度

// 锥形渐变
void lv_grad_conical_init(lv_grad_dsc_t * grad, 
    int16_t cx, int16_t cy,  // 中心点
    int16_t start_angle,      // 起始角度（0-359）
    int16_t end_angle,        // 结束角度
    lv_grad_extend_t extend);
```

## 五、应用样式

```c
static lv_style_t style;
lv_style_init(&style);
lv_style_set_bg_grad(&style, &grad_dsc);  // 设置渐变
lv_style_set_bg_color(&style, lv_color_hex(0x000000));  // 渐变底色
```

## 六、示例

### 水平渐变

```c
static const lv_color_t colors[] = {
    lv_color_hex(0xFF0000),  // 红色
    lv_color_hex(0x00FF00),  // 绿色
};
static const uint8_t fracs[] = {64, 192};  // 25%和75%位置

static lv_grad_dsc_t grad;
lv_grad_init_stops(&grad, colors, NULL, fracs, 2);
lv_grad_horizontal_init(&grad);

lv_obj_t * obj = lv_obj_create(parent);
lv_obj_set_style_bg_grad(obj, &grad, 0);
```

### 线性渐变

```c
static lv_grad_dsc_t grad;
lv_grad_init_stops(&grad, colors, NULL, NULL, 2);
lv_grad_linear_init(&grad, 0, 0, 100, 100, LV_GRAD_EXTEND_PAD);
```

### 径向渐变

```c
static lv_grad_dsc_t grad;
lv_grad_init_stops(&grad, colors, NULL, NULL, 2);
lv_grad_radial_init(&grad, 50, 50, 100, 50, LV_GRAD_EXTEND_PAD);
lv_grad_radial_set_focal(&grad, 30, 30, 10);
```

### 锥形渐变

```c
static lv_grad_dsc_t grad;
lv_grad_init_stops(&grad, colors, NULL, NULL, 2);
lv_grad_conical_init(&grad, lv_pct(50), lv_pct(50), 0, 180, LV_GRAD_EXTEND_PAD);
```

## 七、扩展模式

| 模式                   | 说明                         |
| ---------------------- | ---------------------------- |
| LV_GRAD_EXTEND_PAD     | 超出渐变区域使用边缘颜色填充 |
| LV_GRAD_EXTEND_REFLECT | 镜像重复渐变                 |
| LV_GRAD_EXTEND_REPEAT  | 重复渐变                     |

## 八、渐变方向枚举

```c
enum {
    LV_GRAD_DIR_NONE,      // 无渐变
    LV_GRAD_DIR_VER,       // 垂直渐变
    LV_GRAD_DIR_HOR,       // 水平渐变
    LV_GRAD_DIR_LINEAR,    // 线性渐变
    LV_GRAD_DIR_RADIAL,    // 径向渐变
    LV_GRAD_DIR_CONICAL,   // 锥形渐变
};
```

## 九、编译配置

```c
#define LV_USE_DRAW_SW_COMPLEX_GRADIENTS 1  // 启用复杂渐变
```

## 十、使用要点

| 要点     | 说明                       |
| -------- | -------------------------- |
| 断点数量 | 最多8个                    |
| 性能影响 | 复杂渐变增加渲染开销       |
| 圆形控件 | 径向渐变适合圆形对象       |
| 渐变底色 | 需同时设置bg_color         |
| 透明度   | 可通过grad_opa实现渐变透明 |