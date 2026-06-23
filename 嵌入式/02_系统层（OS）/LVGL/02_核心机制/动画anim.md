# 动画anim

## 动画的本质

LVGL的动画可以理解为在一段时间内连续修改某个属性的值，高频调用普通的 set_xxx 行为。

核心结构体为 lv_anim_t，它是一个定时器、插值器和回调函数的组合体。

动画本质上只干三件事：
1. 时间推进（基于 lv_tick 时钟滴答）
2. 计算当前值（通过插值算法）
3. 把值写回对象（调用 exec_cb 回调函数）

## 动画能改什么

LVGL没有固定的动画属性列表。**只要你能写一个函数去修改它，它就能被动画驱动**。

官方常用的可动画属性包括：

| 类型   | 示例             |
| ------ | ---------------- |
| 位置   | x、y             |
| 大小   | width、height    |
| 透明度 | opa              |
| 角度   | rotation         |
| 缩放   | zoom             |
| 样式值 | bg_opa、bg_color |
| 自定义 | 用户自定义变量   |

## 核心API函数

### 动画创建与初始化

lv_anim_init 初始化动画结构体
lv_anim_set_var 绑定动画与目标对象
lv_anim_set_exec_cb 设置执行回调函数
lv_anim_start 启动动画

### 数值与时间控制

lv_anim_set_values 设置起始值和结束值
lv_anim_set_time 设置动画持续时间（毫秒）
lv_anim_set_delay 设置延迟启动时间（毫秒）
lv_anim_set_playback_time 设置反向播放时间，0表示不反向

### 重复与路径控制

lv_anim_set_repeat_count 设置重复次数，LV_ANIM_REPEAT_INFINITE表示无限重复
lv_anim_set_repeat_delay 设置重复间隔时间
lv_anim_set_path_cb 设置缓动函数路径，可选线性、缓入、缓出等

### 辅助函数

lv_anim_get_curr_value 获取动画当前值
lv_anim_delete 删除指定对象的动画

## 回调函数格式

动画回调函数必须遵循以下签名格式：
void anim_cb(void var, int32_t value)

参数说明：
var 为通用指针，指向被动画的对象
value 为当前帧的插值结果，范围在起始值和结束值之间

在回调函数内部，需要将 var 强制转换为正确的对象类型，然后将 value 应用到相应的属性上。

## 回调适配器模式

当目标属性的类型与动画回调的 value 类型不匹配时，可以使用适配函数进行转换。例如：

static void image_set_scale_anim_cb(void * obj, int32_t scale)
{
    lv_image_set_scale((lv_obj_t *)obj, (uint16_t)scale);
}

这种模式用于解决LVGL动画API的参数类型匹配问题，避免编译警告。

## 工作机制

LVGL动画引擎内部工作原理：

设置起始值 start、结束值 end 和持续时间 time 后，引擎自动计算每毫秒的变化量。每次调用 lv_timer_handler 时，引擎根据当前经过时间计算对应的 value 值，然后调用回调函数。开发者不需要关心调用频率，引擎会自动根据实际经过时间计算正确值，即使延迟调用也不会跳跃，动画仍会在正确的时间点完成。

## 实际应用示例

以下代码实现按钮点击后水平移动的动画效果：

```c
static bool is_expanded = false;
static lv_anim_t anim_current;

static void anim_cb(void * var, int32_t value)
{
    lv_obj_t * obj = (lv_obj_t *)var;
    lv_obj_set_x(obj, value);
}

void action_button(lv_event_t *e)
{
    int32_t start_val = 0;
    int32_t end_val = 800;
    lv_obj_t * btn = lv_event_get_target(e);

    lv_anim_init(&anim_current);
    lv_anim_set_var(&anim_current, btn);
    lv_anim_set_exec_cb(&anim_current, anim_cb);
    lv_anim_set_time(&anim_current, 200);
    lv_anim_set_playback_time(&anim_current, 0);
    
    if(!is_expanded) {
        lv_anim_set_values(&anim_current, start_val, end_val);
        is_expanded = true;
    } else {
        lv_anim_set_values(&anim_current, end_val, start_val);
        is_expanded = false;
    }
    
    lv_anim_start(&anim_current);
}
```

## 动画与控件的关系

动画不属于控件，也不属于图层吸附机制，而是独立的系统级机制。它可以作用于任何控件的任何可数值化属性，包括坐标、宽高、透明度、缩放比例、颜色等。这与定时器、事件、样式等机制处于同一层级，都是LVGL的内置功能模块。
