# 主窗口类QMainWindow

QMainWindow 是 Qt Widgets 模块中用于创建**主窗口应用程序**的核心类，为典型的桌面应用程序提供了标准框架结构和丰富的功能支持。

## 函数

### 窗口属性设置

| 方法               | 描述                             | 使用示例                                                     |
| ------------------ | -------------------------------- | ------------------------------------------------------------ |
| setWindowTitle()   | 设置窗口的标题栏文本             | `mainWindow.setWindowTitle("我的应用程序");`                 |
| setCentralWidget() | 设置主窗口布局结构中的中心区域。 | `window.setCentralWidget(centralWidget/*中心区域组件的指针*/); ` |
|                    |                                  |                                                              |
|                    |                                  |                                                              |

### 窗口几何尺寸方法

**设置函数**

| 方法                                    | 描述                   | 使用示例                                   |
| --------------------------------------- | ---------------------- | ------------------------------------------ |
| resize(int w, int h)                    | 设置窗口大小           | `mainWindow.resize(800, 600);`             |
| move(int x, int y)                      | 设置窗口位置           | `mainWindow.move(100, 100);`               |
| setGeometry(int x, int y, int w, int h) | 设置位置和大小         | `mainWindow.setGeometry(100,100,800,600);` |
| setFixedHeight(int h)                   | 设置固定高度           |                                            |
| setFixedWidth(int w)                    | 设置固定宽度           |                                            |
| setFixedSize(int w, int h)              | 同时设置固定宽度和高度 |                                            |
| setMinimumHeight(int h)                 | 设置最小高度（可拉伸） |                                            |
| setMaximumHeight(int h)                 | 设置最大高度（可收缩） |                                            |
|                                         |                        |                                            |

**获取函数**

| 方法     | 描述          | 使用示例                       |
| -------- | ------------- | ------------------------------ |
| x()      | 获取窗口X坐标 | `int x = mainWindow.x();`      |
| y()      | 获取窗口Y坐标 | `int y = mainWindow.y();`      |
| width()  | 获取窗口宽度  | `int w = mainWindow.width();`  |
| height() | 获取窗口高度  | `int h = mainWindow.height();` |
|          |               |                                |
|          |               |                                |





### 窗口显示控制方法

| 方法             | 描述               | 使用示例                       |
| ---------------- | ------------------ | ------------------------------ |
| show()           | 显示窗口（非模态） | `mainWindow.show();`           |
| hide()           | 隐藏窗口           | `mainWindow.hide();`           |
| showFullScreen() | 全屏显示           | `mainWindow.showFullScreen();` |
| showMaximized()  | 最大化显示         | `mainWindow.showMaximized();`  |
| showMinimized()  | 最小化显示         | `mainWindow.showMinimized();`  |
| showNormal()     | 恢复正常大小       | `mainWindow.showNormal();`     |

# 未读















### 

#### 窗口状态判断方法

| 方法             | 描述           | 返回值 |
| ---------------- | -------------- | ------ |
| `isVisible()`    | 窗口是否可见   | bool   |
| `isHidden()`     | 窗口是否隐藏   | bool   |
| `isMinimized()`  | 窗口是否最小化 | bool   |
| `isMaximized()`  | 窗口是否最大化 | bool   |
| `isFullScreen()` | 窗口是否全屏   | bool   |

#### 窗口关闭相关方法

| 方法                                 | 描述               | 使用示例                                         |
| ------------------------------------ | ------------------ | ------------------------------------------------ |
| `close()`                            | 关闭窗口           | `mainWindow.close();`                            |
| `setAttribute(Qt::WA_DeleteOnClose)` | 设置关闭时自动删除 | `mainWindow.setAttribute(Qt::WA_DeleteOnClose);` |
| `isEnabled()`                        | 检查窗口是否可用   | `if(mainWindow.isEnabled()) {...}`               |
| `setEnabled(bool)`                   | 设置窗口可用状态   | `mainWindow.setEnabled(false);`                  |

#### 

|      |      |      |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |

#### 窗口行为控制方法

| 方法                                    | 描述           | 使用示例                                              |
| --------------------------------------- | -------------- | ----------------------------------------------------- |
| `raise()`                               | 将窗口置于顶层 | `mainWindow.raise();`                                 |
| `activateWindow()`                      | 激活窗口       | `mainWindow.activateWindow();`                        |
| `setWindowModality(Qt::WindowModality)` | 设置模态类型   | `mainWindow.setWindowModality(Qt::ApplicationModal);` |
| `setWindowFlags(Qt::WindowFlags)`       | 设置窗口标志   | `mainWindow.setWindowFlags(Qt::FramelessWindowHint);` |

#### 窗口整体布局结构

| 函数             | 所属类        | 用途                                                         |
| :--------------- | :------------ | :----------------------------------------------------------- |
|                  |               |                                                              |
| `setLayout()`    | `QWidget`     | 为**任何**QWidget（或其子类）设置一个布局管理器（如QHBoxLayout, QVBoxLayout）。 |
| `setMenuBar()`   | `QMainWindow` | 设置主窗口的菜单栏。                                         |
| `setStatusBar()` | `QMainWindow` | 设置主窗口的状态栏。                                         |

#### 

|      |      |      |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |



### 重要说明

1. `show()` 和 `exec()` 的区别：

   - `show()`: 非模态显示，立即返回

- `exec()`: 模态显示，进入事件循环，直到窗口关闭

2. 关闭行为控制：

   - 可以重写 `closeEvent(QCloseEvent*)` 来拦截关闭操作

   ```cpp
   void MainWindow::closeEvent(QCloseEvent *event) {
       if(needConfirmClose) {
           QMessageBox::StandardButton reply;
           reply = QMessageBox::question(this, "确认", "确定要退出吗?",
                                        QMessageBox::Yes|QMessageBox::No);
           if(reply == QMessageBox::No) {
               event->ignore();  // 忽略关闭事件
           } else {
               event->accept();  // 接受关闭事件
           }
       }
   }
   ```

3. 窗口生命周期：

   - 默认情况下窗口关闭时不会自动删除
   - 设置 `WA_DeleteOnClose` 属性后会在关闭时自动删除

这些基础方法是使用 QMainWindow 的起点，掌握了这些方法后，就可以进行更复杂的窗口操作和界面开发了。





## 综合

| 类              | 定位         | 典型用途                     |
| :-------------- | :----------- | :--------------------------- |
| **QWidget**     | 基础窗口部件 | 自定义控件、简单窗口、子窗口 |
| **QDialog**     | 对话框窗口   | 用户交互、设置面板、消息提示 |
| **QMainWindow** | 主应用窗口   | 复杂桌面应用、编辑器、IDE    |



