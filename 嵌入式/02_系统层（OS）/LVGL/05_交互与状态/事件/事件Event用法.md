# 事件Event用法

事件是LVGL中处理用户交互、状态变更和生命周期回调的核心机制。

当用户触摸、点击、控件状态变化或发生绘制时，LVGL会触发相应事件，开发者通过注册回调函数来响应。

## 事件的基本用法

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

### 传递自定义数据

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

## 移出事件回调

```c
lv_obj_remove_event_cb(obj, event_cb);
lv_obj_remove_event_dsc(obj, event_dsc);
```



## 事件冒泡机制

LVGL支持事件冒泡，事件从子对象向上传递到父对象，直到被处理或到达顶层。



## 完整示例

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



## 常见事件处理模式

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



## 关键要点

| 要点                         | 说明                          |
| ---------------------------- | ----------------------------- |
| 一个对象可注册多个事件       | 不同类型的事件可绑定不同回调  |
| 一个回调可处理多种事件       | 通过lv_event_get_code判断类型 |
| 事件回调中可调用任何LVGL API | 但需避免耗时操作              |
| 用户数据需自行管理生命周期   | 动态分配的内存要记得释放      |

