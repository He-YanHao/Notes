# Qt 中的 QPushButton 类详解

QPushButton 是 Qt Widgets 模块中最常用的按钮控件之一，它继承自 QAbstractButton 类，提供了标准的可点击按钮功能。

## 基本特性

### 1. 核心功能
- 显示文本和/或图标
- 响应鼠标点击、键盘激活等用户交互
- 支持按下/释放/悬停等不同状态
- 可设置为默认按钮或自动默认按钮

### 2. 继承关系
```
QObject → QWidget → QAbstractButton → QPushButton

```

## 创建按钮

### 1. 基本创建方式
```cpp
// 创建带文本的按钮
QPushButton *button = new QPushButton("点击我", this);

// 创建带图标的按钮
QPushButton *iconButton = new QPushButton(QIcon(":/images/icon.png"), "带图标的按钮", this);

// 创建空按钮后续设置
QPushButton *emptyButton = new QPushButton(this);
emptyButton->setText("后来设置的文本");
```

### 2. 常用构造函数
| 构造函数                                                     | 描述                 |
| ------------------------------------------------------------ | -------------------- |
| `QPushButton(QWidget *parent = nullptr)`                     | 创建空按钮           |
| `QPushButton(const QString &text, QWidget *parent = nullptr)` | 创建带文本按钮       |
| `QPushButton(const QIcon &icon, const QString &text, QWidget *parent = nullptr)` | 创建带图标和文本按钮 |

## 主要属性与方法

### 1. 外观控制
| 方法                         | 描述               |
| ---------------------------- | ------------------ |
| `setText(const QString &)`   | 设置按钮文本       |
| `text()`                     | 获取按钮文本       |
| `setIcon(const QIcon &)`     | 设置按钮图标       |
| `icon()`                     | 获取按钮图标       |
| `setIconSize(const QSize &)` | 设置图标大小       |
| `iconSize()`                 | 获取图标大小       |
| `setFlat(bool)`              | 设置是否为扁平样式 |
| `isFlat()`                   | 检查是否为扁平样式 |

### 2. 行为控制
| 方法                         | 描述                     |
| ---------------------------- | ------------------------ |
| `setCheckable(bool)`         | 设置按钮是否可切换状态   |
| `isCheckable()`              | 检查按钮是否可切换       |
| `setChecked(bool)`           | 设置按钮选中状态         |
| `isChecked()`                | 检查按钮是否选中         |
| `setDown(bool)`              | 设置按钮按下状态         |
| `isDown()`                   | 检查按钮是否被按下       |
| `setAutoRepeat(bool)`        | 设置是否启用自动重复     |
| `autoRepeat()`               | 检查是否启用自动重复     |
| `setAutoRepeatDelay(int)`    | 设置自动重复初始延迟(ms) |
| `autoRepeatDelay()`          | 获取自动重复初始延迟     |
| `setAutoRepeatInterval(int)` | 设置自动重复间隔(ms)     |
| `autoRepeatInterval()`       | 获取自动重复间隔         |

### 3. 默认按钮设置
| 方法                   | 描述                 |
| ---------------------- | -------------------- |
| `setDefault(bool)`     | 设置为对话框默认按钮 |
| `isDefault()`          | 检查是否为默认按钮   |
| `setAutoDefault(bool)` | 设置自动默认行为     |
| `autoDefault()`        | 检查自动默认设置     |

## 信号与槽

### 1. 主要信号
| 信号                    | 描述                             |
| ----------------------- | -------------------------------- |
| `clicked()`             | 按钮被点击时发出                 |
| `clicked(bool checked)` | 按钮被点击时发出，带当前选中状态 |
| `pressed()`             | 按钮被按下时发出                 |
| `released()`            | 按钮被释放时发出                 |
| `toggled(bool checked)` | 按钮选中状态改变时发出           |

### 2. 常用连接方式
```cpp
// 传统连接方式
connect(button, SIGNAL(clicked()), this, SLOT(handleButtonClick()));

// Qt5推荐的类型安全连接
connect(button, &QPushButton::clicked, this, &MyClass::handleButtonClick);

// 使用lambda表达式
connect(button, &QPushButton::clicked, [=](){
    qDebug() << "按钮被点击了!";
});
```

## 样式定制

### 1. 使用QSS样式表
```cpp
// 设置普通样式
button->setStyleSheet("QPushButton {"
                      "background-color: #4CAF50;"
                      "border: none;"
                      "color: white;"
                      "padding: 8px 16px;"
                      "text-align: center;"
                      "font-size: 14px;"
                      "}");

// 设置悬停和按下状态
button->setStyleSheet("QPushButton {"
                      "background-color: #4CAF50;"
                      "}"
                      "QPushButton:hover {"
                      "background-color: #45a049;"
                      "}"
                      "QPushButton:pressed {"
                      "background-color: #3d8b40;"
                      "}");
```

### 2. 使用QPalette调色板
```cpp
QPalette palette = button->palette();
palette.setColor(QPalette::Button, QColor(Qt::blue));
palette.setColor(QPalette::ButtonText, QColor(Qt::white));
button->setPalette(palette);
button->setAutoFillBackground(true);
```

## 高级用法

### 1. 创建自定义按钮
```cpp
class CustomButton : public QPushButton {
    Q_OBJECT
public:
    explicit CustomButton(QWidget *parent = nullptr) : QPushButton(parent) {
        // 自定义初始化
    }

protected:
    void paintEvent(QPaintEvent *e) override {
        // 自定义绘制
        QPushButton::paintEvent(e);
    }
    
    void enterEvent(QEvent *e) override {
        // 鼠标进入效果
        QPushButton::enterEvent(e);
    }
    
    void leaveEvent(QEvent *e) override {
        // 鼠标离开效果
        QPushButton::leaveEvent(e);
    }
};
```

### 2. 按钮组管理
```cpp
// 创建互斥的按钮组
QButtonGroup *buttonGroup = new QButtonGroup(this);
buttonGroup->addButton(button1, 1);
buttonGroup->addButton(button2, 2);
buttonGroup->addButton(button3, 3);
buttonGroup->setExclusive(true); // 设置互斥

// 连接按钮组信号
connect(buttonGroup, QOverload<QAbstractButton *>::of(&QButtonGroup::buttonClicked),
        [=](QAbstractButton *button){
    qDebug() << "选中按钮ID:" << buttonGroup->id(button);
});
```

## 实际应用示例

### 1. 对话框按钮盒
```cpp
QDialog dialog;
QPushButton *okButton = new QPushButton("确定", &dialog);
QPushButton *cancelButton = new QPushButton("取消", &dialog);

// 设置为默认按钮
okButton->setDefault(true);

// 连接信号
connect(okButton, &QPushButton::clicked, &dialog, &QDialog::accept);
connect(cancelButton, &QPushButton::clicked, &dialog, &QDialog::reject);
```

### 2. 带菜单的按钮
```cpp
QPushButton *menuButton = new QPushButton("选项", this);
QMenu *dropdownMenu = new QMenu(this);
dropdownMenu->addAction("选项1");
dropdownMenu->addAction("选项2");
menuButton->setMenu(dropdownMenu);
```

QPushButton 是 Qt 中最基础但功能丰富的控件之一，通过灵活运用其各种属性和方法，可以创建出满足各种需求的交互式按钮。