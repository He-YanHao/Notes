# decode_and_draw

从缓存（或解码器）拿到图片像素，然后用最高效的方式把它们画到屏幕上。

内部严格分为**“两大路径”**：

- 路径一：“整图绘制”（快速通道）

    - **触发条件**：解码器一次性把整张图片解压完了（`cdsc->dec_dsc.img_data` 不为空）。

    - **应用场景**：小图标、RAW格式图片，或者内存足够大的情况。

    - **处理方式**：
      - 如果图片有旋转或缩放，会先计算变换后的坐标边界（`_lv_img_buf_get_transformed_area`）。
          - 裁剪掉屏幕外不可见的部分（`_lv_area_intersect`）。
          - 然后直接调用 **`lv_draw_img_decoded`**，将整块连续的内存像素一次性提交给底层的绘制接口（可能是DMA2D或CPU渲染），速度极快。

- 路径二：“逐行绘制”（省内存通道）

    - **触发条件**：解码器没有一次性解压整图（`cdsc->dec_dsc.img_data` 为空），通常因为图片太大（如全屏JPG/PNG）。

    - **应用场景**：节省RAM，避免因解码大图导致内存溢出。

    - **处理方式**：
      - 计算需要绘制的区域范围（`mask_com`）。
          - 进入 `for` 循环，**一行一行地**处理：
              - 调用 **`lv_img_decoder_read_line`**，让解码器只解压当前这一行的像素，存入临时缓冲区 `buf`。
              - 立刻调用 `lv_draw_img_decoded` 把这一行画到屏幕上。
              - 循环下一行，直到整个区域绘制完毕。
          - 这就像用“传送带”把像素流水线式地送进显存，内存开销极小。

_lv_img_cache_entry_t * cdsc = _lv_img_cache_open(src, ...);

**这就是解码的实际触发点**。如果缓存中没有这张图，`_lv_img_cache_open` 内部会调用解码器去解压，并把结果挂载到 `cdsc` 结构体上。之后 `decode_and_draw` 只是根据这个结果（是全图还是空）来决定走哪条绘制路径。



```c
static lv_res_t LV_ATTRIBUTE_FAST_MEM decode_and_draw(lv_draw_ctx_t * draw_ctx,
                                                      const lv_draw_img_dsc_t * draw_dsc,
                                                      const lv_area_t * coords, const void * src)
{
    // 如果完全透明 则不进行解码
    if(draw_dsc->opa <= LV_OPA_MIN) return LV_RES_OK;
	// 开始解码
    _lv_img_cache_entry_t * cdsc = _lv_img_cache_open(src, draw_dsc->recolor, draw_dsc->frame_id);

    if(cdsc == NULL) return LV_RES_INV;

    lv_img_cf_t cf;
    if(lv_img_cf_is_chroma_keyed(cdsc->dec_dsc.header.cf)) cf = LV_IMG_CF_TRUE_COLOR_CHROMA_KEYED;
    else if(LV_IMG_CF_ALPHA_8BIT == cdsc->dec_dsc.header.cf) cf = LV_IMG_CF_ALPHA_8BIT;
    else if(LV_IMG_CF_RGB565A8 == cdsc->dec_dsc.header.cf) cf = LV_IMG_CF_RGB565A8;
    else if(lv_img_cf_has_alpha(cdsc->dec_dsc.header.cf)) cf = LV_IMG_CF_TRUE_COLOR_ALPHA;
    else cf = LV_IMG_CF_TRUE_COLOR;

    if(cf == LV_IMG_CF_ALPHA_8BIT) {
        if(draw_dsc->angle || draw_dsc->zoom != LV_IMG_ZOOM_NONE) {
            /* resume normal method */
            cf = LV_IMG_CF_TRUE_COLOR_ALPHA;
            cdsc->dec_dsc.img_data = NULL;
        }
    }

    if(cdsc->dec_dsc.error_msg != NULL) {
        LV_LOG_WARN("Image draw error");

        show_error(draw_ctx, coords, cdsc->dec_dsc.error_msg);
    }
    /*The decoder could open the image and gave the entire uncompressed image.
     *Just draw it!*/
    else if(cdsc->dec_dsc.img_data) {
        lv_area_t map_area_rot;
        lv_area_copy(&map_area_rot, coords);
        if(draw_dsc->angle || draw_dsc->zoom != LV_IMG_ZOOM_NONE) {
            int32_t w = lv_area_get_width(coords);
            int32_t h = lv_area_get_height(coords);

            _lv_img_buf_get_transformed_area(&map_area_rot, w, h, draw_dsc->angle, draw_dsc->zoom, &draw_dsc->pivot);

            map_area_rot.x1 += coords->x1;
            map_area_rot.y1 += coords->y1;
            map_area_rot.x2 += coords->x1;
            map_area_rot.y2 += coords->y1;
        }

        lv_area_t clip_com; /*Common area of mask and coords*/
        bool union_ok;
        union_ok = _lv_area_intersect(&clip_com, draw_ctx->clip_area, &map_area_rot);
        /*Out of mask. There is nothing to draw so the image is drawn successfully.*/
        if(union_ok == false) {
            draw_cleanup(cdsc);
            return LV_RES_OK;
        }

        const lv_area_t * clip_area_ori = draw_ctx->clip_area;
        draw_ctx->clip_area = &clip_com;
        lv_draw_img_decoded(draw_ctx, draw_dsc, coords, cdsc->dec_dsc.img_data, cf);
        draw_ctx->clip_area = clip_area_ori;
    }
    /*The whole uncompressed image is not available. Try to read it line-by-line*/
    else {
        lv_area_t mask_com; /*Common area of mask and coords*/
        bool union_ok;
        union_ok = _lv_area_intersect(&mask_com, draw_ctx->clip_area, coords);
        /*Out of mask. There is nothing to draw so the image is drawn successfully.*/
        if(union_ok == false) {
            draw_cleanup(cdsc);
            return LV_RES_OK;
        }

        int32_t width = lv_area_get_width(&mask_com);

        uint8_t  * buf = lv_mem_buf_get(lv_area_get_width(&mask_com) *
                                        LV_IMG_PX_SIZE_ALPHA_BYTE);  /*+1 because of the possible alpha byte*/

        const lv_area_t * clip_area_ori = draw_ctx->clip_area;
        lv_area_t line;
        lv_area_copy(&line, &mask_com);
        lv_area_set_height(&line, 1);
        int32_t x = mask_com.x1 - coords->x1;
        int32_t y = mask_com.y1 - coords->y1;
        int32_t row;
        lv_res_t read_res;
        for(row = mask_com.y1; row <= mask_com.y2; row++) {
            lv_area_t mask_line;
            union_ok = _lv_area_intersect(&mask_line, clip_area_ori, &line);
            if(union_ok == false) continue;

            read_res = lv_img_decoder_read_line(&cdsc->dec_dsc, x, y, width, buf);
            if(read_res != LV_RES_OK) {
                lv_img_decoder_close(&cdsc->dec_dsc);
                LV_LOG_WARN("Image draw can't read the line");
                lv_mem_buf_release(buf);
                draw_cleanup(cdsc);
                draw_ctx->clip_area = clip_area_ori;
                return LV_RES_INV;
            }

            draw_ctx->clip_area = &mask_line;
            lv_draw_img_decoded(draw_ctx, draw_dsc, &line, buf, cf);
            line.y1++;
            line.y2++;
            y++;
        }
        draw_ctx->clip_area = clip_area_ori;
        lv_mem_buf_release(buf);
    }

    draw_cleanup(cdsc);
    return LV_RES_OK;
}
```

