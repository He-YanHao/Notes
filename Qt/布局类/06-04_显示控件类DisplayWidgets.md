# 显示控件DisplayWidgets类

## 2. QTextBrowser - 文本浏览器（增强型文本显示）

**用途**：显示富文本，支持超链接导航和基本文本格式。

### 特有功能（相较于QLabel）：
- **`setSource(const QUrl &name)`**：设置显示的内容源
- **`backward()`** / **`forward()`**：向后/向前导航历史
- **`home()`**：返回主页
- **`reload()`**：重新加载当前内容
- **`setOpenLinks(bool open)`**：设置是否自动打开链接
- **`setOpenExternalLinks(bool open)`**：设置是否打开外部链接
- **`anchorClicked(const QUrl &link)`**信号：点击链接时发出

```cpp
// 示例：创建一个简单的文本浏览器
QTextBrowser *textBrowser = new QTextBrowser();
textBrowser->setSource(QUrl("qrc:/help/intro.html")); // 显示HTML内容
textBrowser->setOpenExternalLinks(true);
```

---

## 3. QLCDNumber - LCD数字显示

**用途**：模拟LCD数字显示屏，适合显示数字。

### 核心成员：
- **`display(int num)`** / **`display(double num)`**：显示数字
- **`setDigitCount(int numDigits)`**：设置显示的数字位数
- **`setMode(QLCDNumber::Mode mode)`**：设置显示模式（`Hex`, `Dec`, `Oct`, `Bin`）
- **`setSegmentStyle(QLCDNumber::SegmentStyle style)`**：设置段样式（`Outline`, `Filled`, `Flat`）
- **`setSmallDecimalPoint(bool enable)`**：设置小数点样式
- **`value() const`**：获取当前显示的值（double）
- **`intValue() const`**：获取当前显示的值（int）

```cpp
// 示例：创建一个LCD数字显示器
QLCDNumber *lcd = new QLCDNumber();
lcd->setDigitCount(8);        // 显示8位数字
lcd->setMode(QLCDNumber::Dec);// 十进制模式
lcd->setSegmentStyle(QLCDNumber::Filled); // 填充样式
lcd->display(123.456);        // 显示数字
```

---

## 4. QProgressBar - 进度条

**用途**：显示任务进度。

### 核心成员：
- **`setValue(int value)`**：设置当前值
- **`setMinimum(int minimum)`** / **`setMaximum(int maximum)`**：设置范围
- **`setRange(int minimum, int maximum)`**：同时设置最小最大值
- **`setFormat(const QString &format)`**：设置显示格式（如"%p%"显示百分比）
- **`setOrientation(Qt::Orientation orientation)`**：设置方向（水平或垂直）
- **`setTextVisible(bool visible)`**：设置文本是否可见
- **`reset()`**：重置进度条
- **`value() const`**：获取当前值

```cpp
// 示例：创建一个进度条
QProgressBar *progressBar = new QProgressBar();
progressBar->setRange(0, 100);    // 范围0-100
progressBar->setValue(50);         // 当前进度50%
progressBar->setFormat("已完成: %p%"); // 显示格式
progressBar->setTextVisible(true); // 显示进度文本
```

---

## 5. QCalendarWidget - 日历控件

**用途**：显示和选择日期。

### 核心成员：
- **`setSelectedDate(const QDate &date)`**：设置选中的日期
- **`selectedDate() const`**：获取选中的日期
- **`setMinimumDate(const QDate &date)`** / **`setMaximumDate(const QDate &date)`**：设置可选日期范围
- **`setGridVisible(bool show)`**：设置是否显示网格
- **`setNavigationBarVisible(bool visible)`**：设置导航栏是否可见
- **`setFirstDayOfWeek(Qt::DayOfWeek dayOfWeek)`**：设置每周的第一天
- **`showToday()`**：跳转到今天
- **`showSelectedDate()`**：跳转到选中的日期
- **`selectionChanged()`**信号：当选中的日期改变时发出

```cpp
// 示例：创建一个日历控件
QCalendarWidget *calendar = new QCalendarWidget();
calendar->setGridVisible(true);
calendar->setSelectedDate(QDate::currentDate()); // 选中今天
connect(calendar, &QCalendarWidget::selectionChanged, [=]() {
    qDebug() << "选中了日期:" << calendar->selectedDate().toString();
});
```

## 总结对比表

| 控件                | 主要用途      | 核心特性               | 适用场景                       |
| :------------------ | :------------ | :--------------------- | :----------------------------- |
| **QLabel**          | 显示文本/图片 | 轻量、灵活、支持HTML   | 标签、提示、简单信息展示       |
| **QTextBrowser**    | 显示富文本    | 支持超链接、历史导航   | 帮助文档、说明文字、简单浏览器 |
| **QLCDNumber**      | 显示数字      | LCD样式、多种进制      | 计数器、计时器、数字仪表盘     |
| **QProgressBar**    | 显示进度      | 可视化进度、可定制格式 | 文件复制、下载进度、任务完成度 |
| **QCalendarWidget** | 日期选择      | 完整日历功能、日期选择 | 日程安排、日期选择器           |

## 使用建议

1.  **QLabel**：适合大多数简单的信息展示需求
2.  **QTextBrowser**：需要显示格式化文本或超链接时使用
3.  **QLCDNumber**：需要模拟数字显示屏效果时使用
4.  **QProgressBar**：需要显示任务进度时使用
5.  **QCalendarWidget**：需要选择或显示日期时使用

这些显示控件通常与**信号和槽**机制结合使用，实现动态更新显示内容。例如，进度条的值可以随着后台任务的进度而更新，日历的日期选择可以触发其他控件的更新等。