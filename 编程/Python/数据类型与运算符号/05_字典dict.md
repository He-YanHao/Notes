# 字典 Dictionary

字典（`dict`）是 Python 中最强大的数据类型之一，用于存储**键值对（key-value pairs）** 的集合。字典中的元素是**无序的**、**可变的**，并且**键必须是唯一的**（不可重复）。

## 核心特点
1. **键值对结构**：每个元素由键（key）和值（value）组成，格式：`{key: value}`
2. **键的要求**：
   - 必须是**不可变类型**（字符串、整数、元组等）
   - 必须**唯一**（重复键会覆盖旧值）
3. **值的要求**：可以是任意数据类型（字符串、数字、列表、甚至其他字典）
4. **无序性**：元素存储顺序≠添加顺序（Python 3.7+ 会保留插入顺序，但逻辑上仍视为无序）
5. **可变性**：创建后可修改、添加或删除键值对



## 字典操作详解

#### 1. 创建字典
```python
# 方法1：花括号 {}
person = {"name": "Alice", "age": 25, "city": "London"}

# 方法2：dict() 构造函数
scores = dict(math=90, physics=85, chemistry=88)

# 方法3：键值对列表
colors = dict([("red", "#FF0000"), ("green", "#00FF00")])

# 空字典
empty_dict = {}
```

#### 2. 访问元素
```python
print(person["name"])  # 输出: Alice

# 使用 get() 避免 KeyError
print(person.get("age"))      # 输出: 25
print(person.get("job"))      # 输出: None（键不存在）
print(person.get("job", "N/A"))  # 输出: N/A（自定义默认值）
```

#### 3. 修改/添加元素
```python
# 修改值
person["age"] = 26

# 添加新键值对
person["email"] = "alice@mail.com"

# 批量更新 update()
person.update({"city": "Paris", "language": "French"})
```

### 删除元素
```python
# 删除指定键
del person["city"]

# pop() 删除并返回值
age = person.pop("age")

# popitem() 删除最后插入的项（Python 3.7+）
last_item = person.popitem()

# 清空字典
person.clear()
```

#### 5. 遍历字典
```python
# 遍历所有键
for key in person.keys():
    print(key)

# 遍历所有值
for value in person.values():
    print(value)

# 遍历键值对（推荐）
for key, value in person.items():
    print(f"{key}: {value}")
```

#### 6. 检查键是否存在
```python
if "name" in person:
    print("Name exists")

if "job" not in person:
    print("Job not found")
```

#### 7. 字典推导式（Dictionary Comprehension）
```python
# 创建数字平方字典
squares = {x: x**2 for x in range(1, 6)}
# 输出: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# 条件筛选
even_squares = {k: v for k, v in squares.items() if v % 2 == 0}
```

---

### 嵌套字典示例
```python
employees = {
    "emp001": {
        "name": "John",
        "skills": ["Python", "SQL"],
        "projects": {"current": "Web App", "pending": "AI Module"}
    },
    "emp002": {
        "name": "Sarah",
        "skills": ["Java", "Docker"]
    }
}

# 访问嵌套数据
print(employees["emp001"]["projects"]["current"])  # 输出: Web App
employees["emp002"]["projects"] = {"completed": "Mobile App"}  # 添加嵌套键
```

### 常用字典方法
| 方法                       | 描述                                         |
| -------------------------- | -------------------------------------------- |
| `keys()`                   | 返回所有键的视图                             |
| `values()`                 | 返回所有值的视图                             |
| `items()`                  | 返回所有键值对的视图                         |
| `copy()`                   | 返回字典的浅拷贝                             |
| `setdefault(key, default)` | 若键存在返回其值，不存在则插入键并返回默认值 |
| `fromkeys(keys, value)`    | 从键序列创建新字典（值相同）                 |

---

### 字典 vs 列表
| 特性         | 字典（dict）                   | 列表（list）         |
| ------------ | ------------------------------ | -------------------- |
| **存储方式** | 键值对                         | 有序序列             |
| **访问元素** | 通过键（快速）                 | 通过索引（O(n)查找） |
| **顺序保证** | Python 3.7+ 保留插入顺序       | 严格保持顺序         |
| **适用场景** | 描述性数据（如JSON）、快速查找 | 有序集合、堆栈/队列  |

> **关键应用场景**：JSON数据处理、配置管理、数据库记录、缓存系统、快速查找表等。

### 内存示意图
```
+---------+-----------------+
|   Key   |      Value      |
+---------+-----------------+
| "name"  |    "Alice"      |
| "age"   |      25         |
| "city"  |    "London"     |
+---------+-----------------+
```
字典通过哈希表实现，能在 O(1) 时间复杂度内完成查找/插入操作，效率极高。