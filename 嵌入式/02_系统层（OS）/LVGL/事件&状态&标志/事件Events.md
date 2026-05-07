### 四、事件（Events）— 交互与生命周期的回调触发点

事件贯穿**用户交互、绘制渲染、状态变更、生命周期**等多个维度。

#### 4.1 输入与交互事件

| 事件                  | 触发时机                                                     |
| --------------------- | ------------------------------------------------------------ |
| `PRESSED`             | 被按下（瞬间触发一次）                                       |
| `PRESSING`            | 持续按下中，不断触发                                         |
| `PRESS_LOST`          | 按下状态意外丢失                                             |
| `RELEASED`            | 手指/光标在对象上释放                                        |
| `SHORT_CLICKED`       | 短按释放（与长按互斥）                                       |
| `LONG_PRESSED`        | 长按达到阈值（触发一次）                                     |
| `LONG_PRESSED_REPEAT` | 长按状态下周期性触发                                         |
| `CLICKED`             | 完整点击（按下+释放均在对象内）                              |
| `SCROLL_BEGIN`        | 滚动开始                                                     |
| `SCROLL_END`          | 滚动结束                                                     |
| `SCROLL`              | 滚动进行中，持续触发                                         |
| `GESTURE`             | 检测到手势滑动，需调用 `lv_indev_get_gesture_dir()` 获取方向 |
| `KEY`                 | 外部按键作用于焦点对象                                       |
| `FOCUSED`             | 获得输入焦点                                                 |
| `DEFOCUSED`           | 失去输入焦点                                                 |
| `LEAVE`               | 光标/手指从对象上移开                                        |

> 手势方向统一通过 `LV_EVENT_GESTURE` 处理，无独立左右滑事件。

#### 4.2 绘制与渲染事件

| 事件                 | 触发时机                           |
| -------------------- | ---------------------------------- |
| `DRAW_MAIN_BEGIN`    | 主绘制开始前                       |
| `DRAW_MAIN`          | 主绘制进行中                       |
| `DRAW_MAIN_END`      | 主绘制结束后                       |
| `DRAW_POST_BEGIN`    | 后期绘制（覆盖层/特效）开始前      |
| `DRAW_POST`          | 后期绘制进行中                     |
| `DRAW_POST_END`      | 后期绘制结束后                     |
| `DRAW_PART_BEGIN`    | 对象子部件绘制前                   |
| `DRAW_PART_END`      | 对象子部件绘制后                   |
| `REFR_EXT_DRAW_SIZE` | 需扩展绘制区域时（如阴影、外发光） |
| `HIT_TEST`           | 判断点是否命中对象内部区域         |
| `COVER_CHECK`        | 判断对象是否完全遮挡背景           |

#### 4.3 状态与值变更事件

| 事件            | 触发时机                       |
| --------------- | ------------------------------ |
| `VALUE_CHANGED` | 核心数值变化（滑块、复选框等） |
| `CHECKED`       | 被置为选中态                   |
| `UNCHECKED`     | 取消选中态                     |
| `READY`         | 异步初始化完成或动画准备就绪   |
| `CANCEL`        | 操作被取消                     |
| `REFRESH`       | 内容需刷新                     |
| `INSERT`        | 子对象被插入                   |
| `DELETE`        | 对象即将被删除                 |
| `CHILD_CHANGED` | 子对象列表发生增删或重排       |
| `CHILD_CREATED` | 新子对象被创建并添加           |
| `CHILD_DELETED` | 子对象被删除                   |

#### 4.4 屏幕与生命周期事件

| 事件                  | 触发时机     |
| --------------------- | ------------ |
| `SCREEN_LOAD_START`   | 屏幕开始加载 |
| `SCREEN_LOADED`       | 屏幕加载完成 |
| `SCREEN_UNLOAD_START` | 屏幕开始卸载 |
| `SCREEN_UNLOADED`     | 屏幕卸载完成 |

#### 4.5 布局、样式与几何事件

| 事件             | 触发时机                |
| ---------------- | ----------------------- |
| `SIZE_CHANGED`   | 宽高改变                |
| `STYLE_CHANGED`  | 样式属性被修改          |
| `LAYOUT_CHANGED` | 布局或位置重排          |
| `GET_SELF_SIZE`  | LVGL 内部计算对象大小时 |

