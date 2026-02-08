# Z-order

一句话版本：

>   **Z-order 决定“谁压在谁上面画”**

可以把它理解成 **屏幕上的“图层前后顺序”**。

-   **X、Y**：位置
-   **Z**：前后（谁在最上面）

------

## 直觉类比（很贴切）

想象几张纸：

```
┌─────────┐   ← 最上层（最后画）
│  label  │
└─────────┘
┌─────────┐
│  image  │
└─────────┘
┌─────────┐
│  bg     │   ← 最底层（最先画）
└─────────┘
```

👉 **上面的会遮住下面的**

------

# LVGL 里的 Z-order 规则（非常重要）

### 规则只有一句：

>   **同一个父对象下，子对象的创建顺序 = Z-order**

------

## 举个最小例子

```c
lv_obj_t * scr = lv_scr_act();

lv_obj_t * a = lv_obj_create(scr);  // 先创建
lv_obj_t * b = lv_obj_create(scr);  // 后创建
```

结果：

-   `a` 在下面
-   `b` 在上面（会盖住 a）

------

## 所以你这个红色方块

```c
lv_obj_create(scr);
```

如果它是：

-   **最后创建的**
-   那它一定在最上层

------

# 🔀 如何“调整” Z-order？

LVGL 提供了显式 API（你迟早会用到）：

### 放到最顶层

```c
lv_obj_move_foreground(obj);
```

### 放到最底层

```c
lv_obj_move_background(obj);
```

### 精确移动

```c
lv_obj_move_to_index(obj, idx);
```

------

# 🧠 很容易踩坑的点（重点）

## 1️⃣ Z-order ≠ screen 顺序

-   screen 是**不同页面**
-   Z-order 是**同一个 screen 里的前后**

------

## 2️⃣ 不同父对象，Z-order 不可比

```c
scr
 ├── cont1
 │    └── objA
 └── cont2
      └── objB
```

-   `objA` 永远不可能压住 `objB`
-   因为它们**不在同一个父对象**

👉 这是新手最常见误解

------

## 3️⃣ LVGL 是“先画底，再画顶”

渲染顺序是：

```text
父对象
  → 子对象 index 0
  → 子对象 index 1
  → ...
```

后画的 = 在上面

------

# 🎯 Z-order 和你后面 OV7670 的关系（提前点醒）

这点**非常关键**，记住：

>   **摄像头画面 ≈ 一个“背景对象”**

典型结构是：

```text
screen
 ├── camera_img   ← 底层
 ├── crosshair
 ├── FPS label
 └── menu
```

所以你会经常：

```c
lv_obj_move_background(camera_img);
```

否则：

-   菜单被盖住
-   十字线看不到
-   文字消失

------

# 🧩 Z-order 和 Style / Alpha

如果上层对象有：

```c
lv_obj_set_style_bg_opa(obj, LV_OPA_COVER, 0);
```

那它会：

-   **完全遮挡下层**
-   即使下层还在“被绘制”

所以有时候：

-   对象存在
-   但你“看不到”

👉 不是 bug，是 Z-order + OPA

------

# 🧪 一个你可以立刻做的小实验

```c
lv_obj_t * r = lv_obj_create(scr);
lv_obj_set_size(r, 40, 40);
lv_obj_set_style_bg_color(r, lv_color_hex(0xFF0000), 0);

lv_obj_t * g = lv_obj_create(scr);
lv_obj_set_size(g, 40, 40);
lv_obj_set_pos(g, 20, 20);
lv_obj_set_style_bg_color(g, lv_color_hex(0x00FF00), 0);
```

你会看到：

-   绿色盖在红色上

然后试试：

```c
lv_obj_move_foreground(r);
```

------

# 🧠 一句话总结（工程级）

>   **Z-order 是 LVGL 里“最简单但最致命”的概念**

懂它：

-   UI 永远“可控”
    不懂它：
-   你会以为是刷新、DMA、bug

------

如果你愿意，下一步我可以帮你：

-   🧱 设计一个**“摄像头 + UI overlay” 的对象层级**
-   🔄 或讲 **Z-order 在 screen 切换时会发生什么**

你已经走在**非常正确的学习路径上**了。