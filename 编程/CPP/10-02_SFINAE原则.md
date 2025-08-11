## SFINAE原则 Substitution Failure Is Not An Error)

### 基本概念

SFINAE 是模板元编程中的核心原则，全称"替换失败不是错误"。当模板参数替换导致无效代码时，编译器不会报错，而是简单地从重载集中丢弃该候选。

### 工作原理

```cpp
template <typename T>
auto foo(T) -> decltype(T().method(), void())
{	// 检测T是否有method()
    std::cout << "Has method\n";
}

void foo(...) { // 兜底版本
    std::cout << "No method\n";
}

struct A { void method() {} };
struct B {};

foo(A{}); // 输出 "Has method"
foo(B{}); // 输出 "No method"
```

**分解说明：**

`auto foo(T)`

- `auto`: 使用返回类型后置语法
- `foo`: 函数名
- `(T)`: 接受一个类型为 `T` 的参数

`-> decltype(...)`

- 箭头符号表示后置返回类型
- `decltype`: 类型推导关键字，推导表达式的类型

`T().method()`

- `T()`: 创建 `T` 类型的**临时对象**
- `.method()`: 尝试调用该对象的 `method` 成员函数
- **核心检测机制**：
  - 如果 `T` 有可访问的 `method` 成员函数 → 表达式有效
  - 如果 `T` 没有 `method` 成员函数 → 表达式无效

`, void()`

- 逗号运算符：计算多个表达式，返回最后一个表达式的类型
- `void()`: 确保最终返回类型为 `void`
- **表达式组合**：
  - 首先计算 `T().method()`
  - 然后计算 `void()`
  - 整体类型为 `void`



### SFINAE 机制工作原理

有效调用场景（`T = A`）



```cpp
struct A { void method() {} };
foo(A{});
```

1. 编译器尝试实例化模板
2. `T().method()` 有效（因为 `A` 有 `method`）
3. 返回类型推导为 `void`
4. 函数成功实例化并调用

无效调用场景（`T = B`）



```cpp
struct B {}; // 无 method 成员
foo(B{});
```

1. 编译器尝试实例化模板
2. `T().method()` 无效（编译错误）
3. 根据 SFINAE 原则，**不视为错误**
4. 该模板被从重载集中移除
5. 编译器选择后备的 `foo(...)` 版本





### 现代应用（C++11/14）

```cpp
// 使用std::enable_if
template<typename T,
         typename = std::enable_if_t<std::is_integral_v<T>>>
void process(T value) {
    // 仅对整数类型有效
}
```

### 典型应用场景

- 函数重载选择
- 类型特征检测
- 模板特化控制