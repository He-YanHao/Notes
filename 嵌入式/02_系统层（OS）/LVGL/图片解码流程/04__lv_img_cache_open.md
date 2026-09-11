# _lv_img_cache_open

```c
/**
 * Open an image using the image decoder interface and cache it.
 * The image will be left open meaning if the image decoder open callback allocated memory then it will remain.
 * The image is closed if a new image is opened and the new image takes its place in the cache.
 * @param src source of the image. Path to file or pointer to an `lv_img_dsc_t` variable
 * @param color color The color of the image with `LV_IMG_CF_ALPHA_...`
 * @return pointer to the cache entry or NULL if can open the image
 */
_lv_img_cache_entry_t * _lv_img_cache_open(const void * src, lv_color_t color, int32_t frame_id)
{
    /*Is the image cached?*/
    _lv_img_cache_entry_t * cached_src = NULL;

#if LV_IMG_CACHE_DEF_SIZE
    if(entry_cnt == 0) {
        LV_LOG_WARN("lv_img_cache_open: the cache size is 0");
        return NULL;
    }

    _lv_img_cache_entry_t * cache = LV_GC_ROOT(_lv_img_cache_array);

    /*Decrement all lifes. Make the entries older*/
    uint16_t i;
    for(i = 0; i < entry_cnt; i++) {
        if(cache[i].life > INT32_MIN + LV_IMG_CACHE_AGING) {
            cache[i].life -= LV_IMG_CACHE_AGING;
        }
    }

    for(i = 0; i < entry_cnt; i++) {
        if(color.full == cache[i].dec_dsc.color.full &&
           frame_id == cache[i].dec_dsc.frame_id &&
           lv_img_cache_match(src, cache[i].dec_dsc.src)) {
            /*If opened increment its life.
             *Image difficult to open should live longer to keep avoid frequent their recaching.
             *Therefore increase `life` with `time_to_open`*/
            cached_src = &cache[i];
            cached_src->life += cached_src->dec_dsc.time_to_open * LV_IMG_CACHE_LIFE_GAIN;
            if(cached_src->life > LV_IMG_CACHE_LIFE_LIMIT) cached_src->life = LV_IMG_CACHE_LIFE_LIMIT;
            LV_LOG_TRACE("image source found in the cache");
            break;
        }
    }

    /*The image is not cached then cache it now*/
    if(cached_src) return cached_src;

    /*Find an entry to reuse. Select the entry with the least life*/
    cached_src = &cache[0];
    for(i = 1; i < entry_cnt; i++) {
        if(cache[i].life < cached_src->life) {
            cached_src = &cache[i];
        }
    }

    /*Close the decoder to reuse if it was opened (has a valid source)*/
    if(cached_src->dec_dsc.src) {
        lv_img_decoder_close(&cached_src->dec_dsc);
        LV_LOG_INFO("image draw: cache miss, close and reuse an entry");
    }
    else {
        LV_LOG_INFO("image draw: cache miss, cached to an empty entry");
    }
#else
    cached_src = &LV_GC_ROOT(_lv_img_cache_single);
#endif
    /*Open the image and measure the time to open*/
    uint32_t t_start  = lv_tick_get();
    lv_res_t open_res = lv_img_decoder_open(&cached_src->dec_dsc, src, color, frame_id);
    if(open_res == LV_RES_INV) {
        LV_LOG_WARN("Image draw cannot open the image resource");
        lv_memset_00(cached_src, sizeof(_lv_img_cache_entry_t));
        cached_src->life = INT32_MIN; /*Make the empty entry very "weak" to force its us*/
        return NULL;
    }

    cached_src->life = 0;

    /*If `time_to_open` was not set in the open function set it here*/
    if(cached_src->dec_dsc.time_to_open == 0) {
        cached_src->dec_dsc.time_to_open = lv_tick_elaps(t_start);
    }

    if(cached_src->dec_dsc.time_to_open == 0) cached_src->dec_dsc.time_to_open = 1;

    return cached_src;
}
```

