# 多行富文本编辑器QTextEdit

## 简介

QTextEdit 是 Qt 中用于**多行富文本编辑和显示**的高级控件，它提供了完整的文本编辑功能，支持纯文本和富文本（HTML）格式，是构建文本编辑器、日志查看器、聊天窗口等应用的理想选择。

### 继承关系
```
QObject → QWidget → QFrame → QAbstractScrollArea → QTextEdit
```

## 成员函数（非继承）

### 添加内容

```cpp
TextEdit->append("追加的文本内容");
```

