# UI

```
├── 
├── 
├── assets
│   ├── font
│   │   ├── DroidSansFallback.ttf
│   │   └── montserratMedium.ttf
│   └── image
├── custom
│   ├── assets
│   │   ├── font
│   │   └── image
│   ├── custom.c
│   └── custom.h
```

- aic_lang.c / aic_lang.h ：多语言管理的头文件
- font_list.c / font_list.h ：字体链表管理头文件
- lv_conf_custom.h：LVGL 配置覆盖文件，会不修改原有的 `lv_conf.h`，但是覆盖其作用。
- screen.c：
- ui_init.c / ui_init.h：UI 系统初始化
- ui_objects.h：定义所有 UI 对象结构体及外部管理结构
- ui_util.c / ui_util.h：界面辅助工具函数



## 13. `custom/custom.h`
**作用：用户自定义代码头文件**  
- 包含了 UI 相关头文件。
- 声明 `custom_init()` 函数。

---

## 14. `custom/custom.c`
**作用：用户自定义代码实现（占位）**  
- 里面是空函数 `custom_init()`，留给用户添加自己的初始化逻辑。

---

## 15. `assets/font/` 目录
- `montserratMedium.ttf`：英文字体文件。
- `DroidSansFallback.ttf`：中文字体文件（通常用于 fallback，支持 CJK）。

这些文件在 `aic_lang.c` 中通过 `font_names[]` 引用，在运行时从存储路径加载。

---

## 16. `assets/image/` 目录（空）
**作用：预留存放图片资源的目录**  
UIBuilder 可导入图片生成 C 数组或保持为文件。

---

## 整体执行流程总结

1. **系统启动**  
   → 调用 `ui_init()`  
   → 初始化字体链表  
   → 初始化 UI 管理器  
   → 创建 `tate` 屏幕并加载  
   → 执行 `custom_init()`

2. **多语言字体加载**  
   - 当界面需要字体时，调用 `aic_get_multi_lang_font(size)`  
   - 自动根据当前语言选择对应的字体 ID  
   - 先在字体链表中查找，若未加载则通过 `ui_font_init()` 加载 TTF 文件并缓存。

3. **语言切换**  
   - 外部调用 `xxx_update_language(lang)`  
   - 内部调用 `aic_set_language()` 修改全局语言状态  
   - 调用 `xxx_update_current_language()` 刷新该屏幕所有控件的文字和字体。

4. **翻译机制（目前不完整）**  
   - 提供了 `translations[]` 翻译表和 `aic_get_translation()`  
   - 但实际控件文本设置并未使用该函数，而是硬编码。
