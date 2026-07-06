# QFrame 类成员函数介绍

| 函数                                                         | 介绍                                              | 案例                                                 |
| ------------------------------------------------------------ | ------------------------------------------------- | ---------------------------------------------------- |
| QFrame(QWidget *parent = nullptr, Qt::WindowFlags f = Qt::WindowFlags()) | QFrame 类的构造函数，创建一个框架部件             | QFrame *frame = new QFrame(parentWidget);            |
| int frameStyle() const                                       | 获取当前框架的样式，包括形状和阴影的组合值        | int style = frame->frameStyle();                     |
| void setFrameStyle(int style)                                | 设置框架的样式，使用 Shape 和 Shadow 枚举值的组合 | frame->setFrameStyle(QFrame::Box \| QFrame::Raised); |
| QFrame::Shape frameShape() const                             | 获取框架的形状类型                                | QFrame::Shape shape = frame->frameShape();           |
| void setFrameShape(QFrame::Shape shape)                      | 设置框架的形状类型                                | frame->setFrameShape(QFrame::Panel);                 |
| QFrame::Shadow frameShadow() const                           | 获取框架的阴影效果                                | QFrame::Shadow shadow = frame->frameShadow();        |
| void setFrameShadow(QFrame::Shadow shadow)                   | 设置框架的阴影效果                                | frame->setFrameShadow(QFrame::Sunken);               |
| int lineWidth() const                                        | 获取框架的线宽                                    | int width = frame->lineWidth();                      |
| void setLineWidth(int width)                                 | 设置框架的线宽                                    | frame->setLineWidth(3);                              |
| int midLineWidth() const                                     | 获取框架中间线的宽度                              | int midWidth = frame->midLineWidth();                |
| void setMidLineWidth(int width)                              | 设置框架中间线的宽度                              | frame->setMidLineWidth(2);                           |
| int frameWidth() const                                       | 获取框架的总宽度（包括线宽和中间线宽）            | int totalWidth = frame->frameWidth();                |
| QRect frameRect() const                                      | 获取框架的矩形区域                                | QRect rect = frame->frameRect();                     |
| void setFrameRect(const QRect &rect)                         | 设置框架的矩形区域                                | frame->setFrameRect(QRect(10, 10, 100, 50));         |
| QSize sizeHint() const                                       | 获取框架的建议大小                                | QSize size = frame->sizeHint();                      |

# QFrame 类枚举值介绍

| 枚举类型           | 值     | 介绍                                   | 案例                                       |
| ------------------ | ------ | -------------------------------------- | ------------------------------------------ |
| Shape::NoFrame     | 0      | 无框架，不绘制任何边框                 | frame->setFrameShape(QFrame::NoFrame);     |
| Shape::Box         | 0x0001 | 矩形框，绘制一个矩形边框               | frame->setFrameShape(QFrame::Box);         |
| Shape::Panel       | 0x0002 | 面板，绘制一个面板样式的边框           | frame->setFrameShape(QFrame::Panel);       |
| Shape::WinPanel    | 0x0003 | Windows风格面板，绘制Windows风格的面板 | frame->setFrameShape(QFrame::WinPanel);    |
| Shape::HLine       | 0x0004 | 水平线，绘制一条水平分隔线             | frame->setFrameShape(QFrame::HLine);       |
| Shape::VLine       | 0x0005 | 垂直线，绘制一条垂直分隔线             | frame->setFrameShape(QFrame::VLine);       |
| Shape::StyledPanel | 0x0006 | 样式化面板，根据当前样式绘制面板       | frame->setFrameShape(QFrame::StyledPanel); |
| Shadow::Plain      | 0x0010 | 平面效果，无3D阴影效果                 | frame->setFrameShadow(QFrame::Plain);      |
| Shadow::Raised     | 0x0020 | 凸起效果，框架呈现凸起的3D效果         | frame->setFrameShadow(QFrame::Raised);     |
| Shadow::Sunken     | 0x0030 | 凹陷效果，框架呈现凹陷的3D效果         | frame->setFrameShadow(QFrame::Sunken);     |

# 综合使用示例

| 场景                 | 代码示例                                                     | 效果描述                                       |
| -------------------- | ------------------------------------------------------------ | ---------------------------------------------- |
| 创建带凸起边框的面板 | QFrame *frame = new QFrame();<br>frame->setFrameStyle(QFrame::Panel \| QFrame::Raised);<br>frame->setLineWidth(2); | 创建一个有凸起效果的矩形面板                   |
| 创建水平分隔线       | QFrame *line = new QFrame();<br>line->setFrameShape(QFrame::HLine);<br>line->setFrameShadow(QFrame::Sunken);<br>line->setFixedHeight(5); | 创建一条凹陷效果的水平分隔线                   |
| 创建容器框架         | QFrame *container = new QFrame();<br>container->setFrameStyle(QFrame::Box \| QFrame::Plain);<br>container->setLineWidth(1);<br>QVBoxLayout *layout = new QVBoxLayout(container);<br>layout->addWidget(new QLabel("内容")); | 创建一个带边框的容器，内部可以放置其他控件     |
| 创建复杂框架         | QFrame *complexFrame = new QFrame();<br>complexFrame->setFrameShape(QFrame::Box);<br>complexFrame->setFrameShadow(QFrame::Raised);<br>complexFrame->setLineWidth(3);<br>complexFrame->setMidLineWidth(2); | 创建一个有复杂边框效果的框架，包含主线和中间线 |

# 注意事项

1. QFrame 的样式可以通过 Qt 样式表进一步自定义，提供更丰富的视觉效果
2. 框架的最终外观可能受当前应用程序样式(QStyle)的影响
3. 对于特殊需求，可以继承 QFrame 并重写 paintEvent 方法实现完全自定义的绘制
4. QFrame 是许多其他 Qt 控件的基础，了解其功能有助于更好地使用这些派生控件