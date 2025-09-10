# 所有用户界面组件的基类QWidget

`QWidget` 是所有窗口类的父类

- 创建新窗口时如果未指定父对象，则新窗口是独立窗口。

- 创建新窗口时如果指定父对象，则新窗口不是独立窗口。



## 构造函数和析构函数

| 函数                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `QWidget(QWidget *parent = nullptr, Qt::WindowFlags f = Qt::WindowFlags())` | 构造一个窗口部件。`parent` 参数管理内存和父子关系，`f` 参数可以设置窗口类型（如弹出框、工具窗口等）。 |
| `~QWidget()`                                                 | 析构函数。会自动销毁其所有子对象。                           |

## 成员函数

### 外观和样式

| 函数                                     | 描述                                                 | 案例                                         |
| ---------------------------------------- | ---------------------------------------------------- | -------------------------------------------- |
| setStyleSheet(const QString &styleSheet) | 使用 **QSS**（类似 CSS）设置部件的样式表，非常强大。 | `button->setStyleSheet("font-size: 16px;");` |
|                                          |                                                      |                                              |
|                                          |                                                      |                                              |

### 控件切换

| 函数                                                  | 描述                                  | 案例                                  |
| ----------------------------------------------------- | ------------------------------------- | ------------------------------------- |
| QWidget::setTabOrder(QWidget *first, QWidget *second) | 设置两个控件之间的 Tab 键焦点切换顺序 | `QWidget::setTabOrder(header, data);` |
|                                                       |                                       |                                       |
|                                                       |                                       |                                       |















# 未读



### 函数

#### 

|      |      |
| :--- | :--- |
|      |      |
|      |      |

#### 几何属性（位置和大小）

| 函数                                                         | 描述                                                  |
| :----------------------------------------------------------- | :---------------------------------------------------- |
| **获取几何信息**                                             |                                                       |
| `int x() const` `int y() const`                              | 返回部件相对于其父部件的位置坐标。                    |
| `QPoint pos() const`                                         | 返回部件左上角相对于其父部件的位置（QPoint）。        |
| `int width() const` `int height() const`                     | 返回不包括窗口框架的客户区宽度和高度。                |
| `QSize size() const`                                         | 返回部件的尺寸（QSize，包含 width 和 height）。       |
| `QRect geometry() const`                                     | 返回相对于父部件的内部几何区域（不包括窗口边框）。    |
| `QRect frameGeometry() const`                                | 返回包括窗口框架的几何区域（相对于父部件）。          |
| `QRect rect() const`                                         | 返回部件内部的矩形，左上角永远是 (0, 0)。常用于绘图。 |
| **设置几何信息**                                             |                                                       |
| `void move(int x, int y)` `void move(const QPoint &pos)`     | 移动部件到某个位置（相对于其父部件）。                |
| `void resize(int w, int h)` `void resize(const QSize &size)` | 调整部件的大小（不包含窗口框架）。                    |
| `void setGeometry(int x, int y, int w, int h)` `void setGeometry(const QRect &rect)` | **一举两得**：同时设置位置和大小。                    |
| **大小策略和限制**                                           |                                                       |
| `QSize sizeHint() const`                                     | 返回布局系统建议的“理想”大小。常被重写。              |
| `QSize minimumSizeHint() const`                              | 返回布局系统建议的“最小”大小。常被重写。              |
| `void setMinimumSize(const QSize &)` `void setMaximumSize(const QSize &)` `void setFixedSize(const QSize &)` | 设置部件的最小、最大或固定大小。                      |
| `void setSizePolicy(QSizePolicy policy)`                     | 设置部件在布局中的大小策略（如可扩展、固定等）。      |

#### 显示和隐藏

| 函数                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `void show()`                                                | 显示部件及其子部件。如果部件之前没有大小，系统会先给它一个默认大小。 |
| `void hide()`                                                | 隐藏部件。相当于 `setVisible(false)`。                       |
| `void setVisible(bool visible)`                              | 设置部件的可见状态。                                         |
| `void showFullScreen()` `showMaximized()` `showMinimized()` `showNormal()` | 将窗口设置为全屏、最大化、最小化或正常状态。                 |
| `bool isVisible() const`                                     | 判断部件当前是否可见。                                       |
| `void close()`                                               | 关闭部件。如果部件接受了关闭事件（`closeEvent`），它将会被隐藏。 |
| `bool isHidden() const`                                      | 判断部件是否被明确隐藏（`hide()`），即 `isVisible()` 返回 false 且不是因为被父部件遮挡。 |
| `void raise()` `void lower()`                                | 将部件堆叠到同级部件的最上层或最下层。                       |



#### 外观和样式

| 函数                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `void setWindowTitle(const QString &)` `QString windowTitle() const` | 设置/获取窗口标题（对于顶级窗口尤其有用）。                  |
| `void setWindowIcon(const QIcon &icon)`                      | 设置窗口图标（对于顶级窗口尤其有用）。                       |
|                                                              |                                                              |
| `void setPalette(const QPalette &)` `QPalette palette() const` | 设置/获取调色板，用于绘制部件的颜色组。                      |
| `void setFont(const QFont &)` `QFont font() const`           | 设置/获取部件的字体。                                        |
| `void setCursor(const QCursor &)` `void unsetCursor()`       | 设置/取消当鼠标悬停在部件上时的光标形状。                    |
| `void setMouseTracking(bool enable)`                         | 设置是否启用鼠标跟踪。如果启用，即使不按下鼠标按钮，也会触发鼠标移动事件（`mouseMoveEvent`）。 |
| `void setFocusPolicy(Qt::FocusPolicy policy)`                | 设置部件如何获得键盘焦点（如通过Tab键、鼠标点击等）。        |
| `void setEnabled(bool enabled)` `bool isEnabled() const`     | 启用或禁用部件。被禁用的部件无法接收鼠标和键盘事件，通常外观会变灰。 |
| `void update()` `void repaint()`                             | **强烈推荐使用 `update()`**。它会安排一个重绘事件，让Qt在下次事件循环中高效地合并和重绘区域。`repaint()` 会立即强制重绘，可能会打断当前操作，应避免使用。 |

---

父子关系和窗口状态

| 函数                                       | 描述                                                         |
| :----------------------------------------- | :----------------------------------------------------------- |
| `void setParent(QWidget *parent)`          | 设置部件的父对象。这会改变部件的所有权和显示层级。           |
| `QWidget* parentWidget() const`            | 返回父部件指针。                                             |
| `QList<QWidget*> findChildren<QWidget*>()` | 查找所有子部件。                                             |
| `bool isWindow() const`                    | 判断该部件是否是独立窗口（顶级窗口，没有父部件或父部件不是窗口）。 |
| `QWidget* window() const`                  | 返回该部件所属的顶级窗口。                                   |
| `WId winId() const`                        | 返回底层窗口系统的窗口标识符（Handle），用于与原生API交互。  |



#### 事件处理（通常需要重写）

这些是**保护成员函数**，你需要在自定义的 `QWidget` 子类中重写它们来处理特定事件。

| 函数                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `void mousePressEvent(QMouseEvent *event)`                   | 处理鼠标按下事件。                                           |
| `void mouseReleaseEvent(QMouseEvent *event)`                 | 处理鼠标释放事件。                                           |
| `void mouseMoveEvent(QMouseEvent *event)`                    | 处理鼠标移动事件（需要先按下按钮，除非设置了 `setMouseTracking(true)`）。 |
| `void paintEvent(QPaintEvent *event)`                        | **极其重要**。用于绘制部件的内容。所有自定义绘图都在这里进行。 |
| `void resizeEvent(QResizeEvent *event)`                      | 处理部件大小改变事件。                                       |
| `void keyPressEvent(QKeyEvent *event)` `void keyReleaseEvent(QKeyEvent *event)` | 处理键盘按下和释放事件（部件需要有焦点）。                   |
| `void focusInEvent(QFocusEvent *event)` `void focusOutEvent(QFocusEvent *event)` | 处理获得和失去焦点的事件。                                   |
| `void closeEvent(QCloseEvent *event)`                        | 处理窗口关闭事件。可以拦截关闭操作（例如弹出“是否保存？”对话框）。 |
| `void showEvent(QShowEvent *event)` `void hideEvent(QHideEvent *event)` | 处理显示和隐藏事件。                                         |
| `void wheelEvent(QWheelEvent *event)`                        | 处理鼠标滚轮事件。                                           |
| `void enterEvent(QEnterEvent *event)` `void leaveEvent(QEvent *event)` | 处理鼠标进入和离开部件区域的事件。                           |



#### 布局管理

| 函数                              | 描述                       |
| :-------------------------------- | :------------------------- |
| `void setLayout(QLayout *layout)` | 为部件设置一个布局管理器。 |
| `QLayout* layout() const`         | 返回部件当前的布局管理器。 |
| `void adjustSize()`               | 调整部件大小以适应其内容。 |



#### 其他实用函数

| 函数                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `void grab(const QRect &rectangle = QRect(QPoint(0, 0), QSize(-1, -1)))` | 将部件渲染到一個 `QPixmap` 中并返回，用于截图。              |
| `bool hasFocus() const`                                      | 判断部件当前是否拥有键盘焦点。                               |
| `void setFocus()`                                            | 尝试让部件获得键盘焦点。                                     |
| `void setAttribute(Qt::WidgetAttribute attribute, bool on = true)` | 设置窗口属性，例如 `Qt::WA_DeleteOnClose`（关闭时自动删除），`Qt::WA_TranslucentBackground`（半透明背景）等。 |
| `void setWindowFlags(Qt::WindowFlags type)`                  | 设置窗口的标志，可以改变窗口的边框、标题栏、是否置顶等行为。使用时要小心，最好在 `show()` 之前调用。 |
| `void setWindowState(Qt::WindowStates state)`                | 设置窗口的状态（全屏、最大化等）。                           |

