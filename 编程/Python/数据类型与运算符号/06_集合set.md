# 集合 set

集合是 Python 中一种非常重要的内置数据类型，它提供了一种存储**无序、不重复**元素的容器。

## 集合的基本特性

- **无序性**：集合中的元素没有固定的顺序
- **唯一性**：集合中不会存在重复的元素
- **可变性**：集合本身是可变的，可以添加或删除元素
- **可哈希要求**：集合的元素必须是不可变类型（可哈希的）

## 创建集合

### 使用花括号 `{}`
```python
# 创建有元素的集合
fruits = {"apple", "banana", "orange"}
numbers = {1, 2, 3, 4, 5}

print(fruits)  # {'banana', 'orange', 'apple'} (顺序可能不同)
print(type(fruits))  # <class 'set'>
```

### 使用 `set()` 构造函数
```python
# 从列表创建
my_list = [1, 2, 2, 3, 3, 3]
my_set = set(my_list)
print(my_set)  # {1, 2, 3} (自动去重)

# 从字符串创建（每个字符成为元素）
char_set = set("hello")
print(char_set)  # {'h', 'e', 'l', 'o'} (去重且无序)

# 创建空集合
empty_set = set()
print(empty_set)  # set()
```

**注意**：`{}` 创建的是空字典，不是空集合！

```python
empty_dict = {}    # 空字典
empty_set = set()  # 空集合
print(type(empty_dict))  # <class 'dict'>
print(type(empty_set))   # <class 'set'>
```



## 集合的基本操作

### 添加元素
```python
fruits = {"apple", "banana"}

# add() - 添加单个元素
fruits.add("orange")
print(fruits)  # {'apple', 'banana', 'orange'}

# update() - 添加多个元素（可迭代对象）
fruits.update(["grape", "mango"])
fruits.update(("kiwi", "pineapple"))
print(fruits)  # 添加了 grape, mango, kiwi, pineapple
```

### 删除元素
```python
numbers = {1, 2, 3, 4, 5, 6}

# remove() - 删除指定元素，不存在则报错
numbers.remove(3)
print(numbers)  # {1, 2, 4, 5, 6}

# discard() - 删除指定元素，不存在不报错
numbers.discard(10)  # 不会报错
numbers.discard(4)

# pop() - 随机删除并返回一个元素
popped = numbers.pop()
print(f"删除了 {popped}，剩余 {numbers}")

# clear() - 清空集合
numbers.clear()
print(numbers)  # set()
```

### 查询操作
```python
colors = {"red", "green", "blue"}

# 检查元素是否存在
print("red" in colors)    # True
print("yellow" in colors) # False

# 集合长度
print(len(colors))  # 3
```



## 集合的数学运算

集合支持丰富的数学集合运算：

```python
A = {1, 2, 3, 4, 5}
B = {4, 5, 6, 7, 8}
```

### 并集（Union）
```python
# 方法1：使用 | 运算符
union1 = A | B
print(union1)  # {1, 2, 3, 4, 5, 6, 7, 8}

# 方法2：使用 union() 方法
union2 = A.union(B)
print(union2)  # 同上

# 多个集合的并集
C = {8, 9, 10}
print(A | B | C)  # {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

### 交集（Intersection）
```python
# 方法1：使用 & 运算符
intersection1 = A & B
print(intersection1)  # {4, 5}

# 方法2：使用 intersection() 方法
intersection2 = A.intersection(B)
print(intersection2)  # 同上
```

### 差集（Difference）
```python
# A - B：在A中但不在B中的元素
diff1 = A - B
print(diff1)  # {1, 2, 3}

# B - A：在B中但不在A中的元素
diff2 = B - A
print(diff2)  # {6, 7, 8}

# 使用 difference() 方法
print(A.difference(B))  # {1, 2, 3}
```

### 对称差集（Symmetric Difference）
```python
# 在A或B中，但不同时在A和B中的元素
sym_diff1 = A ^ B
print(sym_diff1)  # {1, 2, 3, 6, 7, 8}

# 使用 symmetric_difference() 方法
sym_diff2 = A.symmetric_difference(B)
print(sym_diff2)  # 同上
```



## 集合关系判断

```python
X = {1, 2, 3}
Y = {1, 2}
Z = {1, 2, 3, 4}
```

### 子集和超集
```python
# 子集判断
print(Y.issubset(X))    # True (Y是X的子集)
print(Y <= X)           # True
print(Y < X)            # True (真子集)

# 超集判断
print(X.issuperset(Y))  # True (X是Y的超集)
print(X >= Y)           # True
print(X > Y)            # True (真超集)
```

### 不相交判断
```python
W = {4, 5}
print(X.isdisjoint(W))  # True (X和W没有共同元素)
print(X.isdisjoint(Y))  # False (X和Y有共同元素)
```



## 集合推导式

类似于列表推导式，集合也支持推导式：

```python
# 创建平方数的集合
squares = {x**2 for x in range(10)}
print(squares)  # {0, 1, 4, 9, 16, 25, 36, 49, 64, 81}

# 带条件的集合推导式
even_squares = {x**2 for x in range(10) if x % 2 == 0}
print(even_squares)  # {0, 4, 16, 36, 64}

# 字符串处理
words = ["hello", "world", "python"]
first_letters = {word[0] for word in words}
print(first_letters)  # {'h', 'w', 'p'}
```



## 不可变集合：frozenset

如果需要不可变的集合（可哈希，可作为字典的键或另一个集合的元素），可以使用 `frozenset`：

```python
# 创建 frozenset
immutable_set = frozenset([1, 2, 3, 4])
print(immutable_set)  # frozenset({1, 2, 3, 4})

# frozenset 是不可变的
# immutable_set.add(5)  # 报错：AttributeError

# 可以作为字典的键
dict_with_frozenset = {immutable_set: "value"}
print(dict_with_frozenset)  # {frozenset({1, 2, 3, 4}): 'value'}

# 也可以作为集合的元素
set_of_sets = {immutable_set}
print(set_of_sets)  # {frozenset({1, 2, 3, 4})}
```



## 集合的常用场景

### 数据去重
```python
# 列表去重（保持顺序需要使用其他方法）
duplicates = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
unique = list(set(duplicates))
print(unique)  # [1, 2, 3, 4] (顺序可能不同)
```

### 成员测试（比列表高效）
```python
# 集合的成员测试是 O(1) 时间复杂度
large_set = set(range(1000000))

# 比列表快很多
if 999999 in large_set:
    print("找到了！")
```

### 查找共同元素
```python
# 两个列表的共同元素
list1 = [1, 2, 3, 4, 5]
list2 = [4, 5, 6, 7, 8]
common = set(list1) & set(list2)
print(common)  # {4, 5}
```



## 集合的方法总结

| 方法                              | 描述                   |
| --------------------------------- | ---------------------- |
| `add(element)`                    | 添加元素               |
| `update(iterable)`                | 添加多个元素           |
| `remove(element)`                 | 删除元素，不存在则报错 |
| `discard(element)`                | 删除元素，不存在不报错 |
| `pop()`                           | 随机删除并返回一个元素 |
| `clear()`                         | 清空集合               |
| `union(other_set)`                | 返回并集               |
| `intersection(other_set)`         | 返回交集               |
| `difference(other_set)`           | 返回差集               |
| `symmetric_difference(other_set)` | 返回对称差集           |
| `issubset(other_set)`             | 判断子集               |
| `issuperset(other_set)`           | 判断超集               |
| `isdisjoint(other_set)`           | 判断是否不相交         |
| `copy()`                          | 返回集合的浅拷贝       |

