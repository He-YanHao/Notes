# EEZ样式属性配置面板

## POSITION AND SIZE（位置与尺寸）

| 属性       | 说明                       |
| ---------- | -------------------------- |
| X          | 控件相对于父容器的X坐标    |
| Y          | 控件相对于父容器的Y坐标    |
| Width      | 控件宽度                   |
| Height     | 控件高度                   |
| Min Width  | 最小宽度（用于自适应布局） |
| Max Width  | 最大宽度                   |
| Min Height | 最小高度                   |
| Max Height | 最大高度                   |
| Align      | 对齐方式（中心、左、右等） |

## LAYOUT（布局）/已修正

| 属性                   | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| Layout                 | 布局类型（NONE / FLEX / GRID）                               |
| Flex flow              | Flex 排列方向（ROW / COLUMN / ROW_WRAP / COLUMN_WRAP）       |
| Flex main place        | 主轴对齐方式（START / CENTER / END / SPACE_BETWEEN / SPACE_AROUND / SPACE_EVENLY） |
| Flex cross place       | 交叉轴对齐方式（START / CENTER / END / STRETCH）             |
| Flex track place       | 多行/多列时的对齐方式（START / CENTER / END / SPACE_BETWEEN / SPACE_AROUND / SPACE_EVENLY） |
| Flex grow              | 子控件在主轴方向上的放大权重（0 表示不放大）                 |
| Grid column align      | 网格列对齐方式（START / CENTER / END / STRETCH）             |
| Grid row align         | 网格行对齐方式（START / CENTER / END / STRETCH）             |
| Grid column descriptor | 网格列宽度定义，支持像素值（如 50）、FR(x) 比例值（如 FR(1)）或 CONTENT |
| Grid row descriptor    | 网格行高度定义，支持像素值（如 50）、FR(x) 比例值（如 FR(1)）或 CONTENT |
| Grid cell column pos   | 控件所在网格的起始列索引（从 0 开始）                        |
| Grid cell column span  | 控件占据的列数（最小为 1）                                   |
| Grid cell X align      | 控件在网格单元格内的水平对齐方式（START / CENTER / END / STRETCH） |
| Grid cell row pos      | 控件所在网格的起始行索引（从 0 开始）                        |
| Grid cell row span     | 控件占据的行数（最小为 1）                                   |
| Grid cell Y align      | 控件在网格单元格内的垂直对齐方式（START / CENTER / END / STRETCH） |

## PADDING（内边距）

| 属性   | 说明                  |
| ------ | --------------------- |
| Top    | 内容距离顶部的内边距  |
| Bottom | 内容距离底部的内边距  |
| Left   | 内容距离左边的内边距  |
| Right  | 内容距离右边的内边距  |
| Row    | 行间距（Grid/Flex中） |
| Column | 列间距（Grid/Flex中） |

## MARGIN（外边距）

| 属性   | 说明                     |
| ------ | ------------------------ |
| Top    | 控件距离父容器顶部的间距 |
| Bottom | 控件距离父容器底部的间距 |
| Left   | 控件距离父容器左边的间距 |
| Right  | 控件距离父容器右边的间距 |

## BACKGROUND（背景）/已修正

| 属性               | 说明                                   |
| ------------------ | -------------------------------------- |
| Color              | 背景颜色                               |
| Opacity            | 背景透明度                             |
| Grad. Direction    | 渐变方向（NONE / VER / HOR）           |
| Grad. color        | 渐变终点颜色                           |
| Gradient Stop      | 渐变结束位置（0-255）                  |
| Main Stop          | 主色结束位置（0-255）                  |
| Dither mode        | 抖动模式（NONE / ORDERED / DIFFUSION） |
| Image Source       | 背景图片源                             |
| Image Opacity      | 背景图片透明度                         |
| Image Recolor      | 背景图片重新着色                       |
| Image Recolor Opa. | 背景图片重新着色透明度                 |
| Image Tiled        | 背景图片平铺模式                       |

## BORDER（边框）

| 属性    | 说明                          |
| ------- | ----------------------------- |
| Width   | 边框宽度                      |
| Color   | 边框颜色                      |
| Opacity | 边框透明度                    |
| Side    | 显示哪几边边框（上/下/左/右） |
| Radius  | 圆角半径                      |

## OUTLINE（轮廓）

| 属性    | 说明                       |
| ------- | -------------------------- |
| Width   | 轮廓宽度（绘制在边框外侧） |
| Color   | 轮廓颜色                   |
| Opacity | 轮廓透明度                 |
| Padding | 轮廓与边框的距离           |
| Radius  | 轮廓圆角                   |

## SHADOW（阴影）

| 属性     | 说明         |
| -------- | ------------ |
| Width    | 阴影宽度     |
| Color    | 阴影颜色     |
| Opacity  | 阴影透明度   |
| Offset X | X方向偏移    |
| Offset Y | Y方向偏移    |
| Spread   | 阴影扩散范围 |

## IMAGE（图像）

| 属性            | 说明           |
| --------------- | -------------- |
| Source          | 图像源文件     |
| Recolor         | 重新着色       |
| Recolor Opacity | 重新着色透明度 |
| Scale           | 缩放比例       |
| Rotation        | 旋转角度       |

## LINE（线条）

| 属性    | 说明     |
| ------- | -------- |
| Color   | 线条颜色 |
| Width   | 线条粗细 |
| Rounded | 圆头端点 |
| Dash    | 虚线配置 |

## ARC（圆弧）

| 属性    | 说明     |
| ------- | -------- |
| Color   | 圆弧颜色 |
| Width   | 圆弧粗细 |
| Rounded | 圆头端点 |
| Image   | 图像     |

## TEXT（文本）

| 属性         | 说明                     |
| ------------ | ------------------------ |
| Color        | 文字颜色                 |
| Font         | 字体                     |
| Opacity      | 文字透明度               |
| Letter Space | 字间距                   |
| Line Space   | 行间距                   |
| Decor        | 装饰（下划线、上划线等） |
| Align        | 文本对齐方式             |

## MISCELLANEOUS（杂项）

| 属性       | 说明                     |
| ---------- | ------------------------ |
| Opacity    | 整体透明度               |
| Blend Mode | 混合模式                 |
| Transform  | 变换（旋转、缩放、平移） |
| Animation  | 过渡动画配置             |
| Cursor     | 鼠标光标样式             |
| Scrollbar  | 滚动条样式               |

## 使用建议

| 分类     | 使用频率 | 说明                  |
| -------- | -------- | --------------------- |
| 位置尺寸 | 很高     | 几乎所有控件都需要    |
| 背景     | 很高     | 设置控件基础外观      |
| 边框     | 高       | 调试、选中状态、分组  |
| 内边距   | 高       | 控制内容位置          |
| 文本     | 中       | 标签、按钮文字        |
| 阴影     | 中       | 提升层次感            |
| 轮廓     | 低       | 焦点指示、呼吸灯      |
| 布局     | 视情况   | 复杂界面使用Flex/Grid |