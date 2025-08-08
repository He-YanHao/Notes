# string

## 基本特性与优势

需要头文件`string`

`std::string` 是 C++ 标准库中用于处理文本数据的核心类型，它解决了 C 风格字符数组的诸多痛点，提供了安全、高效的字符串操作能力。

| 特性         | 说明                           |
| ------------ | ------------------------------ |
| 动态内存管理 | 自动调整存储空间大小           |
| 值语义       | 支持拷贝和赋值（深拷贝）       |
| RAII 原则    | 自动释放内存                   |
| 丰富的接口   | 提供 100+ 成员函数             |
| Unicode 支持 | 通过 `wstring`、`u16string` 等 |

## 关键操作与成员函数

### 内容访问
```cpp
std::string str = "Hello World";

char first = str[0];      // 'H'
//普通访问 无边界检查

char c = str.at(1);  // 'e'，越界时抛出 std::out_of_range
//安全访问（带边界检查）

char last = str.back();
//back()返回字符串最后一个字符

char first = str.front();
//front()返回字符串第一个字符

const char* cstr = str.c_str();
const char* data = str.data(); // C++17 起保证以空字符结尾
//都返回指向字符串内部字符数组的指针
// C 风格访问（只读）
```

### 大小与容量
```cpp
str.size();
//当前字符数 (同 length())
str.empty();
//是否为空
str.capacity();
//当前分配的内存大小
str.reserve(100);
//预分配内存，避免重复分配
str.shrink_to_fit();
//释放多余内存 (C++11)
```

### 修改操作
```cpp
// 追加
str.append(" C++");
str.push_back('!');
str += " 2023";

// 插入
str.insert(6, "Awesome "); // "Hello Awesome World! 2023"

// 删除
str.erase(5, 8);     // 删除位置5开始的8个字符
str.clear();         // 清空内容

// 替换
str.replace(0, 5, "Hi"); // "Hi Awesome World! 2023"
```

### 字符串操作
```cpp
// 子串
std::string sub = str.substr(3, 7); // "Awesome"

// 查找
size_t pos = str.find("World"); // 返回首次出现位置
pos = str.rfind('o');           // 从后向前查找

// 比较
int result = str.compare("Hi Awesome C++!");
```

### 检测

```c
// C++20 新特性
//检测开头
if (str.starts_with("Hi"))
{
    ...
}

//检测结尾
if (str.ends_with("2023"))
{
    ...
}

//检测是否包含
if (str.contains("Awesome"))
{
    ...
}
```

## 转换与编码处理

### 数值转换
```cpp
// 字符串到数值
int i = std::stoi("42");
double d = std::stod("3.14159");

// 数值到字符串
std::string num_str = std::to_string(255); // "255"

// C++17 更安全的版本
std::from_chars_result res = std::from_chars(str.data(), str.data() + str.size(), value);
```

### 编码转换 (C++11)
```cpp
// UTF-8 到 UTF-16
std::string utf8 = u8"こんにちは";
std::wstring_convert<std::codecvt_utf8_utf16<char16_t>, char16_t> conv;
std::u16string utf16 = conv.from_bytes(utf8);

// UTF-16 到 UTF-8
std::string utf8_back = conv.to_bytes(utf16);
```

# 还没看



### 1. 性能优化技巧
```cpp
// 高效拼接
std::ostringstream oss;
oss << "Part1: " << value1 << ", Part2: " << value2;
std::string result = oss.str();

// 预留空间减少分配
std::vector<std::string> words;
std::string combined;
combined.reserve(1000); // 预估总大小
for (const auto& word : words) {
    combined += word;
}

// 使用移动避免拷贝
void processString(std::string&& str) {
    // 高效处理
}
processString(std::move(large_str));
```

### 2. 安全注意事项
```cpp
// 避免悬挂指针
const char* dangerous = someString.c_str();
someString += " modification"; // 可能使dangerous无效

// 安全做法
std::string safe_copy = someString.substr(); // 创建副本
const char* safe_ptr = safe_copy.c_str();
```

### 3. 现代 C++ 模式
```cpp
// 使用字符串视图作为参数 (C++17)
void processText(std::string_view text) {
    // 无需复制
}

// 结构化绑定 (C++17)
std::string data = "key:value";
if (auto pos = data.find(':'); pos != std::string::npos) {
    auto [key, value] = std::pair(data.substr(0, pos), data.substr(pos + 1));
}

// 格式化字符串 (C++20)
std::string message = std::format("Hello {}, your score is {:.2f}", name, score);
```

## 七、应用场景示例

### 1. 配置文件解析
```cpp
std::map<std::string, std::string> parseConfig(const std::string& content) {
    std::map<std::string, std::string> config;
    std::istringstream iss(content);
    std::string line;
    
    while (std::getline(iss, line)) {
        if (auto pos = line.find('='); pos != std::string::npos) {
            std::string key = line.substr(0, pos);
            std::string value = line.substr(pos + 1);
            config[key] = value;
        }
    }
    return config;
}
```

### 2. 高效搜索算法
```cpp
size_t countSubstring(std::string_view text, std::string_view pattern) {
    size_t count = 0;
    size_t pos = 0;
    
    while ((pos = text.find(pattern, pos)) != std::string_view::npos) {
        ++count;
        pos += pattern.length();
    }
    return count;
}
```

## 总结

`std::string` 是现代 C++ 开发中不可或缺的核心组件：
- **安全替代**：完美替代 C 风格字符串，消除缓冲区溢出风险
- **功能丰富**：提供超过 100 个成员函数，覆盖各种操作需求
- **性能优化**：SSO、移动语义等机制保障高效运行
- **现代集成**：完美支持字符串视图、格式化等新特性
- **编码支持**：通过不同变体处理多语言文本

> "在 C++ 中，几乎找不到不使用 `std::string` 的正当理由。它应该是所有文本处理的首选工具。" — Bjarne Stroustrup（C++ 之父）

掌握 `std::string` 的各种特性和最佳实践，是成为高效 C++ 开发者的关键一步。随着 C++20/23 的演进，它将继续成为处理文本数据的核心工具。





## C++格式转化

使用 ` static_cast ` 转化数据类型。

```
int i = 10;
float f = static_cast<float>(i);
```

可以将静态int类型转换为float类型

## C++字符串`std::string`

C++字符串需要`#include<string>`头文件


```
std::string 变量名称{"字符串",开始位置,截取长度};
```

例如

```
std::string a{"123456",2,2};
```

则为34，字符串位置从0开始。

中括号为指定位置，如

```
std::string a{123456};
std::cout << a[2];//输出3
```

string可以使用+=进行附加，如

```
str1 += str2;
```

直接将str2附加到了str1后面，且str2不变。

string可以判断是否相等

### 以文本文件形式输出