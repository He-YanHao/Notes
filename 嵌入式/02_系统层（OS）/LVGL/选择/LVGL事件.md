# LVGL事件

LVGL的事件体系覆盖了用户交互、绘制渲染、对象生命周期和状态变更等多个维度。以下将三份列表按功能逻辑进行归类总结，并说明核心触发场景。

### 一、输入与交互事件
这类事件与触摸、手势、按键等物理操作直接相关，是UI响应用户动作的基础。

| 事件名 | 说明 |
| :--- | :--- |
| `PRESSED` | 对象被按下（瞬间触发一次） |
| `PRESSING` | 持续按下的过程中不断触发 |
| `PRESS_LOST` | 按下状态意外丢失（如手指滑出对象边界或弹窗打断） |
| `RELEASED` | 手指/光标在对象上释放 |
| `SHORT_CLICKED` | 短按（短按释放后触发，与长按互斥） |
| `LONG_PRESSED` | 长按（按下达到阈值时间触发一次） |
| `LONG_PRESSED_REPEAT` | 长按重复（长按状态下周期性触发，用于计数或连续调整） |
| `CLICKED` | 完整点击（按下+释放均在对象内完成） |
| `SCROLL_BEGIN` | 滚动开始（对象内容开始惯性或拖动滚动） |
| `SCROLL_END` | 滚动结束 |
| `SCROLL` | 滚动过程中持续触发 |
| `GESTURE` | 检测到手势滑动，需在回调内调用 `lv_indev_get_gesture_dir` 判断具体方向（左/右/上/下） |
| `KEY` | 外部实体按键（如编码器、键盘）作用于当前焦点对象 |
| `FOCUSED` | 对象获得输入焦点 |
| `DEFOCUSED` | 对象失去输入焦点 |
| `LEAVE` | 光标/手指从对象上移开 |

>   列表中没有独立的 `LV_EVENT_LEFT_SWIPE` 事件。手势检测统一由 **`LV_EVENT_GESTURE`** 处理。在回调函数中通过以下代码片段获取具体方向：
>
>   ```
>   static void event_handler(lv_event_t * e) {
>       if(lv_event_get_code(e) == LV_EVENT_GESTURE) {
>           lv_indev_t * indev = lv_indev_get_act();
>           lv_dir_t dir = lv_indev_get_gesture_dir(indev);
>           if(dir == LV_DIR_LEFT) {
>               // 处理左滑逻辑
>           } else if(dir == LV_DIR_RIGHT) {
>               // 处理右滑逻辑
>           }
>       }
>   }
>   ```

### 二、绘制与渲染事件

这些事件贯穿LVGL绘制管线的各个阶段，常用于自定义绘制效果或进行绘图前的准备。

| 事件名 | 说明 |
| :--- | :--- |
| `DRAW_MAIN_BEGIN` | 主绘制阶段开始前触发 |
| `DRAW_MAIN` | 主绘制进行中 |
| `DRAW_MAIN_END` | 主绘制阶段结束后触发 |
| `DRAW_POST_BEGIN` | 后期绘制（覆盖层/特效）开始前 |
| `DRAW_POST` | 后期绘制进行中 |
| `DRAW_POST_END` | 后期绘制结束后 |
| `DRAW_PART_BEGIN` | 对象特定子部件（如滑块的旋钮、列表的分隔线）绘制前 |
| `DRAW_PART_END` | 对应子部件绘制后 |
| `REFR_EXT_DRAW_SIZE` | 对象需扩展绘制区域时触发（如阴影、外发光超出对象边界） |
| `HIT_TEST` | 判断点是否命中对象内部特定区域（用于透明穿透或自定义点击区域） |
| `COVER_CHECK` | 判断对象是否完全遮挡背景，用于渲染优化（若遮挡则跳过下层绘制） |

### 三、状态与值变更事件
当对象的属性、选中状态或内部数据发生变化时触发，通常用于数据同步或UI联动。

| 事件名 | 说明 |
| :--- | :--- |
| `VALUE_CHANGED` | 对象的核心数值变化（如滑块位置、复选框开关状态） |
| `CHECKED` | 对象被置为选中态（仅适用于有选中属性的控件，如Checkbox） |
| `UNCHECKED` | 对象取消选中态 |
| `READY` | 对象异步初始化完成（如加载网络图片）或动画准备就绪 |
| `CANCEL` | 操作被取消（如进度条中止） |
| `REFRESH` | 对象内容需刷新（常用于自定义控件强制重绘） |
| `INSERT` | 子对象被插入时触发 |
| `DELETE` | 对象即将被删除时触发 |
| `CHILD_CHANGED` | 子对象列表发生增删或重排 |
| `CHILD_CREATED` | 新的子对象被创建并添加 |
| `CHILD_DELETED` | 子对象被删除 |

### 四、屏幕与生命周期事件
针对屏幕（Screen）级别的加载、卸载流程，用于管理页面切换时的资源分配与释放。

| 事件名 | 说明 |
| :--- | :--- |
| `SCREEN_LOAD_START` | 屏幕开始加载动画/初始化 |
| `SCREEN_LOADED` | 屏幕加载完成并显示 |
| `SCREEN_UNLOAD_START` | 屏幕开始卸载（即将被切换走） |
| `SCREEN_UNLOADED` | 屏幕卸载完成 |

### 五、布局、样式与几何事件
与对象外观、尺寸和排列算法紧密相关，是响应式UI的核心事件。

| 事件名 | 说明 |
| :--- | :--- |
| `SIZE_CHANGED` | 对象宽度或高度发生改变 |
| `STYLE_CHANGED` | 对象的样式属性（颜色、字体、边距等）被修改 |
| `LAYOUT_CHANGED` | 对象的布局方式或其父容器布局导致位置重排 |
| `GET_SELF_SIZE` | LVGL内部计算对象大小时触发，可用于自定义控件的动态尺寸逻辑 |

