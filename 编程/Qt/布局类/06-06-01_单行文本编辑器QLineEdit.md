# 单行文本输入和编辑QLineEdit

## QLineEdit类简介

QLineEdit 是 Qt 中用于处理**单行文本输入和编辑**的控件，它提供了基本的文本编辑功能，是用户界面中最常用的输入控件之一。QLineEdit 支持文本验证、输入掩码、自动完成、密码模式等多种功能。

### 继承关系
```
QObject → QWidget → QFrame → QLineEdit
```

## 主要成员函数

### 文本内容操作

| 函数                               | 说明                             |
| ---------------------------------- | -------------------------------- |
| `QString text() const`             | 获取当前文本框中的文本内容       |
| `void setText(const QString &)`    | 设置文本框的文本内容             |
| `void clear()`                     | 清空文本框内容                   |
| `void insert(const QString &text)` | 在光标位置插入文本               |
| `void backspace()`                 | 执行退格操作（删除光标前的字符） |
| `void del()`                       | 执行删除操作（删除光标后的字符） |

### 2. 文本选择与光标控制

| 函数                                            | 说明                       |
| ----------------------------------------------- | -------------------------- |
| `QString selectedText() const`                  | 获取选中的文本             |
| `bool hasSelectedText() const`                  | 检查是否有文本被选中       |
| `void selectAll()`                              | 选中所有文本               |
| `void deselect()`                               | 取消文本选择               |
| `int cursorPosition() const`                    | 获取当前光标位置           |
| `void setCursorPosition(int)`                   | 设置光标位置               |
| `void cursorForward(bool mark, int steps = 1)`  | 将光标向前移动指定步数     |
| `void cursorBackward(bool mark, int steps = 1)` | 将光标向后移动指定步数     |
| `void cursorWordForward(bool mark)`             | 将光标移动到下一个单词开头 |
| `void cursorWordBackward(bool mark)`            | 将光标移动到上一个单词开头 |
| `void home(bool mark)`                          | 将光标移动到行首           |
| `void end(bool mark)`                           | 将光标移动到行尾           |

### 3. 文本验证与输入限制

| 函数                                          | 说明                         |
| --------------------------------------------- | ---------------------------- |
| `void setValidator(const QValidator *)`       | 设置验证器，限制输入内容     |
| `const QValidator *validator() const`         | 获取当前验证器               |
| `void setInputMask(const QString &inputMask)` | 设置输入掩码，格式化输入     |
| `QString inputMask() const`                   | 获取当前输入掩码             |
| `void setMaxLength(int)`                      | 设置最大输入长度             |
| `int maxLength() const`                       | 获取最大输入长度             |
| `bool hasAcceptableInput() const`             | 检查当前输入是否满足验证条件 |

### 4. 显示与格式设置

| 函数                                       | 说明                                 |
| ------------------------------------------ | ------------------------------------ |
| `void setEchoMode(QLineEdit::EchoMode)`    | 设置回显模式（正常、密码、不显示等） |
| `EchoMode echoMode() const`                | 获取当前回显模式                     |
| `void setAlignment(Qt::Alignment flag)`    | 设置文本对齐方式                     |
| `Qt::Alignment alignment() const`          | 获取文本对齐方式                     |
| `void setPlaceholderText(const QString &)` | 设置占位符文本（灰色提示文本）       |
| `QString placeholderText() const`          | 获取占位符文本                       |
| `void setReadOnly(bool)`                   | 设置是否为只读模式                   |
| `bool isReadOnly() const`                  | 检查是否为只读模式                   |

### 5. 编辑功能

| 函数                           | 说明                 |
| ------------------------------ | -------------------- |
| `void undo()`                  | 撤销上一次编辑操作   |
| `void redo()`                  | 重做上一次撤销的操作 |
| `bool isUndoAvailable() const` | 检查撤销操作是否可用 |
| `bool isRedoAvailable() const` | 检查重做操作是否可用 |
| `void cut()`                   | 剪切选中文本到剪贴板 |
| `void copy()`                  | 复制选中文本到剪贴板 |
| `void paste()`                 | 从剪贴板粘贴文本     |

### 6. 自动完成功能

| 函数                                       | 说明           |
| ------------------------------------------ | -------------- |
| `void setCompleter(QCompleter *completer)` | 设置自动完成器 |
| `QCompleter *completer() const`            | 获取自动完成器 |

### 7. 边框与样式

| 函数                    | 说明             |
| ----------------------- | ---------------- |
| `void setFrame(bool)`   | 设置是否显示边框 |
| `bool hasFrame() const` | 检查是否显示边框 |

## 常用信号

| 信号                                            | 说明                                     |
| ----------------------------------------------- | ---------------------------------------- |
| `textChanged(const QString &text)`              | 文本内容发生改变时发出                   |
| `textEdited(const QString &text)`               | 文本被编辑时发出（程序设置文本不会触发） |
| `returnPressed()`                               | 按下回车键时发出                         |
| `editingFinished()`                             | 编辑完成（焦点离开）时发出               |
| `selectionChanged()`                            | 文本选择发生变化时发出                   |
| `cursorPositionChanged(int oldPos, int newPos)` | 光标位置改变时发出                       |

## 使用示例

```cpp
#include <QApplication>
#include <QLineEdit>
#include <QVBoxLayout>
#include <QWidget>
#include <QDebug>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    
    QWidget window;
    QVBoxLayout *layout = new QVBoxLayout(&window);
    
    // 创建单行文本编辑器
    QLineEdit *lineEdit = new QLineEdit(&window);
    
    // 设置属性
    lineEdit->setPlaceholderText("请输入用户名");
    lineEdit->setMaxLength(20);
    lineEdit->setEchoMode(QLineEdit::Normal);
    
    // 连接信号
    QObject::connect(lineEdit, &QLineEdit::textChanged, [](const QString &text) {
        qDebug() << "文本已更改:" << text;
    });
    
    QObject::connect(lineEdit, &QLineEdit::returnPressed, []() {
        qDebug() << "按下了回车键";
    });
    
    layout->addWidget(lineEdit);
    window.resize(300, 100);
    window.show();
    
    return app.exec();
}
```

## 常用枚举值

```cpp
// 回显模式
enum EchoMode {
    Normal,     // 正常显示
    NoEcho,     // 不显示任何内容
    Password,   // 显示密码字符（如*）
    PasswordEchoOnEdit // 编辑时正常显示，否则显示密码字符
};
```

QLineEdit 是 Qt 中最基础且功能丰富的文本输入控件，适用于各种需要用户输入单行文本的场景，如用户名、密码、搜索框、URL 输入等。