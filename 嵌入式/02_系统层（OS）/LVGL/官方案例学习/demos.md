# demos

包含：

- **benchmark**：性能测试，测量帧率、渲染时间等指标
- **keypad_encoder**：按键/编码器输入示例，演示非触摸交互
- **music**：音乐播放器UI，展示复杂动画和列表
- **render**：渲染特性测试，如旋转、缩放、图层混合
- **stress**：压力测试，持续创建/删除对象检测内存泄漏
- **vector_graphic**：矢量图形示例（如SVG）
- **widgets**：控件合集，展示LVGL所有内置组件（按钮、滑块、图表等）

调用 `lv_demo_xxx()` 即可启动对应demo，例如：

- `lv_demo_widgets()`
- `lv_demo_benchmark()`

