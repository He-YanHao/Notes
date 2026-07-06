# 布局类

Qt 提供了多种布局类（Layout Classes）来帮助开发者自动管理控件的位置和大小，实现灵活的界面设计。这些布局类可以嵌套组合，构建复杂的用户界面，同时确保在不同屏幕尺寸和分辨率下都能正确显示。

## 核心布局类
Qt 主要提供以下 5 种布局管理器：

| 布局类               | 描述                                     | 适用场景                       |
| -------------------- | ---------------------------------------- | ------------------------------ |
| **`QVBoxLayout`**    | 垂直布局（控件从上到下排列）             | 表单、列表、纵向排列的界面     |
| **`QHBoxLayout`**    | 水平布局（控件从左到右排列）             | 工具栏、按钮组、横向排列的界面 |
| **`QGridLayout`**    | 网格布局（控件按行和列排列，类似表格）   | 计算器、仪表盘、复杂表单       |
| **`QFormLayout`**    | 表单布局（标签和输入控件成对排列）       | 注册表单、设置对话框           |
| **`QStackedLayout`** | 堆叠布局（多个控件重叠，每次只显示一个） | 向导界面、选项卡式内容切换     |

## 成员函数

### 添加和移除项目

| 方法                         | 描述               | 使用示例                        |
| ---------------------------- | ------------------ | ------------------------------- |
| `addWidget(QWidget *widget)` | 向布局中添加控件   | `layout->addWidget(button);`    |
| `addLayout(QLayout *layout)` | 向布局中添加子布局 | `layout->addLayout(subLayout);` |
|                              |                    |                                 |



### 尺寸控制

| 方法 | 描述 | 使用示例 |
| ---- | ---- | -------- |
|      |      |          |
|      |      |          |
|      |      |          |

### 边距控制

| 方法                                             | 描述           | 使用示例                                      |
| ------------------------------------------------ | -------------- | --------------------------------------------- |
| `setContentsMargins(int l, int t, int r, int b)` | 设置布局外边距 | `layout->setContentsMargins(10, 10, 10, 10);` |
| `setSpacing(int spacing)`                        | 设置项目间间距 | `layout->setSpacing(5);`                      |
|                                                  |                |                                               |



### 尺寸相关函数

| 方法 | 描述 | 使用示例 |
| ---- | ---- | -------- |
|      |      |          |
|      |      |          |
|      |      |          |



### 激活和更新布局

| 方法 | 描述 | 使用示例 |
| ---- | ---- | -------- |
|      |      |          |
|      |      |          |
|      |      |          |



### 父级关系管理

| 方法 | 描述 | 使用示例 |
| ---- | ---- | -------- |
|      |      |          |
|      |      |          |
|      |      |          |



### 启用/禁用控制

| 方法 | 描述 | 使用示例 |
| ---- | ---- | -------- |
|      |      |          |
|      |      |          |
|      |      |          |







### 大表格

| 方法                                                 | 描述                 | 使用示例                                                    |
| ---------------------------------------------------- | -------------------- | ----------------------------------------------------------- |
|                                                      |                      |                                                             |
|                                                      |                      |                                                             |
|                                                      |                      |                                                             |
|                                                      |                      |                                                             |
| `getContentsMargins(int *l, int *t, int *r, int *b)` | 获取布局外边距       | `layout->getContentsMargins(&left, &top, &right, &bottom);` |
|                                                      |                      |                                                             |
| `spacing()`                                          | 获取项目间间距       | `int space = layout->spacing();`                            |
| `count()`                                            | 获取项目数量         | `int itemCount = layout->count();`                          |
| `itemAt(int index)`                                  | 获取指定索引处的项目 | `QLayoutItem *item = layout->itemAt(0);`                    |
| `takeAt(int index)`                                  | 移除并返回指定项目   | `QLayoutItem *item = layout->takeAt(0);`                    |
| `sizeHint()`                                         | 获取布局首选大小     | `QSize hint = layout->sizeHint();`                          |
| `minimumSize()`                                      | 获取布局最小大小     | `QSize min = layout->minimumSize();`                        |
| `maximumSize()`                                      | 获取布局最大大小     | `QSize max = layout->maximumSize();`                        |
| `setSizeConstraint(SizeConstraint)`                  | 设置大小约束策略     | `layout->setSizeConstraint(QLayout::SetFixedSize);`         |
| `activate()`                                         | 立即激活并更新布局   | `layout->activate();`                                       |
| `update()`                                           | 安排布局更新         | `layout->update();`                                         |
| `invalidate()`                                       | 使布局计算无效       | `layout->invalidate();`                                     |
| `setEnabled(bool enable)`                            | 启用或禁用布局       | `layout->setEnabled(false);`                                |
| `isEnabled()`                                        | 检查布局是否启用     | `bool enabled = layout->isEnabled();`                       |
| `setParent(QWidget *parent)`                         | 设置布局的父控件     | `layout->setParent(parentWidget);`                          |
| `parentWidget()`                                     | 获取布局的父控件     | `QWidget *parent = layout->parentWidget();`                 |



# 还没看

## 使用示例

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QPushButton>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    
    QWidget window;
    QVBoxLayout *layout = new QVBoxLayout(&window);
    
    // 使用 QLayout 的成员函数
    layout->setContentsMargins(20, 20, 20, 20); // 设置外边距
    layout->setSpacing(10); // 设置项目间距
    
    // 添加控件
    QPushButton *button1 = new QPushButton("Button 1");
    QPushButton *button2 = new QPushButton("Button 2");
    
    layout->addWidget(button1);
    layout->addWidget(button2);
    
    // 获取布局信息
    int left, top, right, bottom;
    layout->getContentsMargins(&left, &top, &right, &bottom);
    
    qDebug() << "Margins: L:" << left << "T:" << top << "R:" << right << "B:" << bottom;
    qDebug() << "Item count:" << layout->count();
    qDebug() << "Preferred size:" << layout->sizeHint();
    
    window.show();
    return app.exec();
}
```

## 重要说明

1. **QLayout 是抽象基类**，不能直接实例化。实际使用时需要创建其子类（如 QVBoxLayout、QHBoxLayout、QGridLayout 等）的实例。

2. **布局所有权**：当布局被设置给一个控件（通过 `QWidget::setLayout()`）后，该控件会成为布局的父对象，并负责管理布局的生命周期。

3. **自动内存管理**：布局会自动管理其子项（控件和子布局）的生命周期。当布局被删除时，它会删除所有子项。

4. **多线程注意事项**：QLayout 及其子类不是线程安全的，应在主线程（GUI 线程）中使用。

QLayout 提供了强大而灵活的界面布局管理功能，是 Qt 界面编程的核心组件之一。通过合理使用这些成员函数，可以创建出各种复杂且响应式的用户界面。

## **布局常用方法**
| 方法                                             | 说明                                                       |
| ------------------------------------------------ | ---------------------------------------------------------- |
| `addWidget(QWidget *)`                           | 添加控件到布局                                             |
| `addLayout(QLayout *)`                           | 添加子布局（用于嵌套）                                     |
| `setContentsMargins(int l, int t, int r, int b)` | 设置边距（左、上、右、下）                                 |
| `setSpacing(int)`                                | 设置控件之间的间距                                         |
| `setStretch(int index, int stretch)`             | 设置控件或行的拉伸比例（`QVBoxLayout`/`QHBoxLayout` 适用） |

## **总结**
- **`QVBoxLayout` / `QHBoxLayout`**：简单纵向或横向排列。
- **`QGridLayout`**：适用于表格类复杂布局。
- **`QFormLayout`**：专门用于表单（标签 + 输入框）。
- **`QStackedLayout`**：多个页面重叠，每次显示一个。
- **布局可以嵌套**，实现更复杂的界面设计。

使用布局管理器可以 **自动适应窗口大小变化**，比手动 `move()` 和 `resize()` 更灵活、更易维护。推荐在 Qt 开发中优先使用布局类！