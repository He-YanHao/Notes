# LVGL 控件总览

本文档对 LVGL 官方列出的所有控件（Widgets）进行分类总结，便于快速查阅与选型。

## 基础交互类

| 控件           | 描述                       |
| -------------- | -------------------------- |
| Button         | 标准按钮，响应点击事件     |
| Button Matrix  | 矩阵按键组，适合数字键盘等 |
| Checkbox       | 复选框，带勾选状态         |
| Drop-Down List | 下拉选择列表               |
| Roller         | 滚轮选择器                 |
| Slider         | 滑动条，调节数值           |
| Switch         | 开关切换控件               |
| Spinbox        | 数字调节输入框             |
| Keyboard       | 虚拟键盘                   |

## 数据展示类

| 控件      | 描述                       |
| --------- | -------------------------- |
| Label     | 文本标签                   |
| Image     | 图片显示                   |
| Arc       | 圆弧进度/指示器            |
| Bar       | 条形进度条                 |
| Chart     | 图表（折线、柱状等）       |
| Led       | LED 状态指示灯             |
| Line      | 线段绘制                   |
| Meter     | 表盘仪表                   |
| Table     | 表格显示                   |
| Spangroup | 富文本（多种样式混排）     |
| Scale     | 刻度尺                     |
| Arc Label | 弧形标签（沿圆弧排列文字） |

## 容器与布局类

| 控件      | 描述                 |
| --------- | -------------------- |
| Container | 基础容器（lv_obj）   |
| Panel     | 带边框和背景的容器   |
| Tabview   | 选项卡视图           |
| Tile View | 磁贴视图（平铺滚动） |
| Window    | 窗口控件（含标题栏） |
| List      | 垂直列表             |
| Menu      | 菜单结构             |

## 高级与特效类

| 控件            | 描述                    |
| --------------- | ----------------------- |
| Animation Image | 序列帧动画图片          |
| GIF             | GIF 动画播放            |
| Lottie          | Lottie 动画（需外部库） |
| 3D Texture      | 3D 纹理显示             |
| Canvas          | 画布，自由绘图          |
| QRCode          | 二维码生成              |
| Calendar        | 日历控件                |
| Message Box     | 消息对话框              |
| Colorwheel      | 颜色选择轮              |
| Spinner         | 加载旋转动画            |

## 输入与文本类

| 控件       | 描述           |
| ---------- | -------------- |
| Textarea   | 多行文本输入区 |
| Pinyin IME | 拼音输入法辅助 |

## 其他/未分类

| 控件       | 描述     |
| ---------- | -------- |
| New Widget | 预留扩展 |
