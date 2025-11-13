# 网格布局QGridLayout

## 特点

- 控件按 **行（Row）和列（Column）** 排列，类似表格。
- 适用于需要精确控制位置的复杂布局，如计算器、仪表盘等。



## 核心布局管理函数

### 添加项目函数

将部件添加到网格的指定行和列，可设置对齐方式。

```cpp
void addWidget(QWidget *widget, int row, int column, Qt::Alignment alignment = Qt::Alignment())
```



将部件添加到网格，并可跨越多行多列。

```cpp
void addWidget(QWidget *widget, int row, int column, int rowSpan, int columnSpan, Qt::Alignment alignment = Qt::Alignment())
```

将子布局添加到网格的指定位置。

```cpp
void addLayout(QLayout *layout, int row, int column, Qt::Alignment alignment = Qt::Alignment())
```

将子布局添加到网格并可跨越多行多列。

```cpp
void addLayout(QLayout *layout, int row, int column, int rowSpan, int columnSpan, Qt::Alignment alignment = Qt::Alignment())
//子布局起始行位置（从0开始） 子布局起始列位置（从0开始） 子布局跨越的行数 子布局跨越的列数 对齐方式（可选参数，有默认值）
```

### 项目获取函数

返回指定行和列位置的部件。

```cpp
QWidget* itemAtPosition(int row, int column) const
```





# 没看

## 行列配置函数

### 3. 行配置
- **`void setRowMinimumHeight(int row, int minSize)`**
  设置指定行的最小高度。
- **`int rowMinimumHeight(int row) const`**
  获取指定行的最小高度。
- **`void setRowStretch(int row, int stretch)`**
  设置指定行的拉伸因子。
- **`int rowStretch(int row) const`**
  获取指定行的拉伸因子。

### 4. 列配置
- **`void setColumnMinimumWidth(int column, int minSize)`**
  设置指定列的最小宽度。
- **`int columnMinimumWidth(int column) const`**
  获取指定列的最小宽度。
- **`void setColumnStretch(int column, int stretch)`**
  设置指定列的拉伸因子。
- **`int columnStretch(int column) const`**
  获取指定列的拉伸因子。

## 📏 间距设置函数

- **`void setHorizontalSpacing(int spacing)`**
  设置水平间距（像素）。
- **`int horizontalSpacing() const`**
  获取水平间距。
- **`void setVerticalSpacing(int spacing)`**
  设置垂直间距（像素）。
- **`int verticalSpacing() const`**
  获取垂直间距。

## 🔍 布局信息函数

- **`int rowCount() const`**
  返回网格中的行数。
- **`int columnCount() const`**
  返回网格中的列数。
- **`QRect cellRect(int row, int column) const`**
  返回指定单元格的几何矩形。

## 🎯 原点角落设置函数

- **`void setOriginCorner(Qt::Corner corner)`**
  设置布局的原点角落（影响添加项目的方向）。
- **`Qt::Corner originCorner() const`**
  获取当前设置的原点角落。

## 📝 代码示例

```cpp
// 创建网格布局
QGridLayout *gridLayout = new QGridLayout;

// 添加部件到第0行第0列
gridLayout->addWidget(button1, 0, 0);

// 添加跨2行2列的部件
gridLayout->addWidget(bigWidget, 1, 1, 2, 2);

// 设置行列属性
gridLayout->setRowStretch(0, 1);      // 第0行拉伸因子为1
gridLayout->setColumnMinimumWidth(1, 100); // 第1列最小宽度100像素

// 设置间距
gridLayout->setHorizontalSpacing(10);
gridLayout->setVerticalSpacing(5);

// 获取信息
int rows = gridLayout->rowCount();    // 获取行数
QRect rect = gridLayout->cellRect(1, 1); // 获取单元格矩形
```

这些函数提供了对网格布局的精细控制，允许你创建复杂而灵活的界面布局。