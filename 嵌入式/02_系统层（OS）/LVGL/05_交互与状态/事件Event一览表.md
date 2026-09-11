# 事件Event一览表

## 输入与交互事件

| 事件                         | 触发时机                                               |
| ---------------------------- | ------------------------------------------------------ |
| LV_EVENT_PRESSED             | 被按下，瞬间触发一次                                   |
| LV_EVENT_PRESSING            | 持续按下中，不断触发                                   |
| LV_EVENT_PRESS_LOST          | 按下状态意外丢失                                       |
| LV_EVENT_RELEASED            | 手指或光标在对象上释放                                 |
| LV_EVENT_SHORT_CLICKED       | 短按释放，与长按互斥                                   |
| LV_EVENT_CLICKED             | 完整点击，按下和释放均在对象内                         |
| LV_EVENT_LONG_PRESSED        | 长按达到阈值，触发一次                                 |
| LV_EVENT_LONG_PRESSED_REPEAT | 长按状态下周期性触发                                   |
| LV_EVENT_SCROLL_BEGIN        | 滚动开始                                               |
| LV_EVENT_SCROLL_END          | 滚动结束                                               |
| LV_EVENT_SCROLL              | 滚动进行中，持续触发                                   |
| LV_EVENT_GESTURE             | 检测到手势滑动，需调用lv_indev_get_gesture_dir获取方向 |
| LV_EVENT_KEY                 | 外部按键作用于焦点对象                                 |
| LV_EVENT_FOCUSED             | 获得输入焦点                                           |
| LV_EVENT_DEFOCUSED           | 失去输入焦点                                           |
| LV_EVENT_LEAVE               | 光标或手指从对象上移开                                 |

## 绘制与渲染事件

| 事件                        | 触发时机                         |
| --------------------------- | -------------------------------- |
| LV_EVENT_DRAW_MAIN_BEGIN    | 主绘制开始前                     |
| LV_EVENT_DRAW_MAIN          | 主绘制进行中                     |
| LV_EVENT_DRAW_MAIN_END      | 主绘制结束后                     |
| LV_EVENT_DRAW_POST_BEGIN    | 后期绘制开始前                   |
| LV_EVENT_DRAW_POST          | 后期绘制进行中                   |
| LV_EVENT_DRAW_POST_END      | 后期绘制结束后                   |
| LV_EVENT_DRAW_PART_BEGIN    | 对象子部件绘制前                 |
| LV_EVENT_DRAW_PART_END      | 对象子部件绘制后                 |
| LV_EVENT_REFR_EXT_DRAW_SIZE | 需扩展绘制区域时，如阴影或外发光 |
| LV_EVENT_HIT_TEST           | 判断点是否命中对象内部区域       |
| LV_EVENT_COVER_CHECK        | 判断对象是否完全遮挡背景         |

## 状态与值变更事件

| 事件                   | 触发时机                     |
| ---------------------- | ---------------------------- |
| LV_EVENT_VALUE_CHANGED | 核心数值变化，如滑块、复选框 |
| LV_EVENT_CHECKED       | 被置为选中态                 |
| LV_EVENT_UNCHECKED     | 取消选中态                   |
| LV_EVENT_READY         | 异步初始化完成或动画准备就绪 |
| LV_EVENT_CANCEL        | 操作被取消                   |
| LV_EVENT_REFRESH       | 内容需要刷新                 |
| LV_EVENT_INSERT        | 子对象被插入                 |
| LV_EVENT_DELETE        | 对象即将被删除               |
| LV_EVENT_CHILD_CHANGED | 子对象列表发生增删或重排     |
| LV_EVENT_CHILD_CREATED | 新子对象被创建并添加         |
| LV_EVENT_CHILD_DELETED | 子对象被删除                 |

## 屏幕与生命周期事件

| 事件                         | 触发时机     |
| ---------------------------- | ------------ |
| LV_EVENT_SCREEN_LOAD_START   | 屏幕开始加载 |
| LV_EVENT_SCREEN_LOADED       | 屏幕加载完成 |
| LV_EVENT_SCREEN_UNLOAD_START | 屏幕开始卸载 |
| LV_EVENT_SCREEN_UNLOADED     | 屏幕卸载完成 |

## 布局与样式事件

| 事件                    | 触发时机               |
| ----------------------- | ---------------------- |
| LV_EVENT_SIZE_CHANGED   | 宽高改变               |
| LV_EVENT_STYLE_CHANGED  | 样式属性被修改         |
| LV_EVENT_LAYOUT_CHANGED | 布局或位置重排         |
| LV_EVENT_GET_SELF_SIZE  | LVGL内部计算对象大小时 |

