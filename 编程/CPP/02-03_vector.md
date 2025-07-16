## 二、`<vector>`：动态数组

### 基本概念

- `std::vector` 是序列容器，提供动态数组功能
- 随机访问元素（O(1) 时间复杂度）
- 在尾部插入/删除高效（分摊O(1)）
- 自动调整大小

### 核心功能

#### 1. 创建与初始化

cpp

```
#include <vector>

std::vector<int> v1;              // 空vector
std::vector<int> v2(5);           // 5个元素，默认值0
std::vector<int> v3(5, 42);       // 5个元素，值都是42
std::vector<int> v4 = {1, 2, 3};  // 初始化列表
std::vector<int> v5(v4);          // 拷贝构造
```

#### 2. 元素访问

cpp

```
v4[1] = 7;          // 随机访问（不检查边界）
v4.at(2) = 9;       // 带边界检查的访问
int first = v4.front(); // 第一个元素
int last = v4.back();   // 最后一个元素
int* data = v4.data();  // 底层数组指针
```

#### 3. 迭代器

cpp

```
// 顺序遍历
for (auto it = v4.begin(); it != v4.end(); ++it) {
    std::cout << *it << " ";
}

// 逆序遍历
for (auto rit = v4.rbegin(); rit != v4.rend(); ++rit) {
    std::cout << *rit << " ";
}

// 基于范围的for循环 (C++11)
for (int num : v4) {
    std::cout << num << " ";
}
```

#### 4. 修改内容

cpp

```
v4.push_back(4);        // 尾部添加元素 {1,7,9,4}
v4.pop_back();          // 移除尾部元素 {1,7,9}
v4.insert(v4.begin() + 1, 5); // 位置1插入5 {1,5,7,9}
v4.erase(v4.begin() + 2);    // 移除位置2元素 {1,5,9}
v4.resize(5);           // 调整大小为5，新增元素为0 {1,5,9,0,0}
v4.clear();             // 清空所有元素
```

#### 5. 容量管理

cpp

```
v4.size();         // 元素数量
v4.capacity();     // 当前分配的存储空间
v4.empty();        // 是否为空
v4.reserve(100);   // 预留空间，避免多次重分配
v4.shrink_to_fit(); // 减少容量以匹配大小
```

### 性能特点

| 操作                | 时间复杂度 |
| :------------------ | :--------- |
| 随机访问            | O(1)       |
| 尾部插入/删除       | O(1) 分摊  |
| 头部或中间插入/删除 | O(n)       |
| 搜索                | O(n)       |