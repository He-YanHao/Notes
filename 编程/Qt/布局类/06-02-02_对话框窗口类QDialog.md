## 对话框窗口类QDialog

是 `QWidget` 的子类。

`QDialog ` 是 `Qt Widgets` 模块中用于创建对话框的基础类，是所有对话框的基类。对话框是用户界面中常见的临时窗口，用于与用户进行短期交互，如获取输入、显示消息或进行特定操作。

不能内嵌。







# 未读







### 函数

| 函数                  | 描述                            |
| :-------------------- | :------------------------------ |
| `int exec()`          | 以模态方式显示对话框，返回结果  |
| `void open()`         | 以半模态方式显示对话框          |
| `void accept()`       | 关闭对话框并设置结果为 Accepted |
| `void reject()`       | 关闭对话框并设置结果为 Rejected |
| `void setModal(bool)` | 设置对话框是否为模态            |
| `void setResult(int)` | 设置对话框结果值                |
| `void show()`         | 以非模态方式显示对话框          |

### 标准对话框

Qt 提供了一系列预定义的标准对话框：

1. **QMessageBox** - 消息提示框

   ```cpp
   QMessageBox::information(this, "标题", "消息内容");
   ```

2. **QFileDialog** - 文件选择对话框

   ```cpp
   QString fileName = QFileDialog::getOpenFileName(this, "打开文件");
   ```

3. **QColorDialog** - 颜色选择对话框

   ```cpp
   QColor color = QColorDialog::getColor(Qt::white, this);
   ```

4. **QFontDialog** - 字体选择对话框

   ```cpp
   bool ok;
   QFont font = QFontDialog::getFont(&ok, QFont("Arial", 10), this);
   ```

5. **QInputDialog** - 输入对话框

   ```cpp
   QString text = QInputDialog::getText(this, "输入", "请输入内容:");
   ```

