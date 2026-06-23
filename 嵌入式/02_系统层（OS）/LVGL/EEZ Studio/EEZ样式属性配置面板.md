# EEZ Studio 样式属性配置面板

## POSITION AND SIZE（位置与尺寸）

| 属性               | 说明                               |
| ------------------ | ---------------------------------- |
| Align              | 对齐方式（中心、左、右等）         |
| Width              | 控件宽度                           |
| Height             | 控件高度                           |
| Length             | 控件长度（某些控件的特殊尺寸属性） |
| Min width          | 最小宽度（用于自适应布局）         |
| Max width          | 最大宽度                           |
| Min height         | 最小高度                           |
| Max height         | 最大高度                           |
| X                  | 控件相对于父容器的X坐标            |
| Y                  | 控件相对于父容器的Y坐标            |
| Transform width    | 变换后的宽度（用于动画和变形效果） |
| Transform height   | 变换后的高度                       |
| Translate X        | X方向平移偏移量                    |
| Translate Y        | Y方向平移偏移量                    |
| Transform scale X  | X方向缩放比例（256为原始大小）     |
| Transform scale Y  | Y方向缩放比例                      |
| Transform rotation | 旋转角度（以0.1度为单位）          |
| Transform pivot X  | 变换中心点的X坐标                  |
| Transform pivot Y  | 变换中心点的Y坐标                  |
| Transform skew X   | X方向倾斜角度                      |
| Transform skew Y   | Y方向倾斜角度                      |

### Align 子选项

| 类型             | 含义       |
| ---------------- | ---------- |
| DEFAULT          | 默认       |
| TOP_LEFT         | 左上角     |
| TOP_MID          | 顶部居中   |
| TOP_RIGHT        | 右上角     |
| BOTTOM_LEFT      | 左下角     |
| BOTTOM_MID       | 底部居中   |
| BOTTOM_RIGHT     | 右下角     |
| LEFT_MID         | 左中       |
| RIGHT_MID        | 右中       |
| CENTER           | 中心       |
| OUT_TOP_LEFT     | 外部左上方 |
| OUT_TOP_MID      | 外部正上方 |
| OUT_TOP_RIGHT    | 外部右上方 |
| OUT_BOTTOM_LEFT  | 外部左下方 |
| OUT_BOTTOM_MID   | 外部正下方 |
| OUT_BOTTOM_RIGHT | 外部右下方 |
| OUT_LEFT_TOP     | 外部左上侧 |
| OUT_LEFT_MID     | 外部左中侧 |
| OUT_LEFT_BOTTOM  | 外部左下侧 |
| OUT_RIGHT_TOP    | 外部右上侧 |
| OUT_RIGHT_MID    | 外部右中侧 |
| OUT_RIGHT_BOTTOM | 外部右下侧 |

## LAYOUT（布局）

### LAYOUT 概述

| 属性   | 说明                                  |
| ------ | ------------------------------------- |
| Layout | 布局类型（NONE / FLEX / GRID）        |
|        | 关于布局的有点多 分成几个表格一起描述 |

NONE 无布局 不再赘述

### FLEX 概述

#### FLEX 父对象设置参数描述

| 属性             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| Flex flow        | Flex 排列方向（ROW / COLUMN / ROW_WRAP / COLUMN_WRAP）       |
| Flex main place  | 主轴对齐方式（START / CENTER / END / SPACE_BETWEEN / SPACE_AROUND / SPACE_EVENLY） |
| Flex cross place | 交叉轴对齐方式（START / CENTER / END）                       |
| Flex track place | 多行/多列时的对齐方式（START / CENTER / END / SPACE_BETWEEN / SPACE_AROUND / SPACE_EVENLY） |

##### Flex flow 子选项

| 分组          | 选项                  | 方向      | 换行     | 排列顺序                     |
| :------------ | :-------------------- | :-------- | :------- | :--------------------------- |
| **基础**      | `ROW`                 | 水平（→） | ❌ 不换行 | 从左到右                     |
|               | `COLUMN`              | 垂直（↓） | ❌ 不换行 | 从上到下                     |
| **换行**      | `ROW_WRAP`            | 水平（→） | ✅ 换行   | 从左到右，换行后继续从左到右 |
|               | `COLUMN_WRAP`         | 垂直（↓） | ✅ 换列   | 从上到下，换列后继续从上到下 |
| **反向**      | `ROW_REVERSE`         | 水平（←） | ❌ 不换行 | 从右到左                     |
|               | `COLUMN_REVERSE`      | 垂直（↑） | ❌ 不换行 | 从下到上                     |
| **反向+换行** | `ROW_WRAP_REVERSE`    | 水平（←） | ✅ 换行   | 从右到左，换行后继续从右到左 |
|               | `COLUMN_WRAP_REVERSE` | 垂直（↑） | ✅ 换列   | 从下到上，换列后继续从下到上 |

#### FLEX 子对象设置参数描述

| 属性      | 说明                                         |
| --------- | -------------------------------------------- |
| Flex grow | 子控件在主轴方向上的放大权重（0 表示不放大） |

### GRID 概述

GRID 布局需要在父对象和子对象和子对象上分别设置。

#### GRID 父对象设置参数描述

| 属性                   | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| Grid column align      | 网格列对齐方式                                               |
| Grid row align         | 网格行对齐方式                                               |
| Grid column descriptor | 网格列宽度定义，支持像素值（如 50）、FR(x) 比例值（如 FR(1)）或 CONTENT |
| Grid row descriptor    | 网格行高度定义，支持像素值（如 50）、FR(x) 比例值（如 FR(1)）或 CONTENT |

##### Grid xxx align 子选项

| 网格列对齐方式 | 说明                    |
| -------------- | ----------------------- |
| **START**      | 对齐到起始位置（左/上） |
| **CENTER**     | 居中对齐                |
| **END**        | 对齐到结束位置（右/下） |
| **STRETCH**    | 拉伸填满整个空间        |
| **SPACE_BETWEEN** | 两端对齐，元素之间的间距相等，边缘不留白         |
| **SPACE_AROUND**  | 每个元素两侧的间距相等，边缘间距是元素间距的一半 |
| **SPACE_EVENLY**  | 所有间距（包括边缘）完全相等                     |

#### GRID 子对象设置参数描述

| 属性                  | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| Grid cell column pos  | 控件所在网格的起始列索引（从 0 开始）                        |
| Grid cell column span | 控件占据的列数（最小为 1）                                   |
| Grid cell X align     | 控件在网格单元格内的水平对齐方式（START / CENTER / END / STRETCH） |
| Grid cell row pos     | 控件所在网格的起始行索引（从 0 开始）                        |
| Grid cell row span    | 控件占据的行数（最小为 1）                                   |
| Grid cell Y align     | 控件在网格单元格内的垂直对齐方式（START / CENTER / END / STRETCH） |

##### Grid cell X/Y align 子选项

| 网格列对齐方式    | 说明                                             |
| ----------------- | ------------------------------------------------ |
| **START**         | 对齐到起始位置（左/上）                          |
| **CENTER**        | 居中对齐                                         |
| **END**           | 对齐到结束位置（右/下）                          |
| **STRETCH**       | 拉伸填满整个空间                                 |
| **SPACE_BETWEEN** | 两端对齐，元素之间的间距相等，边缘不留白         |
| **SPACE_AROUND**  | 每个元素两侧的间距相等，边缘间距是元素间距的一半 |
| **SPACE_EVENLY**  | 所有间距（包括边缘）完全相等                     |



## PADDING（内边距）

| 属性   | 说明                                 |
| ------ | ------------------------------------ |
| Top    | 内容距离顶部的内边距                 |
| Bottom | 内容距离底部的内边距                 |
| Left   | 内容距离左边的内边距                 |
| Right  | 内容距离右边的内边距                 |
| Radial | 径向内边距（用于圆形控件的边缘间距） |
| Row    | 行间距（Grid/Flex 中）               |
| Column | 列间距（Grid/Flex 中）               |

## MARGIN（外边距）

| 属性   | 说明                     |
| ------ | ------------------------ |
| Top    | 控件距离父容器顶部的间距 |
| Bottom | 控件距离父容器底部的间距 |
| Left   | 控件距离父容器左边的间距 |
| Right  | 控件距离父容器右边的间距 |

## BACKGROUND（背景）

| 属性               | 说明                         |
| ------------------ | ---------------------------- |
| Color              | 背景颜色                     |
| Opacity            | 背景透明度                   |
| Grad. Direction    | 渐变方向（NONE / VER / HOR） |
| Grad. color        | 渐变终点颜色                 |
| Gradient Stop      | 渐变结束位置（0-255）        |
| Main Stop          | 主色结束位置（0-255）        |
| main opa           | 主色透明度                   |
| grad opa           | 渐变色透明度                 |
| Image Source       | 背景图片源                   |
| Image Opacity      | 背景图片透明度               |
| Image Recolor      | 背景图片重新着色             |
| Image Recolor Opa. | 背景图片重新着色透明度       |
| Image Tiled        | 背景图片平铺模式             |

## BORDER（边框）

| 属性    | 说明                             |
| ------- | -------------------------------- |
| Color   | 边框颜色                         |
| Opacity | 边框透明度                       |
| Width   | 边框宽度                         |
| Side    | 显示哪几边边框（上/下/左/右）    |
| Post    | 边框绘制位置（在内容之上或之下） |

## OUTLINE（轮廓）

| 属性    | 说明                       |
| ------- | -------------------------- |
| Width   | 轮廓宽度（绘制在边框外侧） |
| Color   | 轮廓颜色                   |
| Opacity | 轮廓透明度                 |
| Padding | 轮廓与边框的距离           |

## SHADOW（阴影）

| 属性     | 说明         |
| -------- | ------------ |
| Width    | 阴影宽度     |
| X Offset | X方向偏移    |
| Y Offset | Y方向偏移    |
| Spread   | 阴影扩散范围 |
| Color    | 阴影颜色     |
| Opacity  | 阴影透明度   |

## IMAGE（图像）

| 属性         | 说明           |
| ------------ | -------------- |
| Opacity      | 图像透明度     |
| Recolor      | 重新着色       |
| Recolor Opa. | 重新着色透明度 |

## LINE（线条）

| 属性       | 说明                 |
| ---------- | -------------------- |
| Width      | 线条粗细             |
| Dash Width | 虚线中实线部分的长度 |
| Dash Gap   | 虚线中间隙的长度     |
| Rounded    | 圆头端点             |
| Color      | 线条颜色             |
| Opacity    | 线条透明度           |

## ARC（圆弧）

| 属性         | 说明       |
| ------------ | ---------- |
| Width        | 圆弧粗细   |
| Rounded      | 圆头端点   |
| Color        | 圆弧颜色   |
| Opacity      | 圆弧透明度 |
| Image Source | 图像源     |

## TEXT（文本）

| 属性         | 说明                     |
| ------------ | ------------------------ |
| Color        | 文字颜色                 |
| Opacity      | 文字透明度               |
| Font         | 字体                     |
| Letter Space | 字间距                   |
| Line Space   | 行间距                   |
| Decoration   | 装饰（下划线、上划线等） |
| Align        | 文本对齐方式             |

## MISCELLANEOUS（杂项）

| 属性           | 说明                                    |
| -------------- | --------------------------------------- |
| Radius         | 圆角半径                                |
| Radial Offset  | 径向偏移量                              |
| Clip corner    | 是否裁剪超出圆角的角落                  |
| Opacity        | 整体透明度                              |
| Blend mode     | 混合模式                                |
| Base direction | 基础方向（LTR 从左到右 / RTL 从右到左） |
| Anim           | 动画配置字符串                          |
| Anim duration  | 动画持续时间                            |

