# C++ 中 string 类的指针使用详解

在 C++ 中，`std::string` 是一个类类型，我们可以使用指针来操作它。以下是关于 string 类指针的详细介绍：

## 1. 基本指针操作

### 1.1 指向 string 对象的指针

```cpp
std::string str = "Hello";
std::string* pStr = &str;  // 获取 string 对象的地址

// 通过指针访问成员函数
std::cout << pStr->length();  // 输出 5
std::cout << (*pStr).size();  // 等价写法
```

### 1.2 动态分配 string 对象

```cpp
std::string* pStr = new std::string("Dynamic");
std::cout << *pStr;  // 输出 "Dynamic"

// 必须记得释放内存
delete pStr;
```

## 2. 访问 string 内部数据的指针

### 2.1 c_str() 和 data()

```cpp
std::string str = "World";
const char* cstr = str.c_str();  // 获取 C 风格字符串指针
const char* data = str.data();   // C++17 起与 c_str() 相同

// 注意：这些指针在字符串修改后可能失效
```

### 2.2 使用指针遍历字符串

```cpp
std::string str = "Hello";
const char* p = str.c_str();

while (*p != '\0') {
    std::cout << *p;
    p++;
}
```

## 3. 指针数组与字符串

### 3.1 string 指针数组

```cpp
std::string strings[] = {"Apple", "Banana", "Cherry"};
std::string* pArr = strings;  // 指向数组第一个元素

for (int i = 0; i < 3; i++) {
    std::cout << pArr[i] << " ";  // 输出所有字符串
}
```

### 3.2 动态 string 数组

```cpp
std::string* pStrings = new std::string[3];
pStrings[0] = "Red";
pStrings[1] = "Green";
pStrings[2] = "Blue";

// 使用后释放
delete[] pStrings;
```

## 4. 智能指针管理 string

现代 C++ 推荐使用智能指针管理动态分配的 string：

```cpp
#include <memory>

// unique_ptr 独占所有权
std::unique_ptr<std::string> uPtr = std::make_unique<std::string>("Unique");

// shared_ptr 共享所有权
std::shared_ptr<std::string> sPtr = std::make_shared<std::string>("Shared");
```

## 5. 注意事项

1. **生命周期管理**：确保指针指向的 string 对象在使用期间有效
2. **const 正确性**：使用 `const std::string*` 表示不修改指向的字符串
3. **避免裸指针**：优先使用智能指针或引用
4. **C 字符串转换**：`c_str()` 返回的指针在 string 修改后可能失效
5. **多字节字符串**：处理 Unicode 时需注意编码问题

## 6. 实际应用示例

```cpp
#include <iostream>
#include <string>
#include <vector>

void printStrings(const std::string* arr, size_t size) {
    for (size_t i = 0; i < size; i++) {
        std::cout << arr[i] << "\n";
    }
}

int main() {
    std::vector<std::string> words = {"Cat", "Dog", "Elephant"};
    
    // 获取 vector 底层数组指针
    printStrings(words.data(), words.size());
    
    // 动态创建字符串数组
    std::unique_ptr<std::string[]> pWords(new std::string[2]);
    pWords[0] = "Hello";
    pWords[1] = "World";
    
    return 0;
}
```

string 类的指针操作提供了灵活性，但在现代 C++ 中，应优先考虑使用引用、智能指针和标准库容器，以减少手动内存管理的风险。