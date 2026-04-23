# EEZ生成代码解析

## 代码作用分类

| 文件组         | 文件                      | 核心作用                                                     | 当前状态                       |
| :------------- | :------------------------ | :----------------------------------------------------------- | :----------------------------- |
| **入口与调度** | `ui.c` / `ui.h`           | UI 初始化入口，负责创建所有屏幕、加载初始屏幕、驱动每个屏幕的 `tick` 刷新。 | **完整可用**                   |
| **界面定义**   | `screens.c` / `screens.h` | 13 个屏幕的具体创建代码（控件布局），以及每个屏幕对应的定时刷新函数。 | **主菜单已布局，其余为空壳**   |
| **资源管理**   | `fonts.c` / `fonts.h`     | 将 LVGL 内置字体包装成可索引的数组，供 EEZ Studio 设计时选用。 | **已集成蒙特塞拉特字体族**     |
|                | `images.c` / `images.h`   | 管理外部图片资源的描述符数组（当前为空）。                   | **待添加图片资源**             |
|                | `styles.c` / `styles.h`   | 用于存放自定义样式的代码（当前为空）。                       | **待扩展**                     |
| **事件与数据** | `actions.h`               | 声明用户交互事件的回调函数（当前为空）。                     | **你需要在此添加按钮点击事件** |
|                | `vars.h`                  | 声明 UI 中使用的全局变量、枚举等（当前为空）。               | **你需要在此定义共享数据**     |
|                | `structs.h`               | 声明自定义结构体类型（当前为空）。                           | **待扩展**                     |

## 调用关系

1. 初始化：`ui_init()` → `create_screens()` → 依次调用各`create_screen_xxx()`，并设置默认LVGL主题。
2. 屏幕加载：`ui_init()` → `loadScreen(SCREEN_ID_MAIN)` → 通过`objects`全局结构获取屏幕对象 → `lv_scr_load_anim()`。
3. 周期性刷新：`ui_tick()` → `tick_screen(currentScreen)` → 通过函数指针数组调用对应屏幕的`tick_screen_xxx()`。

当前代码为EEZ Studio生成的LVGL UI框架基础结构，大部分屏幕为空白模板，需填充具体控件和逻辑。



1. **初始化阶段 (`ui_init`)**
   - 调用 `create_screens()`：依次执行 13 个 `create_screen_xxx()` 函数。
   - 每个 `create_screen_xxx()` 都调用 `lv_obj_create(0)` 创建一个新的屏幕对象，并存入全局结构体 `objects` 中（例如 `objects.main`、`objects.clock`）。
   - **注意**：此时所有 13 个屏幕都已经在内存中创建完毕，但只有当前活动的屏幕会被 LVGL 渲染。

2. **屏幕加载 (`loadScreen`)**
   - `ui_init()` 最后调用 `loadScreen(SCREEN_ID_MAIN)`。
   - `loadScreen` 根据传入的 `ScreensEnum` 枚举值计算出 `currentScreen` 索引，然后通过 `getLvglObjectFromIndex` 从 `objects` 结构体中取出对应的屏幕指针。
   - 调用 `lv_scr_load_anim()` 以淡入动画切换到目标屏幕。

3. **周期性刷新 (`ui_tick`)**
   - 你的主循环（例如 `loop()` 或 FreeRTOS 任务）需要**高频调用** `ui_tick()`（例如每 5-10ms 一次）。
   - `ui_tick()` 内部通过 `tick_screen(currentScreen)` 调用当前屏幕对应的 `tick_screen_xxx()` 函数。
   - **关键设计**：只有当前活动屏幕的 `tick` 函数会被执行，这能有效节省 CPU，避免更新不可见界面。

4. **屏幕切换触发**
   - 当用户点击主菜单上的按钮时，你需要**在对应的事件回调函数中**调用 `loadScreen(目标屏幕ID)`。
   - 例如点击 "Clock" 按钮 → 触发你写的回调 → 执行 `loadScreen(SCREEN_ID_CLOCK)` → 切换到时钟界面。

---

### 🛠️ 你需要重点扩展的文件

EEZ Studio 生成的代码是一个**干净的骨架**，你的核心工作是在预留的扩展点中填充业务逻辑。

| 扩展目标             | 对应文件                  | 具体操作                                                     |
| :------------------- | :------------------------ | :----------------------------------------------------------- |
| **添加按钮点击事件** | `actions.h` + `screens.c` | 1. 在 `actions.h` 中声明函数，如 `void on_clock_btn_clicked(lv_event_t *e);`。<br>2. 在 `screens.c` 的 `create_screen_main()` 中，为每个按钮注册事件回调：`lv_obj_add_event_cb(btn, on_clock_btn_clicked, LV_EVENT_CLICKED, NULL);`。 |
| **实现屏幕内容**     | `screens.c`               | 在每个 `create_screen_xxx()` 的空函数体内，像主菜单一样添加控件（标签、图表、滑块等）。 |
| **实现动态刷新**     | `screens.c`               | 在对应的 `tick_screen_xxx()` 中编写刷新逻辑，例如更新时间显示、更新天气数据、更新系统监控图表。 |
| **管理全局数据**     | `vars.h` + `ui.c`         | 在 `vars.h` 中声明全局变量（如 `extern char current_time_str[16];`），在某个 `.c` 文件中定义，供各屏幕的 `tick` 函数使用。 |
| **添加自定义样式**   | `styles.c`                | 定义 `lv_style_t` 变量并初始化，然后在 `create_screens()` 或各屏幕创建函数中应用。 |

---

### 💡 当前代码的两个小细节

1. **`fonts.c` 中的字体数组**  
   它通过 `#if LV_FONT_MONTSERRAT_XX` 条件编译，确保只有在 `lv_conf.h` 中启用了对应字体，才会被加入数组。这是 EEZ Studio 兼容不同 LVGL 配置的标准做法。

2. **`objects_t` 结构体与索引技巧**  
   `ui.c` 中的 `getLvglObjectFromIndex` 利用了一个巧妙的事实：`objects` 结构体的内存布局与数组一致，因此可以通过 `(lv_obj_t **)&objects` 强转后按索引访问。这使得屏幕切换代码非常简洁。

---

### 📝 总结

这套文件为你搭建了一个**结构清晰、易于扩展的 UI 基础框架**。你现在只需要：

1. 在 `actions.h` / `screens.c` 中**绑定按钮事件**，实现屏幕跳转。
2. 逐个填充 13 个屏幕的 `create` 和 `tick` 函数。
3. 在 `vars.h` 中管理跨屏幕的共享数据。
4. 确保主循环高频调用 `ui_tick()`。

接下来你可以从实现“时钟界面”的显示刷新开始，逐步完善整个项目。如果需要某个具体屏幕的实现思路，随时可以继续深入讨论。