# 从lv_img_set_src开始

```c
/*=====================
 * Setter functions
 *====================*/

void lv_img_set_src(lv_obj_t * obj, const void * src)
{
    // 检查参数合法性
    LV_ASSERT_OBJ(obj, MY_CLASS);

    // 检查对象参数 标记该范围内需要重绘
    lv_obj_invalidate(obj);

	// 判断参数类型
    // 可以是：字符串路径 lv_img_dsc_t结构体的指针 文本符号 未知
    lv_img_src_t src_type = lv_img_src_get_type(src);
    lv_img_t * img = (lv_img_t *)obj;

#if LV_USE_LOG && LV_LOG_LEVEL >= LV_LOG_LEVEL_INFO
    switch(src_type) {
        // 图片来源于需要驱动层获取的外部二进制文件
        case LV_IMG_SRC_FILE:
            LV_LOG_TRACE("lv_img_set_src: `LV_IMG_SRC_FILE` type found");
            break;
        // 图片来源于已经被编入固件的二进制固件或C语言数组
        case LV_IMG_SRC_VARIABLE:
            LV_LOG_TRACE("lv_img_set_src: `LV_IMG_SRC_VARIABLE` type found");
            break;
        // 图片来源是 Symbol 字符串
        case LV_IMG_SRC_SYMBOL:
            LV_LOG_TRACE("lv_img_set_src: `LV_IMG_SRC_SYMBOL` type found");
            break;
        // 未知错误
        default:
            LV_LOG_WARN("lv_img_set_src: unknown type");
    }
#endif

    /*If the new source type is unknown free the memories of the old source*/
    // 图片源无法被解析的时候 释放内存
    if(src_type == LV_IMG_SRC_UNKNOWN) {
        LV_LOG_WARN("lv_img_set_src: unknown image type");
        if(img->src_type == LV_IMG_SRC_SYMBOL || img->src_type == LV_IMG_SRC_FILE) {
            lv_mem_free((void *)img->src);
        }
        img->src      = NULL;
        img->src_type = LV_IMG_SRC_UNKNOWN;
        return;
    }

    // 获取图片信息 保存在header里
    lv_img_header_t header;
    lv_img_decoder_get_info(src, &header);

    /*Save the source*/
    // 假设是图片资源是编译在固件内的类型
    if(src_type == LV_IMG_SRC_VARIABLE) {
        /*If memory was allocated because of the previous `src_type` then free it*/
        // 文件路径字符串是从堆上分配的 LVGL内置字体图标的名称字符串也是堆上分配的
        // 所以要先释放掉
        if(img->src_type == LV_IMG_SRC_FILE || img->src_type == LV_IMG_SRC_SYMBOL) {
            lv_mem_free((void *)img->src);
        }
        img->src = src;
    }
    // 假设类型是文件路径字符串 或 LVGL内置字体图标 则进行这样处理
    else if(src_type == LV_IMG_SRC_FILE || src_type == LV_IMG_SRC_SYMBOL) {
        /*If the new and the old src are the same then it was only a refresh.*/
        // 仅仅在与上一次不一样的时候才会进行刷新
        if(img->src != src) {
            const void * old_src = NULL;
            /*If memory was allocated because of the previous `src_type` then save its pointer and free after allocation.
             *It's important to allocate first to be sure the new data will be on a new address.
             *Else `img_cache` wouldn't see the change in source.*/
            if(img->src_type == LV_IMG_SRC_FILE || img->src_type == LV_IMG_SRC_SYMBOL) {
                old_src = img->src;
            }
            char * new_str = lv_mem_alloc(strlen(src) + 1);
            // 断言 出现错误则死循环
            LV_ASSERT_MALLOC(new_str);
            if(new_str == NULL) return;
            strcpy(new_str, src);
            img->src = new_str;

            if(old_src) lv_mem_free((void *)old_src);
        }
    }

    // 编译在固件内的类型 文件路径 可以从文件中获取图片大小 但是在LVGL内置字体图标的时候 无法获得大小 需要手动赋值
    if(src_type == LV_IMG_SRC_SYMBOL) {
        /*`lv_img_dsc_get_info` couldn't set the width and height of a font so set it here*/
        // 手动计算大小并赋值
        const lv_font_t * font = lv_obj_get_style_text_font(obj, LV_PART_MAIN);
        lv_coord_t letter_space = lv_obj_get_style_text_letter_space(obj, LV_PART_MAIN);
        lv_coord_t line_space = lv_obj_get_style_text_line_space(obj, LV_PART_MAIN);
        lv_point_t size;
        lv_txt_get_size(&size, src, font, letter_space, line_space, LV_COORD_MAX, LV_TEXT_FLAG_NONE);
        header.w = size.x;
        header.h = size.y;
    }

    // 更新大小和各种参数
    img->src_type = src_type;
    img->w        = header.w;
    img->h        = header.h;
    img->cf       = header.cf;
    img->pivot.x = header.w / 2;
    img->pivot.y = header.h / 2;

    // 刷新大小 影响父容器布局
    lv_obj_refresh_self_size(obj);

    /*Provide enough room for the rotated corners*/
	// 处理旋转/缩放 并触发屏幕绘制
    if(img->angle || img->zoom != LV_IMG_ZOOM_NONE) lv_obj_refresh_ext_draw_size(obj);

    // 标记脏区域
    lv_obj_invalidate(obj);
}
```

