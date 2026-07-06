# 模板Template

## 基本概念

### 模板的作用
- 编写与数据类型无关的通用代码
- 避免为不同数据类型重复编写相似代码
- 在编译时生成特定类型的代码

### 模板类型
- 函数模板：生成通用函数
- 类模板：生成通用类

## 函数模板

### 基本语法
```cpp
template <typename T>
//template开始定义一个模板
//typename申请一个类，先用T代替，具体是哪个编译时决定。
//typename可以用class代替，两者在模板中基本等价。
//T仅仅是占位符，任何合法命名都行，但一般用T，或其他大写字母。
T max(T a, T b) {
//模板名称叫max
    return (a > b) ? a : b;
}
```

### 多类型参数
```cpp
template <typename T1, typename T2>
void printPair(T1 first, T2 second) {
    std::cout << "(" << first << ", " << second << ")" << std::endl;
}
// 使用
printPair(42, "hello");
printPair(3.14, true);
```

## 类模板

### 基本语法
```cpp
template <typename T>
class Box {
public:
    Box(T content) : content(content)
    {
        
    }
    T getContent() const {
        return content;
    }
    
private:
    T content;
};

int main() {
    Box<int> intBox(42);
    Box<std::string> strBox("Hello Templates");
    Box<double> doubleBox(3.14159);
    
    std::cout << intBox.getContent() << std::endl;
    std::cout << strBox.getContent() << std::endl;
    std::cout << doubleBox.getContent() << std::endl;
    
    return 0;
}
```

### 模板类继承
```cpp
template <typename T>
class SpecialBox : public Box<T> {
public:
    SpecialBox(T content, int special) 
        : Box<T>(content), specialValue(special) {}
    
    void display() {
        std::cout << "Content: " << this->getContent()
                  << ", Special: " << specialValue << std::endl;
    }
    
private:
    int specialValue;
};
```



## `.hpp`

类模板文件需要将后缀改为`.hpp`。





# 还没看























## 高级模板特性

### 1. 非类型模板参数
```cpp
template <typename T, int Size>
class FixedArray {
public:
    T& operator[](int index) {
        if (index < 0 || index >= Size)
            throw std::out_of_range("Index out of range");
        return data[index];
    }
    
private:
    T data[Size];
};

// 使用
FixedArray<double, 10> array;
```

### 2. 模板特化
```cpp
// 通用模板
template <typename T>
class TypeInfo {
public:
    static std::string name() {
        return "Unknown";
    }
};

// int类型的特化
template <>
class TypeInfo<int> {
public:
    static std::string name() {
        return "int";
    }
};

// 使用
std::cout << TypeInfo<double>::name(); // 输出 "Unknown"
std::cout << TypeInfo<int>::name();    // 输出 "int"
```

### 3. 可变参数模板 (C++11)
```cpp
// 递归终止函数
void print() {
    std::cout << std::endl;
}

// 可变参数模板
template <typename T, typename... Args>
void print(T first, Args... args) {
    std::cout << first << " ";
    print(args...); // 递归调用
}

// 使用
print(1, 2.5, "hello", 'a'); // 输出: 1 2.5 hello a
```

### 4. 概念约束 (C++20)
```cpp
template <typename T>
concept Numeric = std::integral<T> || std::floating_point<T>;

template <Numeric T>
T add(T a, T b) {
    return a + b;
}

// 使用
add(5, 10);     // 合法
add(3.14, 2.5); // 合法
add("a", "b");  // 编译错误：不满足Numeric概念
```

## 五、模板使用最佳实践

1. **模板定义在头文件中**：模板实现通常放在头文件中，因为编译器需要在编译时看到完整定义

2. **使用类型别名简化复杂模板**
   ```cpp
   template <typename T>
   using StringMap = std::map<std::string, T>;
   
   StringMap<int> nameToAge; // 等价于 std::map<std::string, int>
   ```

3. **模板元编程示例：编译时计算阶乘**
   ```cpp
   template <int N>
   struct Factorial {
       static const int value = N * Factorial<N - 1>::value;
   };
   
   template <>
   struct Factorial<0> {
       static const int value = 1;
   };
   
   // 使用
   std::cout << Factorial<5>::value; // 输出120，在编译时计算
   ```

4. **SFINAE（替换失败不是错误）**
   ```cpp
   template <typename T>
   auto print(T value) -> decltype(std::cout << value, void()) {
       std::cout << value << std::endl;
   }
   
   template <typename T>
   void print(...) {
       std::cout << "Cannot print this type" << std::endl;
   }
   
   // 使用
   print(42);       // 调用第一个版本
   print(std::vector<int>()); // 调用第二个版本
   ```

## 六、标准库中的模板应用

1. **容器**
   ```cpp
   std::vector<int> intVector;
   std::list<std::string> stringList;
   std::map<int, double> idMap;
   ```

2. **算法**
   ```cpp
   std::vector<int> numbers = {5, 2, 8, 1, 9};
   std::sort(numbers.begin(), numbers.end());
   
   auto it = std::find(numbers.begin(), numbers.end(), 8);
   ```

3. **智能指针**
   ```cpp
   std::unique_ptr<MyClass> ptr = std::make_unique<MyClass>();
   std::shared_ptr<Resource> shared = std::make_shared<Resource>();
   ```

## 七、模板的优缺点

### 优点：
- 代码复用性高
- 类型安全（优于宏和void指针）
- 性能好（编译时实例化）
- 支持高度泛化的编程

### 缺点：
- 编译时间增加
- 错误信息难以理解
- 代码膨胀（每个实例化产生独立代码）
- 头文件实现导致依赖增加

## 总结

C++模板是强大的泛型编程工具：
1. 使用`template <typename T>`声明模板
2. 函数模板用于通用函数，类模板用于通用类
3. 支持特化、可变参数、概念约束等高级特性
4. 广泛应用于标准库容器、算法和智能指针
5. 遵循"定义在头文件中"的最佳实践

掌握模板技术可以显著提高C++代码的复用性、灵活性和性能，是现代C++开发的核心技能之一。