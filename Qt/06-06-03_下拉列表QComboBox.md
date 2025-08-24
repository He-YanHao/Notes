# 下拉列表QComboBox

QComboBox 是 Qt 中一个非常重要的下拉列表框控件，它允许用户从预定义的选项列表中选择一个或多个值。以下是 QComboBox 类本身（非继承）的成员函数的详细介绍。

## 构造函数&析构函数

### 构造函数

```cpp
QComboBox *ComboBox = new QComboBox(/*父对象指针*/); // 创建组合框
```

构造函数，创建一个组合框控件。

## 核心成员函数

### 添加成员

```cpp
void addItem(const QString &text, const QVariant &userData)
```

添加一个文本项目，可附带用户数据。

案例：

```cpp
ComboBox->addItem("选项1", 1001);
```



### 获取数据

`currentData()`函数可以获取当前选项数据，以`QVariant`类型的形式返回。

可以再使用`toInt()`转换为数字。

```cpp
ComboBox->currentData().toInt();
```



```cpp

```





# QComboBox 类成员函数介绍

# QComboBox 类信号介绍

| 信号声明                                      | 功能介绍                             | 连接案例                                                     |
| --------------------------------------------- | ------------------------------------ | ------------------------------------------------------------ |
| void currentIndexChanged(int index)           | 当前选中项索引改变时发出             | connect(comboBox, &QComboBox::currentIndexChanged, this, &MyClass::onIndexChanged); |
| void currentIndexChanged(const QString &text) | 当前选中项文本改变时发出             | connect(comboBox, &QComboBox::currentIndexChanged, this, &MyClass::onTextChanged); |
| void currentTextChanged(const QString &text)  | 当前文本改变时发出（仅可编辑组合框） | connect(comboBox, &QComboBox::currentTextChanged, this, &MyClass::onTextChanged); |
| void editTextChanged(const QString &text)     | 编辑文本改变时发出（仅可编辑组合框） | connect(comboBox, &QComboBox::editTextChanged, this, &MyClass::onEditTextChanged); |
| void activated(int index)                     | 用户激活（选择）某项时发出           | connect(comboBox, &QComboBox::activated, this, &MyClass::onItemActivated); |
| void activated(const QString &text)           | 用户激活（选择）某项时发出           | connect(comboBox, &QComboBox::activated, this, &MyClass::onItemActivated); |
| void highlighted(int index)                   | 用户高亮某项时发出                   | connect(comboBox, &QComboBox::highlighted, this, &MyClass::onItemHighlighted); |
| void highlighted(const QString &text)         | 用户高亮某项时发出                   | connect(comboBox, &QComboBox::highlighted, this, &MyClass::onItemHighlighted); |

# QComboBox 类枚举值介绍

| 枚举类型         | 值                            | 功能介绍                   | 使用案例                                                     |
| ---------------- | ----------------------------- | -------------------------- | ------------------------------------------------------------ |
| InsertPolicy     | InsertAtTop                   | 在列表顶部插入新项         | comboBox->setInsertPolicy(QComboBox::InsertAtTop);           |
| InsertPolicy     | InsertAtCurrent               | 替换当前项                 | comboBox->setInsertPolicy(QComboBox::InsertAtCurrent);       |
| InsertPolicy     | InsertAtBottom                | 在列表底部插入新项         | comboBox->setInsertPolicy(QComboBox::InsertAtBottom);        |
| InsertPolicy     | InsertAfterCurrent            | 在当前项后插入新项         | comboBox->setInsertPolicy(QComboBox::InsertAfterCurrent);    |
| InsertPolicy     | InsertBeforeCurrent           | 在当前项前插入新项         | comboBox->setInsertPolicy(QComboBox::InsertBeforeCurrent);   |
| InsertPolicy     | InsertAlphabetically          | 按字母顺序插入新项         | comboBox->setInsertPolicy(QComboBox::InsertAlphabetically);  |
| SizeAdjustPolicy | AdjustToContents              | 根据内容调整大小           | comboBox->setSizeAdjustPolicy(QComboBox::AdjustToContents);  |
| SizeAdjustPolicy | AdjustToContentsOnFirstShow   | 首次显示时根据内容调整大小 | comboBox->setSizeAdjustPolicy(QComboBox::AdjustToContentsOnFirstShow); |
| SizeAdjustPolicy | AdjustToMinimumContentsLength | 根据最小内容长度调整大小   | comboBox->setSizeAdjustPolicy(QComboBox::AdjustToMinimumContentsLength); |
| SizeAdjustPolicy | AdjustToMinimumContentsLengthWithIcon | 考虑图标的最小内容长度调整大小 | comboBox->setSizeAdjustPolicy(QComboBox::AdjustToMinimumContentsLengthWithIcon); |。