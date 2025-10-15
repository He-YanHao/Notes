在大多数情况下，LVGL 小部件的 API 函数结构如下：

-   `lv_ + <widget_name> + create(parent)`
-   `lv_ + <widget_name> + set + <property>(widget, value)`
-   `lv_ + <widget_name> + get + <property>(widget)`
-   `lv_ + <widget_name> + add + <property>(widget)`

## 基本属性

所有小部件类型共享一些基本属性：

-   位置
-   大小
-   父母
-   风格
-   事件回传
-   *Clickable*、*Scrollable* 等标志。
-   等。

您可以使用 和 函数设置/获取这些属性。例如：`lv_obj_set_...``lv_obj_get_...`

```
/* Set basic widget attributes */
lv_obj_set_size(btn1, 100, 50);   /* Set a button's size */
lv_obj_set_pos(btn1, 20, 30);     /* Set a button's position */
```



有关位置、大小、坐标和布局的完整详细信息，请参阅[位置和大小](https://docs.lvgl.io/master/details/common-widget-features/coordinates.html#coord)。

## 小组件特定属性

小部件类型也具有特殊属性。例如，滑块具有：

-   最小值和最大值
-   当前价值

对于这些特殊属性，每个小部件类型可能具有唯一的 API 功能。为 例如，对于 [Slider](https://docs.lvgl.io/master/details/widgets/slider.html#lv-slider)：

```
/* Set slider-specific attributes */
lv_slider_set_range(slider1, 0, 100);               /* Set the min and max values */
lv_slider_set_value(slider1, 40, LV_ANIM_ON);       /* Set the current value (position) */
```



小部件的 API 在其[文档中](https://docs.lvgl.io/master/details/widgets/index.html#widgets)进行了描述，但您 还可以查阅每个 Widget 各自的头文件（例如 *widgets/lv_slider.h*）以 查找函数原型的快速参考以及有关每个原型的简短文档。

## 控件创建

可以在运行时动态创建和删除小部件。仅当前创建 （现有）小部件消耗 RAM。

这允许您仅在单击按钮打开屏幕时创建屏幕，并且 删除加载新屏幕时的屏幕。

可以根据设备的当前环境创建 UI。例如，您 可以根据当前连接的传感器创建仪表、图表、条形图和滑块。

每个小部件都有自己的**创建**函数，原型如下：

```
lv_obj_t * lv_<widget>_create(lv_obj_t * parent)
```



create 函数只有一个参数，用于指定在哪个小部件上 创建新的小组件。`parent`

返回值是指向 类型为 的已创建小部件的指针。`lv_obj_t *`

## 控件删除

所有小部件类型都有一个通用**的删除**功能。它删除小部件和 它的所有子孙。

```
void lv_obj_delete(lv_obj_t * widget);
```



立即[``](https://docs.lvgl.io/master/API/core/lv_obj_tree_h.html#_CPPv413lv_obj_deleteP8lv_obj_t)删除小组件。如果出于任何原因您不能 立即删除小部件，您可以使用 [lv_obj_delete_async](https://docs.lvgl.io/master/API/core/lv_obj_tree_h.html#_CPPv419lv_obj_delete_asyncP8lv_obj_t)（小部件），它将在下一次调用时[``](https://docs.lvgl.io/master/API/misc/lv_timer_h.html#_CPPv416lv_timer_handlerv)执行删除。这 很有用，例如，如果要删除子处理[``](https://docs.lvgl.io/master/API/misc/lv_event_h.html#_CPPv4N15lv_event_code_t15LV_EVENT_DELETEE)程序中小部件的父级。删除后，小部件占用的 RAM 为 释放。

您可以使用 [lv_obj_clean](https://docs.lvgl.io/master/API/core/lv_obj_tree_h.html#_CPPv412lv_obj_cleanP8lv_obj_t)（widget） 删除控件的所有子项（但不能删除控件本身）。

您可以使用 [lv_obj_delete_delayed](https://docs.lvgl.io/master/API/core/lv_obj_tree_h.html#_CPPv421lv_obj_delete_delayedP8lv_obj_t8uint32_t)（widget， 1000） 删除 一段时间。延迟以毫秒表示。

通过调用 [lv_obj_null_on_delete](https://docs.lvgl.io/master/API/core/lv_obj_h.html#_CPPv421lv_obj_null_on_deletePP8lv_obj_t)（&widget），变量 删除小部件时，小部件将设置为 NULL。这使得检查变得容易 小部件是否存在。`lv_obj_t *`

下面是一个使用上述一些函数的示例：

```
static lv_obj_t * my_label; /* Static in the file so it stays valid */

/* Call it every 2000 ms */
void some_timer_callback(lv_timer_t * t)
{
   /* If the label is not created yet, create it and also delete it after 1000 ms */
   if(my_label == NULL) {
      my_label = lv_label_create(lv_screen_active());
      lv_obj_delete_delayed(my_label, 1000);
      lv_obj_null_on_delete(&my_label);
   }
   /* Move the label if it exists */
   else {
      lv_obj_set_x(my_label, lv_obj_get_x(my_label) + 1);
   }
}
```