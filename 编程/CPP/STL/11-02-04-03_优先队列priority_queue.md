# C++ STL 优先队列（`priority_queue`）详解

## 1. 概述
`priority_queue` 是 C++ STL 中的**容器适配器**，提供了一种特殊的队列功能：元素按**优先级顺序**出队（最高优先级最先出队），而非传统队列的 FIFO（先进先出）顺序。其底层通常基于**堆数据结构**（二叉堆）实现，使得插入和删除操作的时间复杂度为 `O(log n)`。

**核心特性**：
- **优先级驱动**：元素出队顺序由优先级决定（默认大顶堆，最大值优先）
- **高效操作**：插入 (`push`) 和删除 (`pop`) 时间复杂度均为 `O(log n)`
- **受限访问**：仅能访问队首元素（`top()`），不支持随机访问
- **容器适配器**：基于其他容器（如 `vector` 或 `deque`）封装实现

## 2. 基本用法

### 2.1 头文件与声明
```cpp
#include <queue>

// 默认声明（大顶堆，降序）
priority_queue<int> pq; 

// 完整声明模板
priority_queue<T, Container, Compare> pq;
```
- **`T`**：元素类型（如 `int`, `string` 等）
- **`Container`**：底层容器类型（默认 `vector<T>`，可选 `deque<T>`）
- **`Compare`**：比较函数类型（默认 `less<T>`，即大顶堆）

### 2.2 常用操作
| **操作**  | **功能描述**               | **时间复杂度** |
| --------- | -------------------------- | -------------- |
| `push(x)` | 插入元素 `x` 并排序        | `O(log n)`     |
| `pop()`   | 移除队首（最高优先级）元素 | `O(log n)`     |
| `top()`   | 访问队首元素（只读）       | `O(1)`         |
| `empty()` | 检查队列是否为空           | `O(1)`         |
| `size()`  | 返回队列元素数量           | `O(1)`         |

**示例代码**：
```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    priority_queue<int> pq;  // 大顶堆
    pq.push(30);
    pq.push(10);
    pq.push(50);

    cout << "队首元素: " << pq.top() << endl; // 输出 50
    pq.pop(); // 移除 50
    cout << "新队首: " << pq.top() << endl;   // 输出 30
    return 0;
}
```

## 3. 自定义排序规则

### 3.1 内置比较函数
```cpp
// 小顶堆（升序）
priority_queue<int, vector<int>, greater<int>> minHeap;
```
- `greater<int>` 使小值优先出队

### 3.2 自定义比较规则
#### 方法 1：重载 `operator<`
```cpp
struct Fruit {
    string name;
    int price;
    Fruit(string n, int p) : name(n), price(p) {}
    // 价格低优先级高（小顶堆）
    bool operator<(const Fruit& other) const {
        return price > other.price; // 注意方向！
    }
};

priority_queue<Fruit> fruitQueue; // 使用重载的 <
```

#### 方法 2：自定义仿函数（函数对象）
```cpp
struct CompareFruit {
    bool operator()(const Fruit& a, const Fruit& b) {
        return a.price > b.price; // 小顶堆
    }
};

priority_queue<Fruit, vector<Fruit>, CompareFruit> fruitQueue;
```

#### 方法 3：Lambda 表达式（C++11）
```cpp
auto cmp = [](int a, int b) { return a > b; };
priority_queue<int, vector<int>, decltype(cmp)> customQueue(cmp);
```

> ⚠️ 注意：比较函数返回 `true` 表示 `a` 的优先级**低于** `b`（即 `b` 应更早出队）。

## 4. 应用场景
`priority_queue` 的高效性使其适用于多种算法和系统设计：

1. **任务调度系统**  
   优先级高的任务优先执行（如操作系统进程调度）。

2. **Dijkstra 最短路径算法**  
   维护未访问节点中距离最小的顶点。

3. **Huffman 编码**  
   动态选择频率最小的节点合并。

4. **实时数据处理**  
   处理高优先级数据流（如金融交易系统）。

5. **合并 K 个有序链表**  
   用堆维护每个链表的当前头节点。

## 5. 底层实现与性能
### 5.1 堆结构原理
`priority_queue` 底层通常使用 **`vector` 或 `deque`** 存储数据，并通过**堆算法**维护优先级：
- **插入 (`push`)**：元素添加到末尾，再通过**上浮操作** (`adjust_up`) 恢复堆结构。
- **删除 (`pop`)**：交换首尾元素 → 删除尾部 → 对根节点执行**下沉操作** (`adjust_down`)。

### 5.2 关键源码逻辑
```cpp
template<class T, class Container, class Compare>
class priority_queue {
protected:
    Container c; // 底层容器（默认 vector）
    Compare comp; // 比较函数对象

    // 上浮调整（插入时调用）
    void adjust_up(size_t child) {
        size_t parent = (child - 1) / 2;
        while (child > 0 && comp(c[parent], c[child])) {
            swap(c[parent], c[child]);
            child = parent;
            parent = (child - 1) / 2;
        }
    }

    // 下沉调整（删除时调用）
    void adjust_down(size_t parent) {
        size_t child = parent * 2 + 1;
        while (child < c.size()) {
            if (child + 1 < c.size() && comp(c[child], c[child+1])) 
                child++;
            if (comp(c[parent], c[child])) {
                swap(c[parent], c[child]);
                parent = child;
                child = parent * 2 + 1;
            } else break;
        }
    }
public:
    void push(const T& x) {
        c.push_back(x);
        adjust_up(c.size() - 1);
    }
    void pop() {
        swap(c[0], c.back());
        c.pop_back();
        adjust_down(0);
    }
};
```

## 6. 与相关容器的对比

| **特性**            | `priority_queue`    | `queue`                    | `set`/`multiset`                |
| ------------------- | ------------------- | -------------------------- | ------------------------------- |
| **底层结构**        | 堆（基于 `vector`） | 双端队列 (`deque`)         | 红黑树 (平衡 BST)               |
| **访问方式**        | 仅队首 (`top()`)    | 队首/队尾 (`front`/`back`) | 任意元素 (迭代器)               |
| **元素顺序**        | 按优先级            | FIFO (先进先出)            | 按键排序                        |
| **插入/删除复杂度** | `O(log n)`          | `O(1)`                     | `O(log n)`                      |
| **去重能力**        | ❌ 允许重复元素      | ❌ 允许重复                 | `set` 去重，`multiset` 允许重复 |
| **适用场景**        | 按优先级处理任务    | 缓冲队列、BFS 算法         | 有序存储、快速查找              |

## 7. 总结与最佳实践
**核心优势**：  
- **高效优先级管理**：自动维护元素顺序，简化调度逻辑。  
- **灵活性**：支持自定义比较规则，适配复杂数据类型。  

**使用建议**：  
1. **优先选择 `vector` 作为底层容器**：内存连续，缓存友好（默认已优化）。  
2. **避免频繁创建销毁**：复用队列对象以减少内存分配开销。  
3. **复杂结构传指针**：存储 `std::unique_ptr<T>` 而非大对象，避免拷贝代价。  
4. **小顶堆用 `greater<T>`**：语法简洁且标准库已优化。  

**适用场景总结表**：
| **场景类型**       | **推荐容器**          | **原因**             |
| ------------------ | --------------------- | -------------------- |
| 任务调度           | `priority_queue`      | 按优先级动态处理任务 |
| 广度优先搜索 (BFS) | `queue`               | 严格 FIFO 保证顺序   |
| 数据去重与快速查找 | `set`/`unordered_set` | 基于键值高效检索     |
| 高频任意位置插入   | `list`                | `O(1)` 插入删除      |

`priority_queue` 是处理**优先级驱动问题**的利器，结合堆的高效性和 STL 的泛型设计，可大幅提升算法开发效率。