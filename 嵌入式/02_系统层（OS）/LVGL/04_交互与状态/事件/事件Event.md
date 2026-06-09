# 事件 Event

事件是LVGL中处理用户交互、状态变更和生命周期回调的核心机制。当用户触摸、点击、控件状态变化或发生绘制时，LVGL会触发相应事件，开发者通过注册回调函数来响应。

## 一、事件的基本用法

### 注册事件回调

```c
lv_obj_add_event_cb(btn, my_btn_event_cb, LV_EVENT_CLICKED, NULL);
```

参数说明：
- btn：要监听事件的对象
- my_btn_event_cb：事件发生时的回调函数
- LV_EVENT_CLICKED：要监听的事件类型
- NULL：传递给回调的用户自定义数据

### 回调函数定义

```c
void my_btn_event_cb(lv_event_t * e)
{
    printf("Clicked\n");
}
```

### 回调函数中获取信息

```c
void my_btn_event_cb(lv_event_t * e)
{
    // 获取触发事件的对象
    lv_obj_t * target = lv_event_get_target(e);
    
    // 获取事件类型
    lv_event_code_t code = lv_event_get_code(e);
    
    // 获取用户自定义数据
    void * user_data = lv_event_get_user_data(e);
    
    if(code == LV_EVENT_CLICKED) {
        printf("按钮被点击\n");
    }
}
```

## 二、事件分类

### 输入与交互事件

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

### 绘制与渲染事件

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

### 状态与值变更事件

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

### 屏幕与生命周期事件

| 事件                         | 触发时机     |
| ---------------------------- | ------------ |
| LV_EVENT_SCREEN_LOAD_START   | 屏幕开始加载 |
| LV_EVENT_SCREEN_LOADED       | 屏幕加载完成 |
| LV_EVENT_SCREEN_UNLOAD_START | 屏幕开始卸载 |
| LV_EVENT_SCREEN_UNLOADED     | 屏幕卸载完成 |

### 布局与样式事件

| 事件                    | 触发时机               |
| ----------------------- | ---------------------- |
| LV_EVENT_SIZE_CHANGED   | 宽高改变               |
| LV_EVENT_STYLE_CHANGED  | 样式属性被修改         |
| LV_EVENT_LAYOUT_CHANGED | 布局或位置重排         |
| LV_EVENT_GET_SELF_SIZE  | LVGL内部计算对象大小时 |

## 三、传递自定义数据

```c
// 定义数据结构
typedef struct {
    int button_id;
    char name[32];
} button_data_t;

// 创建并传递数据
button_data_t * data = malloc(sizeof(button_data_t));
data->button_id = 1;
strcpy(data->name, "Button1");

lv_obj_add_event_cb(btn, my_btn_event_cb, LV_EVENT_CLICKED, data);

// 回调中获取数据
void my_btn_event_cb(lv_event_t * e)
{
    button_data_t * data = lv_event_get_user_data(e);
    printf("Button %d clicked\n", data->button_id);
}
```

注意：使用动态分配内存时，需要在适当时候释放，避免内存泄漏。

## 四、移出事件回调

```c
lv_obj_remove_event_cb(obj, event_cb);
lv_obj_remove_event_dsc(obj, event_dsc);
```

## 五、事件冒泡机制

LVGL支持事件冒泡，事件从子对象向上传递到父对象，直到被处理或到达顶层。

## 六、完整示例

```c
void create_button_with_event(void)
{
    lv_obj_t * btn = lv_button_create(lv_screen_active());
    lv_obj_set_size(btn, 100, 50);
    lv_obj_center(btn);
    
    lv_obj_t * label = lv_label_create(btn);
    lv_label_set_text(label, "Click me");
    lv_obj_center(label);
    
    lv_obj_add_event_cb(btn, my_btn_event_cb, LV_EVENT_CLICKED, NULL);
}

void my_btn_event_cb(lv_event_t * e)
{
    lv_obj_t * target = lv_event_get_target(e);
    printf("按钮被点击\n");
}
```

## 七、常见事件处理模式

```c
void common_event_cb(lv_event_t * e)
{
    lv_obj_t * obj = lv_event_get_target(e);
    lv_event_code_t code = lv_event_get_code(e);
    
    switch(code) {
        case LV_EVENT_CLICKED:
            break;
        case LV_EVENT_VALUE_CHANGED:
            break;
        case LV_EVENT_LONG_PRESSED:
            break;
        default:
            break;
    }
}
```

## 八、关键要点

| 要点                         | 说明                          |
| ---------------------------- | ----------------------------- |
| 一个对象可注册多个事件       | 不同类型的事件可绑定不同回调  |
| 一个回调可处理多种事件       | 通过lv_event_get_code判断类型 |
| 事件回调中可调用任何LVGL API | 但需避免耗时操作              |
| 用户数据需自行管理生命周期   | 动态分配的内存要记得释放      |

事件是LVGL交互编程的基础，掌握事件机制后才能构建响应用户操作的各种交互界面。