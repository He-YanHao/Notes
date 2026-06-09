# 布局 Layout

布局是LVGL中自动排列子对象位置的机制，与手动指定坐标（`lv_obj_set_pos`）不同，布局会根据容器大小和预设规则自动计算子对象位置。

## 一、布局与手动定位的区别

| 方式     | 说明                             | 适用场景             |
| -------- | -------------------------------- | -------------------- |
| 手动定位 | 用 `lv_obj_set_pos` 指定绝对坐标 | 少量固定位置的控件   |
| 布局     | 容器自动排列子对象               | 复杂界面、响应式设计 |

布局的核心优势：当容器大小改变时，子对象会自动重新排列，无需手动计算。

## 二、LVGL支持的布局类型

| 布局           | 说明                       |
| -------------- | -------------------------- |
| LV_LAYOUT_NONE | 无布局，手动定位（默认）   |
| LV_LAYOUT_FLEX | 弹性布局，单行或单列排列   |
| LV_LAYOUT_GRID | 网格布局，多行多列表格排列 |

启用布局：

```c
lv_obj_set_layout(cont, LV_LAYOUT_FLEX);
```

## 三、Flex 布局

Flex用于单行或单列排列，类似于CSS Flexbox。

### 基本用法

```c
lv_obj_t * cont = lv_obj_create(parent);
lv_obj_set_layout(cont, LV_LAYOUT_FLEX);
```

### 排列方向

```c
lv_obj_set_flex_flow(cont, LV_FLEX_FLOW_ROW);       // 水平排列
lv_obj_set_flex_flow(cont, LV_FLEX_FLOW_COLUMN);    // 垂直排列
lv_obj_set_flex_flow(cont, LV_FLEX_FLOW_ROW_WRAP);  // 水平换行
```

### 对齐方式

```c
// 主轴对齐（水平方向）
lv_obj_set_flex_align(cont, 
    LV_FLEX_ALIGN_START,      // 左对齐
    LV_FLEX_ALIGN_CENTER,     // 居中对齐
    LV_FLEX_ALIGN_END,        // 右对齐
    LV_FLEX_ALIGN_SPACE_AROUND,   // 环绕分布
    LV_FLEX_ALIGN_SPACE_BETWEEN,  // 两端对齐
    LV_FLEX_ALIGN_SPACE_EVENLY);  // 均匀分布
```

### Flex 示例

```c
// 水平居中排列的按钮组
lv_obj_set_layout(cont, LV_LAYOUT_FLEX);
lv_obj_set_flex_flow(cont, LV_FLEX_FLOW_ROW);
lv_obj_set_flex_align(cont, LV_FLEX_ALIGN_CENTER, LV_FLEX_ALIGN_CENTER, LV_FLEX_ALIGN_CENTER);

for(int i = 0; i < 3; i++) {
    lv_obj_t * btn = lv_btn_create(cont);
    lv_obj_t * label = lv_label_create(btn);
    lv_label_set_text_fmt(label, "Btn %d", i);
}
```

## 四、Grid 布局

Grid用于多行多列表格排列，需定义列宽和行高。

### 基本用法

```c
lv_obj_t * cont = lv_obj_create(parent);
lv_obj_set_layout(cont, LV_LAYOUT_GRID);
```

### 定义列和行

```c
// 列定义：3列，第一列100px，第二列占剩余空间，第三列自适应内容
static const int32_t col_dsc[] = {100, LV_GRID_FR(1), LV_GRID_CONTENT, LV_GRID_TEMPLATE_LAST};

// 行定义：2行，第一行50px，第二行自适应内容
static const int32_t row_dsc[] = {50, LV_GRID_CONTENT, LV_GRID_TEMPLATE_LAST};

lv_obj_set_grid_dsc_array(cont, col_dsc, row_dsc);
```

### 列/行尺寸类型

| 类型            | 说明                   |
| --------------- | ---------------------- |
| 固定数值        | 固定像素宽度/高度      |
| LV_GRID_CONTENT | 根据子对象内容自动调整 |
| LV_GRID_FR(x)   | 按比例分配剩余空间     |

### 放置子对象

```c
// 将对象放在第0行、第1列
lv_obj_set_grid_cell(obj, LV_GRID_ALIGN_CENTER, 1, 1, LV_GRID_ALIGN_CENTER, 0, 1);
```

参数说明：

| 参数     | 含义         |
| -------- | ------------ |
| h_align  | 水平对齐方式 |
| col_pos  | 起始列索引   |
| col_span | 占据列数     |
| v_align  | 垂直对齐方式 |
| row_pos  | 起始行索引   |
| row_span | 占据行数     |

### Grid 示例

```c
static const int32_t col_dsc[] = {LV_GRID_FR(1), LV_GRID_FR(2), LV_GRID_TEMPLATE_LAST};
static const int32_t row_dsc[] = {40, 40, LV_GRID_TEMPLATE_LAST};

lv_obj_set_grid_dsc_array(cont, col_dsc, row_dsc);

// 左上角按钮：第0行第0列
lv_obj_t * btn1 = lv_btn_create(cont);
lv_obj_set_grid_cell(btn1, LV_GRID_ALIGN_STRETCH, 0, 1, LV_GRID_ALIGN_STRETCH, 0, 1);

// 右侧按钮：跨第0行第1-2列
lv_obj_t * btn2 = lv_btn_create(cont);
lv_obj_set_grid_cell(btn2, LV_GRID_ALIGN_STRETCH, 1, 2, LV_GRID_ALIGN_STRETCH, 0, 1);
```

## 五、布局样式

### 内边距

```c
lv_obj_set_style_pad_top(cont, 10, 0);     // 上内边距
lv_obj_set_style_pad_bottom(cont, 10, 0);  // 下内边距
lv_obj_set_style_pad_left(cont, 10, 0);    // 左内边距
lv_obj_set_style_pad_right(cont, 10, 0);   // 右内边距
```

### 间距

```c
lv_obj_set_style_pad_row(cont, 5, 0);    // 行间距
lv_obj_set_style_pad_column(cont, 5, 0); // 列间距
```

## 六、忽略布局

子对象可设置标志使其脱离布局管理：

```c
lv_obj_add_flag(obj, LV_OBJ_FLAG_IGNORE_LAYOUT);
```

应用场景：装饰元素、浮动按钮、需要绝对定位的特殊控件。

## 七、布局选择指南

| 场景                    | 推荐布局              |
| ----------------------- | --------------------- |
| 垂直排列的菜单/列表     | Flex Column           |
| 水平排列的按钮组        | Flex Row              |
| 工具栏图标              | Flex Row Space Evenly |
| 表单布局（标签+输入框） | Grid                  |
| 仪表盘卡片              | Grid                  |
| 单行滚动区域            | Flex Row Wrap         |
| 需要绝对定位的装饰      | IGNORE_LAYOUT         |

## 八、总结

| 要点               | 说明               |
| ------------------ | ------------------ |
| 布局是自动排列机制 | 无需手动计算坐标   |
| Flex用于单行/单列  | 方向和对齐灵活     |
| Grid用于多行多列   | 适合复杂表格布局   |
| 可混合使用         | 容器嵌套不同布局   |
| IGNORE_LAYOUT      | 让特定控件脱离布局 |