# C++ 中的 `virtual` 关键字详解

`virtual` 是 C++ 中实现多态性的核心关键字，主要用于处理继承关系中的函数和类。它在两个主要场景中使用：**虚函数**和**虚继承**。

## 1. 虚函数 (Virtual Functions)

### 目的：
实现运行时多态（动态绑定），允许通过基类指针或引用调用派生类的重写函数。

### 基本用法：
```cpp
class Base {
public:
    virtual void show() {  // 声明为虚函数
        std::cout << "Base class\n";
    }
};

class Derived : public Base {
public:
    void show() override {  // 重写虚函数（override可选但推荐）
        std::cout << "Derived class\n";
    }
};

int main() {
    Base* b = new Derived();
    b->show();  // 输出 "Derived class"（多态行为）
    delete b;
    return 0;
}
```

### 关键特性：
1. **动态绑定**：函数调用在运行时决定
2. **必须通过指针或引用**：直接对象调用不会触发多态
3. **可被重写**：派生类可提供自己的实现
4. **虚函数表**：编译器为每个含虚函数的类创建虚表（vtable）

### 重要规则：
- 构造函数不能是虚函数
- 析构函数**应该**是虚函数（当类可能被继承时）
- 静态成员函数不能是虚函数
- 虚函数可以有默认参数（但不建议使用）

## 2. 虚析构函数 (Virtual Destructors)

### 为什么需要？
确保通过基类指针删除派生类对象时，正确调用派生类析构函数。

```cpp
class Base {
public:
    virtual ~Base() {  // 虚析构函数
        std::cout << "Base destroyed\n";
    }
};

class Derived : public Base {
public:
    ~Derived() override {
        std::cout << "Derived destroyed\n";
    }
};

int main() {
    Base* b = new Derived();
    delete b;  // 正确调用顺序：~Derived() → ~Base()
    return 0;
}
```

**输出：**
```
Derived destroyed
Base destroyed
```

## 3. 纯虚函数与抽象类

### 纯虚函数：
```cpp
virtual void function() = 0;  // 纯虚函数声明
```

### 抽象类：
- 包含至少一个纯虚函数的类
- 不能实例化
- 强制派生类实现接口

```cpp
class Shape {  // 抽象类
public:
    virtual double area() const = 0;  // 纯虚函数
    virtual ~Shape() = default;
};

class Circle : public Shape {
public:
    double area() const override {  // 必须实现
        return 3.14 * radius * radius;
    }
private:
    double radius = 1.0;
};
```

## 4. 虚继承 (Virtual Inheritance)

### 解决菱形继承问题：
```
    A
   / \
  B   C
   \ /
    D
```

### 用法：
```cpp
class A {
public:
    int value;
};

class B : virtual public A {};  // 虚继承
class C : virtual public A {};  // 虚继承

class D : public B, public C {
public:
    void setValue(int v) {
        value = v;  // 无歧义，只有一个共享副本
    }
};
```

### 关键点：
1. 虚基类子对象由最派生类直接初始化
2. 使用虚基类表（vtable）管理共享实例
3. 构造函数调用顺序不同：虚基类优先

## 5. 虚函数的工作原理（底层机制）

### 虚函数表（vtable）：
- 每个含虚函数的类有一个 vtable
- 存储指向实际函数实现的指针
- 对象包含隐藏的 vptr（虚表指针）

### 内存布局示例：
```
[对象内存布局]
+----------------+
| vptr           | → 指向 vtable
| 成员变量...    |
+----------------+

[vtable]
+----------------+
| &Base::~Base   |
| &Base::func1   |
| &Derived::func2|
+----------------+
```

## 6. `override` 和 `final` 关键字（C++11）

### override：
显式标记重写的虚函数，提高代码可读性，编译器会检查是否确实重写。

```cpp
class Derived : public Base {
public:
    void show() override {  // 明确表示重写
        // ...
    }
};
```

### final：
禁止进一步重写或继承。

```cpp
class Base {
public:
    virtual void func() final {}  // 禁止派生类重写
};

class Derived final : public Base {  // 禁止进一步继承
    // ...
};
```

## 何时使用 virtual？

| 场景               | 推荐使用 virtual |
| ------------------ | ---------------- |
| 需要多态行为       | ✓                |
| 基类析构函数       | ✓                |
| 接口定义（抽象类） | ✓                |
| 解决菱形继承       | ✓（虚继承）      |
| 普通成员函数       | ✗                |
| 构造函数           | ✗                |
| 静态成员函数       | ✗                |

## 性能考虑
- 虚函数调用比普通函数调用稍慢（多一次指针解引用）
- 每个含虚函数的对象增加一个指针大小（vptr）
- 虚继承会增加额外开销（虚基类表）
- 在性能关键代码中谨慎使用

## 总结
`virtual` 关键字是 C++ 多态性的基石：
1. **虚函数**：实现运行时多态
2. **虚析构函数**：确保正确销毁对象
3. **纯虚函数**：定义接口规范
4. **虚继承**：解决多重继承中的共享问题

正确理解和使用 `virtual` 是掌握 C++ 面向对象编程的关键，它提供了强大的灵活性，但也需要谨慎使用以避免性能问题和设计复杂性。