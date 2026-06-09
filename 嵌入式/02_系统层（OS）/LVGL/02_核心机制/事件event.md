# 事件 Event

事件是LVGL中处理用户交互的核心机制。当用户触摸、点击、按下按钮或控件状态发生变化时，LVGL会触发相应的事件，开发者通过注册回调函数来响应这些事件。

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

## 二、常用事件类型

| 事件类型               | 触发时机                   |
| ---------------------- | -------------------------- |
| LV_EVENT_PRESSED       | 对象被按下时               |
| LV_EVENT_RELEASED      | 对象被释放时               |
| LV_EVENT_CLICKED       | 点击完成（按下并释放）时   |
| LV_EVENT_LONG_PRESSED  | 长按时触发                 |
| LV_EVENT_VALUE_CHANGED | 对象值改变时（如滑块滑动） |
| LV_EVENT_FOCUSED       | 获得焦点时                 |
| LV_EVENT_DEFOCUSED     | 失去焦点时                 |
| LV_EVENT_DELETE        | 对象被删除时               |
| LV_EVENT_REFRESH       | 需要刷新时                 |

## 三、回调函数中的信息获取

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

## 四、完整示例

```c
void create_button_with_event(void)
{
    // 创建按钮
    lv_obj_t * btn = lv_button_create(lv_screen_active());
    lv_obj_set_size(btn, 100, 50);
    lv_obj_center(btn);
    
    // 添加标签
    lv_obj_t * label = lv_label_create(btn);
    lv_label_set_text(label, "Click me");
    lv_obj_center(label);
    
    // 添加点击事件
    lv_obj_add_event_cb(btn, my_btn_event_cb, LV_EVENT_CLICKED, NULL);
}

void my_btn_event_cb(lv_event_t * e)
{
    lv_obj_t * target = lv_event_get_target(e);
    printf("按钮被点击\n");
}
```

## 五、传递自定义数据

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

## 六、移出事件回调

```c
lv_obj_remove_event_cb(obj, event_cb);
lv_obj_remove_event_dsc(obj, event_dsc);
```

## 七、事件冒泡机制

LVGL支持事件冒泡，事件从子对象向上传递到父对象，直到被处理或到达顶层。

## 八、关键要点

| 要点                         | 说明                          |
| ---------------------------- | ----------------------------- |
| 一个对象可注册多个事件       | 不同类型的事件可绑定不同回调  |
| 一个回调可处理多种事件       | 通过lv_event_get_code判断类型 |
| 事件回调中可调用任何LVGL API | 但避免耗时操作                |
| 用户数据需要自行管理生命周期 | 动态分配的数据要记得释放      |

## 九、常见事件处理模式

```c
void common_event_cb(lv_event_t * e)
{
    lv_obj_t * obj = lv_event_get_target(e);
    lv_event_code_t code = lv_event_get_code(e);
    
    switch(code) {
        case LV_EVENT_CLICKED:
            // 处理点击
            break;
        case LV_EVENT_VALUE_CHANGED:
            // 处理值变化
            break;
        case LV_EVENT_LONG_PRESSED:
            // 处理长按
            break;
        default:
            break;
    }
}
```

事件是LVGL交互编程的基础，掌握事件机制后才能构建响应用户操作的各种交互界面。