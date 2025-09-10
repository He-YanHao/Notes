# 数字显示QLCDNumber



## 构造函数&析构函数

### 构造函数

```cpp
QLCDNumber(QWidget *parent = nullptr)
```

创建一个新的 QLCDNumber 控件。 

```cpp
QLCDNumber(uint numDigits, QWidget *parent = nullptr)
```

创建并设置显示位数的 QLCDNumber 控件。

### 析构函数

**`~QLCDNumber()`**  

析构函数。

销毁 QLCDNumber 对象。  自动调用，通常无需手动处理。

## 成员函数详解

### 设置数值显示

| **`void display(const QString &s)`** | 显示字符串。字符串中的非数字字符会显示为特殊符号。   | `lcd->display("12:30:45");` |
| ------------------------------------ | ---------------------------------------------------- | --------------------------- |
| **`void display(int num)`**          | 显示整数值。根据当前模式（十进制、十六进制等）显示。 | `lcd->display(123);`        |
| **`void display(double num)`**       | 显示浮点数值。会自动处理小数点的显示。               | `lcd->display(98.6);`       |
|                                      |                                                      |                             |
|                                      |                                                      |                             |
|                                      |                                                      |                             |

### 显示模式设置

| **`void setMode(QLCDNumber::Mode mode)`** | 设置显示模式。                             | `lcd->setMode(QLCDNumber::Hex); // 十六进制模式` |
| ----------------------------------------- | ------------------------------------------ | ------------------------------------------------ |
| **`void setDigitCount(int numDigits)`**   | 设置显示的位数。必须足够容纳要显示的内容。 | `lcd->setDigitCount(8); // 设置显示8位`          |
|                                           |                                            |                                                  |
|                                           |                                            |                                                  |



### 返回显示

| **`int digitCount() const`**        | 返回当前设置的显示位数。                   | `int digits = lcd->digitCount(); // 返回8` |
| ----------------------------------- | ------------------------------------------ | ------------------------------------------ |
| **`QLCDNumber::Mode mode() const`** | 返回当前的显示模式（十进制、十六进制等）。 | `QLCDNumber::Mode m = lcd->mode();`        |
| **`int intValue() const`**          | 返回当前显示的整数值。                     | `int num = lcd->intValue(); // 获取整数值` |
| **`double value() const`**          | 返回当前显示的数值（如果是数值显示）。     | `double val = lcd->value(); // 获取当前值` |



|                                                            |                                            |                                                          |
| :--------------------------------------------------------- | :----------------------------------------- | :------------------------------------------------------- |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
| **`bool checkOverflow(double num) const`**                 | 检查给定的数值是否会溢出（超出显示范围）。 | `if (lcd->checkOverflow(9999)) qDebug() << "Overflow!";` |
| **`bool checkOverflow(int num) const`**                    | 检查给定的整数值是否会溢出。               | `if (lcd->checkOverflow(100000)) qDebug() << "Too big";` |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
| **`QLCDNumber::SegmentStyle segmentStyle() const`**        | 返回当前的段显示样式。                     | `QLCDNumber::SegmentStyle style = lcd->segmentStyle();`  |
| **`void setSegmentStyle(QLCDNumber::SegmentStyle style)`** | 设置段显示样式，影响外观。                 | `lcd->setSegmentStyle(QLCDNumber::Filled);`              |
|                                                            |                                            |                                                          |
|                                                            |                                            |                                                          |
| **`bool smallDecimalPoint() const`**                       | 返回是否使用小小数点模式。                 | `bool small = lcd->smallDecimalPoint();`                 |
| **`void setSmallDecimalPoint(bool small)`**                | 设置是否使用小小数点模式。                 | `lcd->setSmallDecimalPoint(true);`                       |
| **`QSize sizeHint() const override`**                      | 返回控件的建议大小（重写自QWidget）。      | `QSize hint = lcd->sizeHint();`                          |

---

## 枚举类型详解

### QLCDNumber::Mode - 显示模式

| 枚举值                | 描述                     | 案例                                                         |
| :-------------------- | :----------------------- | :----------------------------------------------------------- |
| **`QLCDNumber::Hex`** | 十六进制显示（0-9, A-F） | `lcd->setMode(QLCDNumber::Hex); lcd->display(255); // 显示 "FF"` |
| **`QLCDNumber::Dec`** | 十进制显示（0-9）        | `lcd->setMode(QLCDNumber::Dec); lcd->display(123); // 显示 "123"` |
| **`QLCDNumber::Oct`** | 八进制显示（0-7）        | `lcd->setMode(QLCDNumber::Oct); lcd->display(10); // 显示 "12"` |
| **`QLCDNumber::Bin`** | 二进制显示（0-1）        | `lcd->setMode(QLCDNumber::Bin); lcd->display(5); // 显示 "101"` |

### QLCDNumber::SegmentStyle - 段样式

| 枚举值                    | 描述                   | 视觉效果         |
| :------------------------ | :--------------------- | :--------------- |
| **`QLCDNumber::Outline`** | 轮廓样式，段显示为轮廓 | 空心数字         |
| **`QLCDNumber::Filled`**  | 填充样式，段被填充     | 实心数字（默认） |
| **`QLCDNumber::Flat`**    | 扁平样式，平面显示     | 扁平化设计       |

---

## 综合使用示例

```cpp
#include <QLCDNumber>
#include <QVBoxLayout>
#include <QHBoxLayout>
#include <QPushButton>
#include <QWidget>

class LCDDemo : public QWidget {
    Q_OBJECT
public:
    LCDDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setupUI();
    }

private:
    void setupUI() {
        // 创建QLCDNumber控件
        lcd = new QLCDNumber(10, this); // 10位显示
        lcd->setSegmentStyle(QLCDNumber::Filled);
        lcd->setSmallDecimalPoint(true);

        // 创建控制按钮
        QPushButton *btnDec = new QPushButton("十进制", this);
        QPushButton *btnHex = new QPushButton("十六进制", this);
        QPushButton *btnOct = new QPushButton("八进制", this);
        QPushButton *btnBin = new QPushButton("二进制", this);
        QPushButton *btnNumber = new QPushButton("显示数字", this);
        QPushButton *btnText = new QPushButton("显示文本", this);

        // 连接信号槽
        connect(btnDec, &QPushButton::clicked, this, [this]() {
            lcd->setMode(QLCDNumber::Dec);
            lcd->display(1234567890);
        });

        connect(btnHex, &QPushButton::clicked, this, [this]() {
            lcd->setMode(QLCDNumber::Hex);
            lcd->display(0xABCDEF);
        });

        connect(btnOct, &QPushButton::clicked, this, [this]() {
            lcd->setMode(QLCDNumber::Oct);
            lcd->display(0777);
        });

        connect(btnBin, &QPushButton::clicked, this, [this]() {
            lcd->setMode(QLCDNumber::Bin);
            lcd->display(0b10101010);
        });

        connect(btnNumber, &QPushButton::clicked, this, [this]() {
            lcd->display(3.14159); // 显示浮点数，会自动显示小数点
        });

        connect(btnText, &QPushButton::clicked, this, [this]() {
            lcd->display("HELLO"); // 显示文本，非数字字符会显示为特殊符号
        });

        // 布局
        QVBoxLayout *mainLayout = new QVBoxLayout(this);
        QHBoxLayout *btnLayout1 = new QHBoxLayout();
        QHBoxLayout *btnLayout2 = new QHBoxLayout();

        mainLayout->addWidget(lcd);
        
        btnLayout1->addWidget(btnDec);
        btnLayout1->addWidget(btnHex);
        btnLayout1->addWidget(btnOct);
        btnLayout1->addWidget(btnBin);
        
        btnLayout2->addWidget(btnNumber);
        btnLayout2->addWidget(btnText);
        
        mainLayout->addLayout(btnLayout1);
        mainLayout->addLayout(btnLayout2);

        // 初始显示
        lcd->display("READY");
    }

private:
    QLCDNumber *lcd;
};
```

## 特殊字符显示

QLCDNumber 可以显示一些特殊字符，当使用 `display(const QString &s)` 时：

| 字符      | 显示效果     | 说明            |
| :-------- | :----------- | :-------------- |
| **`0-9`** | 相应数字     | 正常数字显示    |
| **`A-F`** | 十六进制字母 | 在Hex模式下有效 |
| **`-`**   | 负号         | 显示为负号      |
| **`.`**   | 小数点       | 显示小数点      |
| **` `**   | 空格         | 显示为空        |
| **`E`**   | 错误符号     | 显示错误指示    |
| **`H`**   | 特殊符号     | 显示特定图案    |
| **其他**  | 特殊符号     | 显示为各种图案  |

```cpp
// 特殊字符显示示例
lcd->display("123-456");    // 显示数字和负号
lcd->display("ERR  ");      // 显示错误信息
lcd->display("3.14");       // 显示带小数点的数字
```

## 注意事项

1. **位数限制**：必须使用 `setDigitCount()` 设置足够的位数，否则会截断显示
2. **溢出检查**：使用 `checkOverflow()` 可以预先检查数值是否会超出显示范围
3. **模式切换**：切换显示模式会影响数值的显示方式
4. **小数显示**：`setSmallDecimalPoint(true)` 可以让小数点更紧凑

这些成员函数使得 QLCDNumber 成为一个功能丰富的数码管显示控件，非常适合显示各种数值和简单的文本信息。