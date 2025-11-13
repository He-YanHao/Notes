# Lambda表达式



# Lambda 表达式详细介绍

Lambda 表达式是 C++11 引入的一种重要特性，它允许我们编写内联的匿名函数，大大提高了代码的简洁性和表现力。在 Qt 编程中，Lambda 表达式特别有用，尤其是在连接信号槽和处理回调时。

## 基本语法结构

Lambda 表达式的基本语法如下：

```cpp
[capture_list](parameter_list) mutable -> return_type {
    // 函数体
}
```

## 组成部分详解

### 1. 捕获列表 (Capture List) `[ ]`

捕获列表定义了 Lambda 表达式如何访问其外部作用域中的变量：

- **`[]`**：不捕获任何外部变量
- **`[=]`**：以值的方式捕获所有外部变量
- **`[&]`**：以引用的方式捕获所有外部变量
- **`[var]`**：以值的方式捕获特定变量 `var`
- **`[&var]`**：以引用的方式捕获特定变量 `var`
- **`[=, &var]`**：以值的方式捕获所有外部变量，但以引用的方式捕获 `var`
- **`[&, var]`**：以引用的方式捕获所有外部变量，但以值的方式捕获 `var`
- **`[this]`**：捕获当前类的 `this` 指针，可以访问类的成员

### 2. 参数列表 `( )`

与普通函数的参数列表类似，定义 Lambda 表达式接受的参数：

```cpp
[](int x, int y) { return x + y; } // 接受两个int参数
```

### 3. 可变规范 `mutable` (可选)

允许修改按值捕获的变量（默认情况下，按值捕获的变量是只读的）：

```cpp
[int x = 0]() mutable { return ++x; }; // 可以修改x
```

### 4. 返回类型 `-> type` (可选)

显式指定返回类型。如果省略，编译器会自动推导返回类型：

```cpp
[]() -> double { return 3.14; }; // 显式指定返回double
```

### 5. 函数体 `{ }`

包含 Lambda 表达式要执行的代码。

## 在 Qt 中的常见用法

### 1. 信号槽连接

```cpp
QPushButton *button = new QPushButton("Click me");
QObject::connect(button, &QPushButton::clicked, []() {
    qDebug() << "Button clicked!";
});
```

### 2. 使用捕获列表访问外部变量

```cpp
int counter = 0;
QPushButton *button = new QPushButton("Click me");
QObject::connect(button, &QPushButton::clicked, [&counter]() {
    qDebug() << "Clicked" << ++counter << "times";
});
```

### 3. 在算法中使用

```cpp
QList<int> numbers = {1, 2, 3, 4, 5};
// 使用Lambda过滤偶数
QList<int> evens = numbers.filtered([](int n) { return n % 2 == 0; });
```

## 高级用法

### 1. 泛型 Lambda (C++14)

```cpp
auto print = [](auto value) {
    qDebug() << value;
};
print(42);        // 输出: 42
print("Hello");   // 输出: Hello
```

### 2. 初始化捕获 (C++14)

```cpp
int x = 10;
auto lambda = [value = x + 5]() { 
    return value; // value被初始化为15
};
```

### 3. constexpr Lambda (C++17)

```cpp
constexpr auto square = [](int n) { return n * n; };
static_assert(square(5) == 25); // 编译时计算
```

## 在 Qt 中的注意事项

1. **对象生命周期**：当使用引用捕获 `[&]` 时，要确保被引用的对象在 Lambda 被执行时仍然存在。

2. **线程安全**：如果 Lambda 可能在多个线程中执行，需要确保对共享数据的访问是线程安全的。

3. **事件循环**：在 Lambda 中执行长时间操作可能会阻塞 Qt 的事件循环，考虑使用 `QtConcurrent` 或工作线程。

4. **内存管理**：如果 Lambda 捕获了指向 QObject 的指针，要注意 Qt 的对象父子关系内存管理机制。

## 实际示例

```cpp
// 创建一个对话框，使用Lambda处理按钮点击
void showDialog() {
    QDialog dialog;
    QVBoxLayout *layout = new QVBoxLayout(&dialog);
    
    QLineEdit *input = new QLineEdit;
    QPushButton *okButton = new QPushButton("OK");
    QPushButton *cancelButton = new QPushButton("Cancel");
    
    layout->addWidget(input);
    layout->addWidget(okButton);
    layout->addWidget(cancelButton);
    
    // 使用Lambda处理按钮点击
    QObject::connect(okButton, &QPushButton::clicked, [&dialog, input]() {
        qDebug() << "User entered:" << input->text();
        dialog.accept();
    });
    
    QObject::connect(cancelButton, &QPushButton::clicked, [&dialog]() {
        dialog.reject();
    });
    
    dialog.exec();
}
```

## 总结

Lambda 表达式是现代 C++ 编程中极其强大的工具，特别是在 Qt 开发中：

1. **简洁性**：减少了需要命名的函数数量，使代码更紧凑
2. **上下文感知**：通过捕获列表可以直接访问外部变量
3. **灵活性**：可以轻松创建即用即弃的函数对象
4. **与现代C++特性集成**：与自动类型推导、模板等特性完美结合

掌握 Lambda 表达式可以显著提高 Qt 代码的质量和开发效率，特别是在处理信号槽连接和回调函数时。